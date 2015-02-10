---
title: API
---
* TOC
{:toc}

## Overview

This describes the resources that make up the official RacketLogger API. If you have any problems or requests please contact
support.

## Current Version

By default, all requests receive the latest version of the API. We will implement versioning at some point in the future.

## Schema

All API access is over HTTP (no https for now), and accessed from the `api.racketlogger.com`
domain. All data is sent and received as JSON.

<pre class="terminal">
$ curl -i http://api.racketlogger.com/rackets

HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Connection: keep-alive
Status: 200 OK
Content-Length: 999
Cache-Control: max-age=0, private, must-revalidate
X-Content-Type-Options: nosniff

[ ... ]
</pre>

All timestamps are returned in ISO 8601 format:

    YYYY-MM-DDTHH:MM:SSZ

### Summary Representations

When you fetch a list of resources, the response includes a _subset_ of the
attributes for that resource. This is the "summary" representation of the
resource. (Some attributes are computationally expensive for the API to provide.
For performance reasons, the summary representation excludes those attributes.
To obtain those attributes, fetch the "detailed" representation.)

**Example**: When you get a list of rackets, you get the summary
representation of each racket. Here, we fetch the list of rackets owned
by the "rafa" user:

    GET /users/rafa/rackets

### Detailed Representations

When you fetch an individual resource, the response typically includes _all_
attributes for that resource. This is the "detailed" representation of the
resource. (However, authorization can influence the amount of detail
included in the representation.)

**Example**: When you get an individual's rackets, you get the detailed
representation of the repository.

    GET /racket/xyz

The documentation provides an example response for each API method. The example
response illustrates all attributes that are returned by that method.

## Parameters

Many API methods take optional parameters. For GET requests, any parameters not
specified as a segment in the path can be passed as an HTTP query string
parameter:

For POST, PATCH, PUT, and DELETE requests, parameters not included in the URL should be encoded as JSON
with a Content-Type of 'application/json':

## Root Endpoint

You can issue a `GET` request to the root endpoint to get all the endpoint categories that the API supports:

<pre class="terminal">
$ curl http://api.racketlogger.com
</pre>

## Client Errors

There are three possible types of client errors on API calls that
receive request bodies:

1. Sending invalid JSON will result in a `400 Bad Request` response.

        HTTP/1.1 400 Bad Request
        Content-Length: 35

        {"message":"Problems parsing JSON"}

2. Sending the wrong type of JSON values will result in a `400 Bad
   Request` response.

        HTTP/1.1 400 Bad Request
        Content-Length: 40

        {"message":"Body should be a JSON object"}

3. Sending invalid fields will result in a `422 Unprocessable Entity`
   response.

        HTTP/1.1 422 Unprocessable Entity
        Content-Length: 149

        {
          "message": "Validation Failed",
          "errors": [
            {
              "resource": "Issue",
              "field": "title",
              "code": "missing_field"
            }
          ]
        }

All error objects have resource and field properties so that your client
can tell what the problem is.  There's also an error code to let you
know what is wrong with the field.  These are the possible validation error
codes:


## HTTP Redirects

We use HTTP redirection where appropriate. Clients should assume that any
request may result in a redirection. Receiving an HTTP redirection is *not* an
error and clients should follow that redirect. Redirect responses will have a
`Location` header field which contains the URI of the resource to which the
client should repeat the requests.

Status Code | Description
-----------|-----------|
`301` | Permanent redirection. The URI you used to make the request has been superseded by the one specified in the `Location` header field. This and all future requests to this resource should be directed to the new URI.
`302`, `307` | Temporary redirection. The request should be repeated verbatim to the URI specified in the `Location` header field but clients should continue to use the original URI for future requests.

Other redirection status codes may be used in accordance with the HTTP 1.1 spec.

## HTTP Verbs

Where possible, the API strives to use appropriate HTTP verbs for each
action.

Verb | Description
-----|-----------
`HEAD` | Can be issued against any resource to get just the HTTP header info.
`GET` | Used for retrieving resources.
`POST` | Used for creating resources, or performing custom actions (such as merging a pull request).
`PATCH` | Used for updating resources with partial JSON data.  For instance, an Issue resource has `title` and `body` attributes.  A PATCH request may accept one or more of the attributes to update the resource.  PATCH is a relatively new and uncommon HTTP verb, so resource endpoints also accept `POST` requests.
`PUT` | Used for replacing resources or collections. For `PUT` requests with no `body` attribute, be sure to set the `Content-Length` header to zero.
`DELETE` |Used for deleting resources.

## Authentication

There are two ways to authenticate for the RacketLogger API.  Requests that
require authentication will return `404 Not Found`, instead of
`403 Forbidden`, in some places.  This is to prevent the accidental leakage
of private repositories to unauthorized users.

### OAuth2 Token (sent in a header)

<pre class="terminal">
$ curl -H "Authorization: token OAUTH-TOKEN" http://api.racketlogger.com
</pre>

### OAuth2 Token (sent as a parameter)

<pre class="terminal">
$ curl http://api.racketlogger.com/?access_token=OAUTH-TOKEN
</pre>

Note that OAuth2 tokens can be obtain by emailing support.

### OAuth2 Key/Secret

<pre class="terminal">
$ curl 'http://api.racketlogger.com/users/whatever?client_id=xxxx&client_secret=yyyy'
</pre>

This should only be used in server to server scenarios.  Don't leak your
OAuth application's client secret to your users.

## User Agent Required

All API requests MUST include a valid `User-Agent` header. Requests with no `User-Agent`
header will be rejected. We request that you use your RacketLogger username, or the name of your
application, for the `User-Agent` header value. This allows us to contact you if there are problems.

Here's an example:

<pre class="terminal">
User-Agent: Awesome-Tennis-App
</pre>

If you provide an invalid `User-Agent` header, you will receive a `403 Forbidden` response.

#### UTC

We use UTC until proper timezone handling can be done.
