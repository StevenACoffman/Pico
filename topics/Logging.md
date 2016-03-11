# Why Logging? (compiled by John Schultz)

## Often, it's all we've got.
When you find a bug during development, you typically run a debugger trying to track down the potential cause. Now imagine for a while that you can’t use a debugger. For example, because the bug manifested itself on a customer environment few days ago and everything you have is logs. Would you be able to find anything in them?

 * [10 Tips for Proper Application Logging](http://www.javacodegeeks.com/2011/01/10-tips-proper-application-logging.html)
* If we do it right, we can start to ask — and answer — questions that wouldn't even be worth thinking about otherwise.
  * What else was happening when something went wrong?
  * How long is an important/frequently-used function\] taking?
  * Where is the bottleneck?
  * Does anyone actually use \[SHARE:app\]? Which features do they use?


## A Case Study: The Mysterious Login ID

The issue:
- User help request (email [Re: Dr. Zimmerman|^RE- Dr. Zimmerman.eml])
- Secondary user (at oakwood.org) is denied access to our app
- Primary user (at UMMS) controls secondary user access and doesn't understand why secondary user would be denied

What info do we have to go on?
- We know how users are authorized
- We can see what our primary user thinks should be this user's ID (in our DB)
- We can test user authorization (we think) using altId
- This email contains an email address that is supposed to be used as a friend account ID
- The user can't log in

What info would actually help us?
- What IDs have been rejected, resulting in the “Your ID and password were accepted but you do not have access to this application” message.

Turns out we have that information, but we're just throwing it away!
```
return rejectAuthenticatedUser(mapping, request, Constants.NO_ACCESS_ERROR_MESSAGE);
```

It took a full week of calendar time to solve this simple problem, when this would have shown us the problem in the first 5 minutes:
```
// spoofingText captures altId information, if applicable
String spoofingText = "";
if(userId != null && !request.getRemoteUser().equals(userId)) {
	spoofingText = " (as " + userId + ")";
}
LOGGER.info("Access denied for user '" + request.getRemoteUser() + "'" + spoofingText + ".");
return rejectAuthenticatedUser(mapping, request, Constants.NO_ACCESS_ERROR_MESSAGE);
```

### How Do We Log?

As in: how does it work, _and_ how should we set it up (how do _we_ log?)?

#### How does a Logging Framework work?

http://en.wikipedia.org/wiki/Java_logging_framework

####  How do we log?

*log4j.properties*
* Define "loggers"
  * There is always a "rootLogger," which is at the top of the parent/child hierarchy for all loggers
  * An "ancestor"/"descendant" relationship exists and is based on the names of loggers (using dot-separated names; suspiciously/conveniently similar to Java package naming)
  * Each logger needs an appender, or its log messages will not be saved anywhere
  * A logger can (of course) have multiple appenders (e.g., a RollingFileAppender and an SMTPAppender)
  * Appenders can be declared in the logger definition, inherited from an ancestor logger, or _both_ (if a logger inherits an appender *and* the logger's definition specifies an appender, both of them will be used — even if they are the same appender). {warning}If a logger inherits an appender *and* the logger's definition specifies an appender, both of them will be used — even if they are the same appender.

* Define "appenders"
  * Take formatted log messages and get them where you want them to go
  * Often requires specifying various properties of the appender, depending what type of appender it is
  * FileAppenders should have a File specified, SMTPAppenders an SMTPHost, plus To/From/Subject (email properties)
  * If you prefer a certain layout for log statements, you should provide that
  * Our de facto standard is two-parted since [org.apache.log4j.PatternLayout](http://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/PatternLayout.html) calls for a pattern:
```
log4j.appender.EMAIL.layout=org.apache.log4j.PatternLayout
log4j.appender.EMAIL.layout.ConversionPattern=%d %-5p - [%c{2}.%M] %m\n
```
  * *Use appropriate appenders for appropriate environments*
  * I think Sashi has done something to disable Console logging on our servers, but that doesn't mean we should be defining ConsoleAppenders in test/log4j.properties and prod/log4j.properties.
  * Are you running DevNullSMTP to capture error emails when running Resin locally? If not, an SMTPAppender defined in dev/log4j.properties won't do anything except fill your console with extra errors generated during logging — distracting from the root cause of the error!

### *declaring loggers*
* Okay: it's a tiny bit annoying, but declare a logger in every class that does logging. The logger should know its name, that name should match the class name, and your logging output should make sense.
* Don't use static loggers
* For goodness' sake, declare loggers using the class name of the class in which they're declared.  *DO NOT DO THIS*:
```
public class SrschAuthorizationDBA implements java.io.Serializable
{
    private static Logger LOGGER = Logger.getLogger(StudentSrschDBA.class);
```

### *logging statements*
In Log4J, many logging statements (especially INFO and DEBUG statements) are fairly straightforward.  If we want to log a simple message, we just create a String as we normally would for any other use in Java, but provide that String as the parameter of a method call to one of the logging methods:
```
Logger log = Logger.getLogger(ThisClass.class);
...
log.info(request.getRemoteUser() + " submitted an evaluation of a student (" + student.getUniqname() + " ).");
```

There are some drawbacks to this particular method of log message creation and especially this line of code (this is an inefficient way to create a single String, the method calls in the middle of the line create some risk of a NullPointerException, etc.), so you shouldn't necessarily copy it straight from here... but until you're able to state definitively (with the help of logs, perhaps?) that it's causing a noticeable or meaningful problem, don't overthink it.

There is one type of logging that is extremely common, but not as straightforward: *error/exception logging*.  It's arguably the most important use case for logging, and it can make debugging/troubleshooting so much easier, but it's easy to do wrong.  I've taken the following example from [10 Tips for Proper Application Logging|http://www.javacodegeeks.com/2011/01/10-tips-proper-application-logging.html] section 9 ("Log exceptions properly").  Can you tell which of these lines of error-logging  code is/are done right, and which is/are done wrong (and why)?
```
try {
    Integer x = null;
    ++x;
} catch (Exception e) {
    log.error(e);        //A
    log.error(e, e);        //B
    log.error("" + e);        //C
    log.error(e.toString());        //D
    log.error(e.getMessage());        //E
    log.error(null, e);        //F
    log.error("", e);        //G
    log.error("Error reading configuration file: " + e);        //J
    log.error("Error reading configuration file: " + e.getMessage());        //K
    log.error("Error reading configuration file", e);        //L
}
```
#### *NO PEEKING*
Okay, here's the answer: only line "L" is really done right; "B", "F", and "G" are debatably okay because they're almost as useful since you'll get to see a stacktrace and that should really narrow down the problem space, but each of them is confusing or unhelpful in at least one way that "L" takes care of.  I skipped lines H and I (they're in the linked document) because they were supposed to demonstrate something that doesn't really apply to Log4J (but does apply to other Java logging frameworks/API, like SLF4J). For a little more detail about what's wrong with the rest of these statements, see the relevant section of the linked page.

### What Should We Log?

That's the big question, isn't it?  These are just my ideas, so don't treat them as gospel, but I don't think any of them would hurt much if implemented.

#### Log information about access
As the case study above showed, it can be an unnecessarily difficult problem when users can't log in to an app in the first place.  If the app has a good way of knowing when it's rejecting a user (this can be harder with a standard AppAccess filter, but with custom authorizers and especially single-point entry controllers it's very likely to be available), log that!  It can be bad enough to trace back and check what logic the app went through to determine a user needed to be rejected; at the very least, we can narrow down the userid we're using to trace that.
On the other hand, sometimes there are multiple roles for users of a single app, and sometimes a single user might be qualified to use the app for more than one of those roles.  Therefore it's probably a good idea (which many of our apps already implement) to log some information about a new user logging on — including the role that's saved for them in the application's Session.

#### Log information about actions users take that can change the state of the system
If a user has a problem doing something, it's good to know what they tried to do.  Some users are easy to reach out to/get more info from; some are not.  Some can't provide accurate information even when they do try to be helpful — possibly through no fault of their own.  Logs are our answer.

A solid way to do this would be: once a user has attempted to complete an action (all user input is received and the success of the action lies with the app), log all the input available — to a reasonable extent.  It may be too much to log the entire text of an evaluation form, especially if it includes open-ended text answers.  But it should be possible to log the differentiating aspects of the submission/request: "\[SHARE:user\] submitted an evaluation of \[SHARE:student uniqname\] for \[SHARE:course identifier\]." "\[SHARE:user\] scored Exam #\[SHARE:exam ID\]."  A reasonable implementation of this might be
- Determine the entry point (Controller RequestMapping, probably) for every user action that changes state
- Add a logging statement for each entry point, logging as many of the parameters as is reasonable (a good way to think of this might be: log the information you would see in a REST request URL, not necessarily the info you'd find in the REST request's payload)
- If the app returns something meaningful but small enough to fit in the log file without completely overtaking the file, it may be worthwhile to log that, too
- Even if there is no succinct or meaningful return value, it could be useful to log something just before the request is completed and the response returned; assuming some time/date info is included in the logging layout pattern, the return time could be analyzed in conjunction with the request time to determine if a request is taking too long.

It's a little bit negative to put it this way, but: logging user actions that can change the state of the system/database/world gives you a defense against erroneous or incomplete complaints.  If a user claims that something was saved and then disappeared, _and you have no logging of user Delete actions_, you can scramble for an explanation, you can pore over all the code in the app to see if there's some code that would have made that information go away — all the while doubting the user's innocence and veracity, you can ask your coworkers if anybody touched that data... and in the end, you might not end up with anything satisfying at all.  But if a user claims that something was saved and then disappeared, _and you have logged every user Save, Delete, and Update action_, you'll be able to tell exactly when that data was last saved, by whom, and whose change they were overwriting.  It might leave the blame, and responsibility for a fix, squarely at your feet... but at least you'll know you deserve it.

#### Log errors. Log them correctly.
This is covered above, but: pay attention to the methods the logging framework provides for logging exceptions along with a little bit of information about the exceptions.  These are really easy to take advantage of, and they are so useful when used correctly.

I disagree with some of the advice about logging errors in the "10 Tips" page I linked above.  The author is correct that *something* will eventually catch — and log — an Exception, if our code catches it, logs it, then rethrows it.  But in many cases that "something" is the top level of the webapp, and if it catches an exception it's probably not going to be pretty.  Users will see an unattractive and unsettling "Error" page, with information they don't need.  In a good number of our webapps, the code that catches uncaught Exceptions is actually broken, and we have lost a lot of valuable troubleshooting information because of it.  Be thoughtful when catching and logging exceptions, but don't just give up on the idea because in some cases it might provide duplicate information.  Duplicate information is normally infinitely better than no information at all.


#### Recommended Resources

If you want more info about logging in Java in general, or if you have specific questions, I would recommend the following pages, which I used pretty extensively for writing this:

- The aforementioned and linked [10 Tips for Proper Application Logging](http://www.javacodegeeks.com/2011/01/10-tips-proper-application-logging.html) — there are 8 tips I don't think I even got into.
- The actual [Manual to log4j 1.2](http://logging.apache.org/log4j/1.2/manual.html) — covers a lot of details about how loggers and appenders work, and how to set them up and use them.
