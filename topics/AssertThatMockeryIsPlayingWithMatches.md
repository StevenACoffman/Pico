#  AssertThat, Hamcrest Collections matchers, Hamcrest Date matchers, and ArgumentCaptor

## AssertThat - +[Read This ](https://objectpartners.com/2013/09/18/the-benefits-of-using-assertthat-over-other-assert-methods-in-unit-tests/)+


## Hamcrest Collections matchers - [From Baeldung's Cookbook](http://www.baeldung.com/hamcrest-collections-arrays)

* check if single element is in a collection
```
List<String> collection = Lists.newArrayList("ab", "cd", "ef");
assertThat(collection, hasItem("cd"));
assertThat(collection, not(hasItem("zz")));
```
* check if multiple elements are in a collection
```
List<String> collection = Lists.newArrayList("ab", "cd", "ef");
assertThat(collection, hasItems("cd", "ef"));
```
* check all elements in a collection – with strict order
```
List<String> collection = Lists.newArrayList("ab", "cd", "ef");
assertThat(collection, contains("ab", "cd", "ef"));
```
* check all elements in a collection – with any order
```
List<String> collection = Lists.newArrayList("ab", "cd", "ef");
assertThat(collection, containsInAnyOrder("cd", "ab", "ef"));
```
* check if collection is empty
```
List<String> collection = Lists.newArrayList();
assertThat(collection, empty());
```
* check if array is empty
```
String[] array = new String[] { "ab" };
assertThat(array, not(emptyArray()));
```
* check if Map is empty
```
Map<String, String> collection = Maps.newHashMap();
assertThat(collection, equalTo(Collections.EMPTY_MAP));
```
* check size of a collection
```
List<String> collection = Lists.newArrayList("ab", "cd", "ef");
assertThat(collection, hasSize(3));
```
* checking size of an iterable
```
Iterable<String> collection = Lists.newArrayList("ab", "cd", "ef");
assertThat(collection, Matchers.<String> iterableWithSize(3));
```
* check condition on every item
```
List<Integer> collection = Lists.newArrayList(15, 20, 25, 30);
assertThat(collection, everyItem(greaterThan(10)));
```

## Hamcrest Date matchers - [Read This ]( https://github.com/eXparity/hamcrest-date)
* Look ma! No pain
```
Date today = new Date(); myBirthday = new Date();
assertThat(today, within(1, DAY, myBirthday));
```
* A factory is provided for commonly used moments in time. For Example:
```
Date myBirthday = new Date();
assertThat(myBirthday, DateMatchers.sameDay(Moments.today()));
```

## ArgumentCaptor

Mockito verifies argument values in natural java style: by using an equals() method. This is also the recommended way of matching arguments because it makes tests clean & simple. In some situations though, it is helpful to assert on certain arguments after the actual verification. For example:

```
	ArgumentCaptor<Person> argument = ArgumentCaptor.forClass(Person.class);
	verify(mock).doSomething(argument.capture());
	assertEquals("John", argument.getValue().getName());
```

{warning:title=Use with verification but not stubbing|icon=true}
It is recommended to use ArgumentCaptor with verification but not with stubbing. Using ArgumentCaptor with stubbing may decrease test readability because captor is created outside of assert (aka verify or 'then') block. Also it may reduce defect localization because if stubbed method was not called then no argument is captured.
{warning}

In a way ArgumentCaptor is related to custom argument matchers (see javadoc for ArgumentMatcher class). Both techniques can be used for making sure certain arguments where passed to mocks. However, ArgumentCaptor may be a better fit if:

*    custom argument matcher is not likely to be reused
*    you just need it to assert on argument values to complete verification

Custom argument matchers via ArgumentMatcher are usually better for stubbing.
