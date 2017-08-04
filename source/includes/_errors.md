# Errors

<aside class="notice">This error section is stored in a separate file in `includes/_errors.md`. Slate allows you to optionally separate out your docs into many files...just save them to the `includes` folder and add them to the top of your `index.md`'s frontmatter. Files are included in the order listed.</aside>

The Kittn API uses the following error codes:


Error Code | Meaning
---------- | -------
400 | Bad Request -- YRequest does not match the specification.
401 | Unauthorized -- Your API key is wrong.
403 | Forbidden -- User is not authorized.
404 | Not Found -- The specified kitten could not be found.
405 | Method Not Allowed -- You tried to access a kitten with an invalid method.
406 | Not Acceptable -- You requested a format that isn't json.
410 | Gone -- The kitten requested has been removed from our servers.
418 | I'm a teapot.
429 | Too Many Requests -- You're requesting too many kittens! Slow down!
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Unavailable -- We're temporarily offline for maintenance. Please try again later.

    <td>400</td>
    <td>Bad_Request</td>
    <td></td>
    <td>401</td>
    <td>Unauthorized</td>
    <td>U.</td>
    <td>403</td>
    <td>Forbidden</td>
    <td>Invalid user.</td>
    <td>404</td>
    <td>Not_Found</td>
    <td>Something requested was not found.</td>
    <td>408</td>
    <td>Request_Timeout</td>
    <td>Timeout (tipically on provider’s side).</td>
    <td>409</td>
    <td>Conflict</td>
    <td>Conflict with the configuration (e.g. FDS returns SecurePayment and referencing a non secure gateway).</td>
    <td>410</td>
    <td>Gone</td>
    <td>Resource not yet avaliable.</td>
    <td>500</td>
    <td>Internal_Server_Error</td>
    <td>System internal error (contact SWAPPT team).</td>
    <td>1000</td>
    <td>Undefined</td>
    <td>Error could not be traced.</td>