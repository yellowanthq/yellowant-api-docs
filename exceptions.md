# Exceptions {#errors}

The YellowAnt API uses the following error codes:

| Error Code | Meaning |
| :--- | :--- |
| 400 | Bad Request – Your request to the url is incorrect |
| 401 | Unauthorized – Your API key is wrong |
| 403 | Forbidden – It is forbidden to access this page |
| 404 | Not Found – The specified url could not be found |
| 405 | Method Not Allowed – You tried to access the url with an invalid method |
| 406 | Not Acceptable – You requested a format that isn’t json |
| 410 | Gone – The url requested has been removed from our servers |
| 429 | Too Many Requests – You are sending too many requests |
| 500 | Internal Server Error – We had a problem with our server. Try again later. |
| 502 | Bad Gateway – We had a problem with our server. Try again later. |
| 503 | Service Unavailable – We’re temporarily offline for maintenance. Please try again later. |
| 504 | Gateway Timeout – The YellowAnt servers are up, but the request could not be processed |



