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

<!-- tocstop -->

</details>

## Page View

<!-- semconv browser.page_view -->
The event name MUST be `page_view`.

| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `browser.page.referrer` | string | Referrer URL for the browser page (`document.referrer`) | `https://en.wikipedia.org/wiki/Main_Page` | Recommended |
| `browser.page.type` | int | Browser page type | `0` | Required |
| `browser.page.user_consent` | boolean | Whether or not user provided consent on the page (consent for what?) |  | Recommended |
| [`http.url`](../../../trace/semantic_conventions/http.md) | string | Full HTTP request URL in the form `scheme://host[:port]/path?query[#fragment]`. Usually the fragment is not transmitted over HTTP, but if it is known, it should be included nevertheless. [1] | `https://en.wikipedia.org/wiki/Main_Page`; `https://en.wikipedia.org/wiki/Main_Page#foo` | Required |

**[1]:** The URL fragment may be included for virtual pages

`browser.page.type` MUST be one of the following:

| Value  | Description |
|---|---|
| `0` | physical_page |
| `1` | virtual_page |
<!-- endsemconv -->

## Ajax

<!-- semconv browser.ajax -->
The event name MUST be `ajax`.

| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `browser.page.url` | string | The URL of the page that made the Ajax call | `https://en.wikipedia.org/wiki/Main_Page` | Recommended |
| `browser.request_type` | int | Ajax request type | `0` | Required |
| [`http.method`](../../../trace/semantic_conventions/http.md) | string | HTTP request method. | `GET`; `POST`; `HEAD` | Required |
| [`http.request_content_length`](../../../trace/semantic_conventions/http.md) | int | The size of the request payload body in bytes. This is the number of bytes transferred excluding headers and is often, but not always, present as the [Content-Length](https://www.rfc-editor.org/rfc/rfc9110.html#field.content-length) header. For requests using transport encoding, this should be the compressed size. | `3495` | Recommended |
| [`http.response_content_length`](../../../trace/semantic_conventions/http.md) | int | The size of the response payload body in bytes. This is the number of bytes transferred excluding headers and is often, but not always, present as the [Content-Length](https://www.rfc-editor.org/rfc/rfc9110.html#field.content-length) header. For requests using transport encoding, this should be the compressed size. | `3495` | Recommended |
| [`http.status_code`](../../../trace/semantic_conventions/http.md) | int | [HTTP response status code](https://tools.ietf.org/html/rfc7231#section-6). | `200` | Conditionally Required: If and only if one was received/sent. |
| [`http.url`](../../../trace/semantic_conventions/http.md) | string | Full HTTP request URL in the form `scheme://host[:port]/path?query[#fragment]`. Usually the fragment is not transmitted over HTTP, but if it is known, it should be included nevertheless. [1] | `https://www.foo.bar/search?q=OpenTelemetry#SemConv` | Required |

**[1]:** `http.url` MUST NOT contain credentials passed via URL in form of `https://username:password@www.example.com/`. In such case the attribute's value should be `https://www.example.com/`.

`browser.request_type` MUST be one of the following:

| Value  | Description |
|---|---|
| `0` | fetch |
| `1` | xhr |
<!-- endsemconv -->

## Exception

<!-- semconv browser.exception -->
The event name MUST be `exception`.

| Attribute  | Type | Description  | Examples  | Requirement Level |
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

| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `element` | string | Target element tag name (obtained via `event.target.tagName`) | `button` | Recommended |
| `element_xpath` | string | Target element xpath | `//*[@id="testBtn"]` | Recommended |
| `user_action.type` | string | Type of interaction. See enum [here](https://github.com/microsoft/ApplicationInsights-JS/blob/941ec2e4dbd017b8450f2b17c60088ead1e6c428/extensions/applicationinsights-clickanalytics-js/src/Enums.ts) | `clickleft` | Recommended |
| `click_coordinates` | string | Click coordinates captured as a string in the format {x}X{y}.  Eg. 345X23 | `345X23` | Recommended |
| `tags` | string[] | Grab data from data-otel-* attributes in tree | `[data-otel-asd="value" -> asd attr w/ "value"]` | Recommended |
<!-- endsemconv -->
