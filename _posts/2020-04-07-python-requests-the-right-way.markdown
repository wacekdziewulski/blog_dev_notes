---
layout: post
title:  "Python: requests - the right way"
date:   2020-04-07 17:00:00 +0100
categories: python
---

The `requests` library is a very popular one for Python, when dealing with sending HTTP requests. No wonder, it's simple and yet gives a lot of options when You really need them. There are however some popular misusages, that lead into unexpected trouble. Believe me, I've been there.

## The initial way

This is what we can find in tutorials and what generally works:

{% highlight python %}
#!/usr/bin/python3

import requests

def getRandomJoke():
    r = requests.get('https://official-joke-api.appspot.com/jokes/programming/random')
    if (r.status_code == 200):
        joke = r.json()[0]
        return joke["setup"] + "\n" + joke["punchline"]
    else:
        return ""

print(getRandomJoke())
{% endhighlight %}

This oversimplified example just works. First we send a GET request synchronously and then we check the status\_code. What could possibly go wrong?
_Notice that I won't be getting into the details such as headers, payload etc., because it's not the point of this article._

# DNS name resolution

Let's say, that for some reason, the library is not able to resolve the hostname. We'll get a long stacktrace, but basically things should move forward. Unfortunately I stumbled upon a problem, when the server was not available and I still tried to access r.status\_code, which again - hung.

# Target is not responding

It's unusual nowadays that a certain service is down, but that surely happens from time to time. We expect the library to use a regular TCP timeout and just exit with an error. Nope, not this time. The function will hang indefinetely as well, because the default timeout is 'unlimited'. Ouch.

# HTTP status code

For some of the calls, we may need to be strict when checking the HTTP status code and differentiate between 204 and 200, or 403 and 404 to act accordingly - that makes sense. Other times however it's enough that the reponse code is in the 2xx,3xx range to mark that the request succeeded and raise an exception otherwise. This can easily be achieved with the requests library as well.

## The right way

Let's tweak the code a little bit, so that it's production ready now. I will follow with the same example, but add some more code and meaningful comments.

{% highlight python %}
#!/usr/bin/python3

import requests
from requests.exceptions import Timeout, ConnectionError, HTTPError      # we import some important exceptions from the library to catch them later on

def getRandomJoke():
    try:
        # by default the timeout is indefinite, but it should actually always be set to some integer value!
        DEFAULT_TIMEOUT_IN_SECONDS = 5
        URL = "https://official-joke-api.appspot.com/jokes/programming/random"
        # we've added the timeout to the call now, so the call will proceed even on no response from the server. Otherwise it would hang
        r = requests.get(URL, timeout=DEFAULT_TIMEOUT_IN_SECONDS)
        # raise an HTTPError exception if the status code is 4xx or 5xx - remember to catch it!
        # It will be a no-op (and passthrough) if the status is 2xx or 3xx though!
        r.raise_for_status()

        joke = r.json()[0]
        return joke["setup"] + "\n" + joke["punchline"]

    except Timeout:
        # Timeout is raised if the call exceeded the timeout=DEFAULT_TIMEOUT_IN_SECONDS value set in the request
        print("Connection timed out. Try again later.")
        return ''
    except ConnectionError as e:
        # ConnectionError indicates that there was some network problem - e.g. hostname was not resolved correctly
        # more details are found in the exception, so we can print that
        print(f"ConnectionError occured: {str(e)}.")
        return ''
    except HTTPError as e:
        # This exception will be raised only if the status code was 4xx or 5xx and raise_for_status() was called.
        # The status code is accessible in the exception.response object not from the original request!
        status_code = e.response.status_code
        if status_code == 403:
            print(f"The access to resource under: {URL} is forbidden.")
        elif status_code == 404:
            print(f"The resource under: {URL} was not found.")
        else:
            print(f"Server returned other HTTP Error: {status_code}")
        return ''
    except Exception as e:
        # It's wise to catch a generic exception anyway and not make any more calls to the 'r' request object if it failed!
        print(f"An exception of type {type(e).__name__} occurred.")
        return ''

print(getRandomJoke())
{% endhighlight %}

## Summary

* When using requests, always set a timeout value and catch it in an exception handler. Also a retry of the call makes sense in such a case.
* To simplify HTTP status handling, You can call `raise_for_status()` and handle it in the `HTTPError` exception - it's cleaner.
* A `ConnectionError` should be caught in case of network related problems.
* Catching a generic `Exception` makes also sense in case there is an other exception raised in the block or some other problem.
* If any exception was raised, try not to make any calls to the original requests object (`r` in my example)

## References

* [Requests library][requests]
* [Requests - timeout][requests-timeouts]

[requests]: https://requests.readthedocs.io/en/master/
[requests-timeouts]: https://requests.readthedocs.io/en/master/user/quickstart/#timeouts
