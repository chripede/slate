# Errors

We use the following error codes:

Error Code | Meaning
---------- | -------
400 | Bad Request -- Your request could not be parsed.
401 | Unauthorized -- Your API key is wrong.
403 | Forbidden -- Request could not be completed. Check that you have available API units.
429 | Too Many Requests -- You have hit your rate limit. Slow down.

<aside class="notice">
Most of the time you will find a detailed explanation in the response body.
</aside>
