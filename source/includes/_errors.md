# Errors

<aside class="notice">
Currently, we only support the error code 400. Support for other error codes will be soon added.
</aside>

The Blip API uses the following error codes:

Error Code | Meaning
---------- | -------
400 | Bad Request -- An error occured, check `response.error` for a string explanation of the reason the error occured.
