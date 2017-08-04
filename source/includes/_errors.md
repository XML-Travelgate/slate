# Errors

Error Code | Meaning
---------- | -------
400 | Bad Request -- YRequest does not match the specification.
401 | Unauthorized -- Your API key is wrong.
403 | Forbidden -- User is not authorized.
404 | Not Found -- The resource could not be found.
408 | RequestTimeout -- Timeout (tipically on provider’s side).
409 | Conflict -- Conflict with the configuration (e.g. FDS returns SecurePayment and referencing a non secure gateway).
410 | Gone -- The resource has been removed from our servers.
500 | Internal Server Error -- We had a problem with our server. Try again later.
1000 | Undefined -- Error could not be traced! ESO QUE ES???????????