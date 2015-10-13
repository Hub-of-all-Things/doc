# Errors

The HAT API uses the standard HTTP error codes:

Error Code | Meaning
---------- | -------
400 | Bad Request -- Your request sucks
401 | Unauthorized -- Your API key is wrong
403 | Forbidden -- The action you requested is not allowed for you: it belongs to somebody else or is too private
404 | Not Found -- The specified data could not be found
406 | Not Acceptable -- You requested a format that isn't json
410 | Gone -- The item requested has been removed from our servers
429 | Too Many Requests -- You're asking for too much! Slow down!
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Unavailable -- We're temporarially offline for maintanance. Please try again later.

Response body may optionally contain a more detailed explanation of the error