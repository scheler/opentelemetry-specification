# Semantic conventions for browser events

**Status**: [Experimental](../../document-status.md)


<details>
<summary>Table of Contents</summary>
<!-- Re-generate TOC with `markdown-toc --no-first-h1 -i` -->

<!-- toc -->

- [Page View](#page-view)
- [Ajax](#ajax)
- [Exception](#exception)
- [UserAction](#useraction)
- [PageNavigationTiming](#pagenavigationtiming)
- [AjaxTiming](#ajaxtiming)
- [ResourceTiming](#resourcetiming)

<!-- tocstop -->

</details>


This document describes the semantic conventions for browser events. The events may be represented either as Span Events or Log Events.

All events have the following three high-level attributes. The domain has a fixed value of `browser`. The event name is specified at the beginning of each section below. The payload of the event goes in an attribute called `event.data` and the fields described below for each event go into this attribute.

| Key  | Value | Description  |
|---|---|---|
| `event.domain` | browser | Fixed value |
| `event.name` | | Described in each section below|
| `event.data` | | A map of key/value pairs, with the keys for each event described in following sections|



## Page View

<!-- semconv browser.page_view -->
The event name MUST be `page_view`.

| Key  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `referrer` | string | Referrer URL for the browser page (`document.referrer`) | `https://en.wikipedia.org/wiki/Main_Page` | Recommended |
| `type` | int | Browser page type | `0` | Required |
| `user_consent` | boolean | Whether or not user provided consent on the page (consent for what?) |  | Recommended |
| [`http.url`](../../../trace/semantic_conventions/http.md) | string | Full HTTP request URL in the form `scheme://host[:port]/path?query[#fragment]`. Usually the fragment is not transmitted over HTTP, but if it is known, it should be included nevertheless. [1] | `https://en.wikipedia.org/wiki/Main_Page`; `https://en.wikipedia.org/wiki/Main_Page#foo` | Required |

**[1]:** The URL fragment may be included for virtual pages

`type` MUST be one of the following:

| Value  | Description |
|---|---|
| `0` | physical_page |
| `1` | virtual_page |
<!-- endsemconv -->

## Ajax

<!-- semconv browser.ajax -->
The event name MUST be `ajax`.

| Key  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `page.url` | string | The URL of the page that made the Ajax call | `https://en.wikipedia.org/wiki/Main_Page` | Recommended |
| `type` | int | Ajax request type | `0` | Required |
| [`http.method`](../../../trace/semantic_conventions/http.md) | string | HTTP request method. | `GET`; `POST`; `HEAD` | Required |
| [`http.request_content_length`](../../../trace/semantic_conventions/http.md) | int | The size of the request payload body in bytes. This is the number of bytes transferred excluding headers and is often, but not always, present as the [Content-Length](https://www.rfc-editor.org/rfc/rfc9110.html#field.content-length) header. For requests using transport encoding, this should be the compressed size. | `3495` | Recommended |
| [`http.response_content_length`](../../../trace/semantic_conventions/http.md) | int | The size of the response payload body in bytes. This is the number of bytes transferred excluding headers and is often, but not always, present as the [Content-Length](https://www.rfc-editor.org/rfc/rfc9110.html#field.content-length) header. For requests using transport encoding, this should be the compressed size. | `3495` | Recommended |
| [`http.status_code`](../../../trace/semantic_conventions/http.md) | int | [HTTP response status code](https://tools.ietf.org/html/rfc7231#section-6). | `200` | Conditionally Required: If and only if one was received/sent. |
| [`http.url`](../../../trace/semantic_conventions/http.md) | string | Full HTTP request URL in the form `scheme://host[:port]/path?query[#fragment]`. Usually the fragment is not transmitted over HTTP, but if it is known, it should be included nevertheless. [1] | `https://www.foo.bar/search?q=OpenTelemetry#SemConv` | Required |

**[1]:** `http.url` MUST NOT contain credentials passed via URL in form of `https://username:password@www.example.com/`. In such case the attribute's value should be `https://www.example.com/`.

`type` MUST be one of the following:

| Value  | Description |
|---|---|
| `0` | fetch |
| `1` | xhr |
<!-- endsemconv -->

## Exception

<!-- semconv browser.exception -->
The event name MUST be `exception`.

| Key  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `file_name` | string | Name of the file that generated the error | `foo.js` | Recommended |
| `line_number` | int | Name of the file that generated the error |  | Recommended |
| `column_number` | int | Name of the file that generated the error |  | Recommended |
| `exception.message` | string | The exception message. | `Division by zero`; `Can't convert 'int' object to str implicitly` | Recommended |
| `exception.stacktrace` | string | A stacktrace as a string in the natural representation for the language runtime. The representation is to be determined and documented by each language SIG. | `Exception in thread "main" java.lang.RuntimeException: Test exception\n at com.example.GenerateTrace.methodB(GenerateTrace.java:13)\n at com.example.GenerateTrace.methodA(GenerateTrace.java:9)\n at com.example.GenerateTrace.main(GenerateTrace.java:5)` | Recommended |
| `exception.type` | string | The type of the exception (its fully-qualified class name, if applicable). The dynamic type of the exception should be preferred over the static type in languages that support it. | `java.net.ConnectException`; `OSError` | Recommended |
<!-- endsemconv -->

## UserAction

<!-- semconv browser.user_action -->
The event name MUST be `user_action`.

| Key  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `element` | string | Target element tag name (obtained via `event.target.tagName`) | `button` | Recommended |
| `element_xpath` | string | Target element xpath | `//*[@id="testBtn"]` | Recommended |
| `type` | string | Type of interaction. See enum [here](https://github.com/microsoft/ApplicationInsights-JS/blob/941ec2e4dbd017b8450f2b17c60088ead1e6c428/extensions/applicationinsights-clickanalytics-js/src/Enums.ts) | `clickleft` | Recommended |
| `click_coordinates` | string | Click coordinates captured as a string in the format {x}X{y}.  Eg. 345X23 | `345X23` | Recommended |
| `tags` | string[] | Grab data from data-otel-* attributes in tree | `[data-otel-asd="value" -> asd attr w/ "value"]` | Recommended |
<!-- endsemconv -->

## PageNavigationTiming

`event.name`is `page_navigation_timing`

| Key  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| fetchStart | long | || Recommended |
| unloadEventStart | long | || Recommended |
| unloadEventEnd | long | || Recommended |
| domInteractive | long | || Recommended |
| domContentLoadedEventStart | long | || Recommended |
| domContentLoadedEventEnd | long | || Recommended |
| domComplete | long | || Recommended |
| loadEventStart | long | || Recommended |
| loadEventEnd | long | || Recommended |
| firstPaint | long | || Recommended |
| firstContentfulPaint | long | || Recommended |


## AjaxTiming

`event.name` is `ajax_timing`

| Key  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| open | long | || Recommended |
| send | long | || Recommended |
| domainLookupStart | long | || Recommended |
| domainLookupEnd | long | || Recommended |
| connectStart | long | || Recommended |
| secureConnectionStart | long | || Recommended |
| connectEnd | long | || Recommended |
| requestStart | long | || Recommended |
| responseStart | long | || Recommended |
| responseEnd | long | || Recommended |
| loaded | long | || Recommended |

## ResourceTiming

`event.name` is `resource_timing`

| Key  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
|fetchStart | long | || Recommended |
|domainLookupStart | long | || Recommended |
|domainLookupEnd | long | || Recommended |
|connectStart | long | || Recommended |
|secureConnectionStart | long | || Recommended |
|connectEnd | long | || Recommended |
|requestStart | long | || Recommended |
|responseStart | long | || Recommended |
|responseEnd | long | || Recommended |

