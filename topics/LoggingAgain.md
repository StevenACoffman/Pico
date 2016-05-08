# Logging
### Archie Cowan says good error logging has the following things

* error data like stack traces
* what host was called
* what service is that host part of
* what endpoint was called
* what request URL / post data input was sent
* how long did we wait for a response
* how long did it take to connect
* what are our timeouts set to now
* what were the response codes if anything was received
* access information (added by Steve)

Keep all the stuff in the log message under 8kb or it will be truncated in Python/Django. This means that REST request or large form POST payload might not fit.

What about:

* Feature Flags/DipSwitches?
* Any other relevant internal state

The error logging advice is predicated on logging information about actions users/systems take that can change the state of the system.
