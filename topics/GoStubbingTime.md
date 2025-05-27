### Copied from [Stubbing Time.Now() in golang](https://labs.yulrizka.com/en/stubbing-time-dot-now-in-golang/)

Stubbing Time.Now() in golang
=============================

Ahmy Yulrizka

2014-10-27

Page content

*   [Passing the time instance](#passing-the-time-instance)
*   [Passing a function that generate time](#passing-a-function-that-generate-time)
*   [Abstract the time generator as an interface](#abstract-the-time-generator-as-an-interface)
*   [Package level time generator](#package-level-time-generator)
*   [Embed time generator in struct](#embed-time-generator-in-struct)
*   [Better way to write test function with time](#better-way-to-write-test-function-with-time)

Sometimes it’s really hard to test functionality that involve with system time. Especially when we want to test the function with a specific time. For example testing whether today is end of month or test 2 different behavior at a different time

Below we look into different ways we can mock or stub the time. Each with it’s own advantages and disadvantages.

Passing the time instance
-------------------------

     1func CheckEndOfMonth(now time.Time) {
     2    // business process
     3}
     4
     5func main() {
     6	CheckEndOfMonth(time.Now())
     7}
     8
     9// for test
    10func TestCheckEndOfMonth(t *testing.T) {
    11	CheckEndOfMonth(time.Date(2019, 1, 1, 0, 0, 0, 0, time.UTC))
    12}
    

This way you can either pass `time.Now()` in your main pacakge or call it with something like `time.Date(t.Year(), t.Month(), 1, 0, 0, 0, 0, time.UTC)` in your test.

Advantages:

*   Simple, no extra generator necessary
*   Easy to test, no need to wire mock function or struct

Disadvantages:

*   Caller need to pass instance of everywhere

Passing a function that generate time
-------------------------------------

     1func CheckEndOfMonth(now func() time.Time) {
     2	// ...
     3	x := now() // runs at this moment, not at the caller time
     4}
     5
     6func main() {
     7	CheckEndOfMonth(time.Now) // instead of time.Now()
     8}
     9
    10// for test
    11func TestCheckEndOfMonth(t *testing.T) {
    12	loc := time.UTC // closure can be used if necessary
    13	timeFn := func() time.Time {
    14		return time.Date(2019, 1, 1, 0, 0, 0, 0, loc)
    15	}
    16	CheckEndOfMonth(timeFn)
    17}
    

Advantages:

*   This method allows you to have more control over how and when the time is generated. For example the time is generated inside the `CheckEndOfMonth` not at the _caller_.
*   Allows the mock function to use closure inside the function

Disadvantages:

*   Caller needs to pass function

Abstract the time generator as an interface
-------------------------------------------

     1type Clock interface {
     2	Now() time.Time
     3}
     4
     5type realClock struct {}
     6func (realClock) Now() time.Time { return time.Now() }
     7
     8func CheckEndOfMonth(clock Clock) {
     9	// ...
    10	x := clock.Now()
    11}
    12
    13func main() {
    14	CheckEndOfMonth(realClock{})
    15}
    

on the test code

     1type mockClock struct {
     2    t time.Time
     3}
     4func NewMockClock(t time.Time) mockClock {
     5    return mocktime{t}
     6}
     7
     8func (m mockClock) Now() time.Time {
     9	return m.t
    10}
    11
    12func TestCheckEndOfMonth(t *testing.T) {
    13	mc := NewMockClock(time.Date(2019, 1, 1, 0, 0, 0, 0, time.UTC))
    14	CheckEndOfMonth(mc)
    15}
    

Advantages:

*   Control the generator method
*   Interface can be defined small with only needed functions
*   Easily switch to different implementation

Disadvantages:

*   Caller need to pass instance that implements the interface

Package level time generator
----------------------------

Previous examples require you to pass in either concrete instance or a function on the caller.

Another approach you can use is to create a package level function to generate current time. You can change the implementation during test.

     1package main
     2
     3import "time"
     4
     5type nowFuncT func() time.Time
     6
     7var nowFunc nowFuncT
     8
     9func init() {
    10    resetClockImplementation()
    11}
    12
    13func resetClockImplementation() {
    14    nowFunc = func() time.Time {
    15        return time.Now()
    16    }
    17}
    18
    19// function to return current time stamp in UTC
    20func now() time.Time {
    21    return nowFunc().UTC()
    22}
    23
    24func CheckEndOfMonth() {
    25	// ...
    26	x := now()
    27}
    

now in your test you could do something like this:

     1func TestCheckEndOfMonth(t *testing.T) {
     2    // change implementation of clock in the beginning of the test
     3    nowFunc = func() time.Time {
     4        return time.Date(2000, 12, 15, 17, 8, 00, 0, time.UTC)
     5    }
     6
     7    // after finish with the test, reset the time implementation
     8    defer resetClockImplementation()
     9    
    10    CheckEndOfMonth()
    11    //...
    12}
    

Advantages:

*   No need to pass instance of time or generator

Disadvantages:

*   Caller need to pass instance
*   Global variable
*   Not thread safe
*   Need to remember to call `resetClockImplementation`

Embed time generator in struct
------------------------------

If you are lucky enough to work in a _struct_, you can do this

     1// TimeValidator contains business logic to CheckEndOfMonth
     2type TimeValidator struct {
     3	// .. your fields
     4	clock func() time.Time
     5}
     6
     7func (t TimeValidator) CheckEndOfMonth()  {
     8	x := t.now()
     9	// ...
    10}
    11
    12// now is time generator that by default fall back to standard library
    13func (t TimeValidator) now() time.Time  {
    14	if t.clock == nil {
    15		return time.Now() // default implementation which fall back to standard library
    16	}
    17
    18	return t.clock()
    19}
    20
    21func main() {
    22	tv := TimeValidator{}
    23	tv.CheckEndOfMonth()
    24}
    

And the test can be

    1func TestCheckEndOfMonth(t *testing.T) {
    2	tv := TimeValidator{
    3		clock: func() time.Time {
    4			return time.Date(2000, 12, 15, 17, 8, 00, 0, time.UTC)
    5	}}
    6	tv.CheckEndOfMonth()
    7}
    

I like this method because you don’t have to pass instance of time or function all over the place but still have it easily create an arbitrary time mock/stub

**Make this pattern reusable**  
If you do this more often, you can also easily reuse the functionality into other struct by creating embeddable

     1// TimeMock embeddable structure that provides ability to inject time to the parent struct
     2type TimeMock struct {
     3	NowFn func() time.Time
     4
     5	// can be extended to mock other time function
     6	AfterFn func() <-chan time.Time
     7}
     8
     9func (t TimeMock) Now() time.Time  {
    10	if t.NowFn == nil {
    11		return time.Now() // default implementation
    12	}
    13
    14	return t.NowFn()
    15}
    16
    17// Use in the business logic
    18
    19// TimeValidator contains business logic that uses time.Now
    20type TimeValidator struct {
    21	clock TimeMock 
    22}
    23
    24func (t TimeValidator) CheckEndOfMonth()  {
    25	x := t.clock.Now()
    26	// ...
    27}
    28
    29func main () {
    30    tv := TimeValidator{} // empty clock field gives implementation with standard lib
    31    tv.CheckEndOfMonth()
    32}
    

in test

    1func TestCheckEndOfMonth(t *testing.T) {
    2	tv := TimeValidator{
    3		clock: TimeMock{
    4			NowFn: func() time.Time {
    5				return time.Date(2000, 12, 15, 17, 8, 00, 0, time.UTC)	
    6		},
    7	}}
    8	tv.CheckEndOfMonth()
    9}
    

Advantages:

*   No need to pass instance of time or generator
*   Fall back to default standard library time implementation
*   Easily extend `TimeMock` by adding more function that is needed

Disadvantages:

*   The caller needs to be a struct

Better way to write test function with time
-------------------------------------------

Function with time is usually hard to test because it dependency with on _time_ package.

A different way to test is to separate the function that generate the time with the function that process the time value. For example

    1func CheckEndOfMonth()  {
    2	now :=  time.Now()
    3	
    4	d := endOfMonth(now)
    5	if now == d {
    6		// do some business logic
    7	} 
    8}
    

Instead of testing that function, extract the logic of processing time

     1func CheckEndOfMonth()  {
     2	now :=  time.Now()
     3
     4	processEndOfMonth(now)
     5}
     6
     7func processEndOfMonth(t time.Time) {
     8	d := endOfMonth(t)
     9	if t == d {
    10		// do some business logic
    11	}
    12}
    

this way you don’t test the `CheckEndOfMonth()` but `processEndOfMonth`. This way you can easily mock time without the need to do some wiring.
