# Semantic conventions for browser events

**Status**: [Experimental](../../document-status.md)


<details>
<summary>Table of Contents</summary>
<!-- Re-generate TOC with `markdown-toc --no-first-h1 -i` -->

<!-- toc -->

- [Events](#events)
  * [PageView](#pageview)
  * [PageLoad](#pageload)
  * [PageNavigationTiming](#pagenavigationtiming)
  * [ResourceTiming](#resourcetiming)
  * [HTTP](#http)
    + [HTTP request and response headers](#http-request-and-response-headers)
  * [HttpRequestTiming](#httprequesttiming)
  * [Exception](#exception)
  * [UserAction](#useraction)
  * [WebVital](#webvital)
- [Resource](#resource)
  * [Resource](#resource-1)
  * [ResourceVarying](#resourcevarying)
- [Mapping to OTel Signal Types](#mapping-to-otel-signal-types)

<!-- tocstop -->

</details>


This document describes the semantic conventions for browser resource and events.

# Events

The events may be represented either as Span Events or Log Events.

All events have the following three high-level attributes. The event name is specified at the beginning of each section below. The payload of the event goes in a nested attribute called `event.data`. `event.data' for each event is listed below.

| Key  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `event.domain` | string | Fixed value: browser ||Required|
| `event.name` | string | Described in each section below||Required|
| `event.data` | map | A map of key/value pairs, with the keys for each event described in following sections||Recommended|

## PageView

<!-- semconv browser.page_view -->
The event name MUST be `page_view`.

| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `referrer` | string | Referring Page URI (`document.referrer`) whenever available. | `https://en.wikipedia.org/wiki/Main_Page` | Recommended |
| `type` | int | Browser page type | `0` | Required |
| `title` | string | Page title DOM property | `Shopping cart page` | Recommended |
| url. Alias for [`http.url`](../../../trace/semantic_conventions/http.md) | string | Full HTTP request URL in the form `scheme://host[:port]/path?query[#fragment]`. Usually the fragment is not transmitted over HTTP, but if it is known, it should be included nevertheless. [1] | `https://en.wikipedia.org/wiki/Main_Page`; `https://en.wikipedia.org/wiki/Main_Page#foo` | Required |

**[1]:** The URL fragment may be included for virtual pages

`type` MUST be one of the following:

| Value  | Description |
|---|---|
| `0` | physical_page |
| `1` | virtual_page |
<!-- endsemconv -->


<details>
<summary>Sample PageView Event</summary>

```json
    "log_record": {
        "timeUnixNano":"1581452773000000789",
        "attributes": [
        {
            "key": "event.domain",
            "value": {
            "stringValue": "browser"
            }
        },
        {
            "key": "event.name",
            "value": {
            "stringValue": "page_view"
            }
        },

        {
            "key": "event.data",
            "value": {
                "nestedAttributes": [
                    {
                        "key": "http.url",
                        "value": {
                        "stringValue": "https://www.guidgenerator.com/online-guid-generator.aspx"
                        }
                    },
                    {
                        "key": "http.url",
                        "value": {
                        "stringValue": "https://www.guidgenerator.com/online-guid-generator.aspx"
                        }
                    },
                    {
                        "key": "type",
                        "value": {
                        "intValue": "0"
                        }
                    },
                    {
                        "key": "title",
                        "value": {
                        "stringValue": "Free Online GUID Generator"
                        }
                    },
                ]
            }
        }
        ]
    }

```
</details>

## PageLoad

<!-- semconv browser.page_load -->
The event name MUST be `page_load`.

| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| url. Alias for [`http.url`](../../../trace/semantic_conventions/http.md) | string | Full HTTP request URL in the form `scheme://host[:port]/path?query[#fragment]`. Usually the fragment is not transmitted over HTTP, but if it is known, it should be included nevertheless. [1] | `https://en.wikipedia.org/wiki/Main_Page`; `https://en.wikipedia.org/wiki/Main_Page#foo` | Required |

**[1]:** The URL fragment may be included for virtual pages
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


## HTTP

<!-- semconv browser.http -->
The event name MUST be `HTTP`.

| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `request_type` | int | Request type | `0` | Required |
| `response_content_length_uncompressed`. Alias for `http.response_content_length_uncompressed` | int | length of the response after it's uncompressed | `23421` | Recommended |
| `method`. Alias for [`http.method`](../../../trace/semantic_conventions/http.md) | string | HTTP request method. | `GET`; `POST`; `HEAD` | Required |
| `request_content_length`. Alias for [`http.request_content_length`](../../../trace/semantic_conventions/http.md) | int | The size of the request payload body in bytes. This is the number of bytes transferred excluding headers and is often, but not always, present as the [Content-Length](https://www.rfc-editor.org/rfc/rfc9110.html#field.content-length) header. For requests using transport encoding, this should be the compressed size. | `3495` | Recommended |
| `response_content_length`. Alias for [`http.response_content_length`](../../../trace/semantic_conventions/http.md) | int | The size of the response payload body in bytes. This is the number of bytes transferred excluding headers and is often, but not always, present as the [Content-Length](https://www.rfc-editor.org/rfc/rfc9110.html#field.content-length) header. For requests using transport encoding, this should be the compressed size. | `3495` | Recommended |
| `status_code`. Alias for [`http.status_code`](../../../trace/semantic_conventions/http.md) | int | [HTTP response status code](https://tools.ietf.org/html/rfc7231#section-6). | `200` | Conditionally Required: If and only if one was received/sent. |
| `url`. Alias for [`http.url`](../../../trace/semantic_conventions/http.md) | string | Full HTTP request URL in the form `scheme://host[:port]/path?query[#fragment]`. Usually the fragment is not transmitted over HTTP, but if it is known, it should be included nevertheless. [1] | `https://www.foo.bar/search?q=OpenTelemetry#SemConv` | Required |

**[1]:** `url` MUST NOT contain credentials passed via URL in form of `https://username:password@www.example.com/`. In such case the attribute's value should be `https://www.example.com/`.

`request_type` MUST be one of the following:

| Value  | Description |
|---|---|
| `0` | fetch |
| `1` | xhr |
| `2` | send_beacon |
<!-- endsemconv -->

### HTTP request and response headers

| Key  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `request.header.<key>`. Alias for `http.request.header.<key>` | string[] | HTTP request headers, `<key>` being the normalized HTTP Header name (lowercase, with `-` characters replaced by `_`), the value being the header values. [1] | `http.request.header.content_type=["application/json"]`; `http.request.header.x_forwarded_for=["1.2.3.4", "1.2.3.5"]` | Optional |
| `response.header.<key>`. Alias for `http.response.header.<key>` | string[] | HTTP response headers, `<key>` being the normalized HTTP Header name (lowercase, with `-` characters replaced by `_`), the value being the header values. [1] | `http.response.header.content_type=["application/json"]`; `http.response.header.my_custom_header=["abc", "def"]` | Optional |

**[1]:** Instrumentations SHOULD require an explicit configuration of which headers are to be captured.
Including all request/response headers can be a security risk - explicit configuration helps avoid leaking sensitive information.

The `User-Agent` header is already captured in the `browser.user_agent` resource attribute.
Users MAY explicitly configure instrumentations to capture them even though it is not recommended.

## HttpRequestTiming

`event.name` is `http_request_timing`

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


## Exception

<!-- semconv browser.exception -->
The event name MUST be `exception`.

| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `exception.file_name` | string | Name of the file that generated the error | `foo.js` | Recommended |
| `exception.line_number` | int | Line number where the error occurred |  | Recommended |
| `exception.column_number` | int | Column number where the error occurred |  | Recommended |
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
| `user_action_type` | string | Type of interaction. See enum [here](https://github.com/microsoft/ApplicationInsights-JS/blob/941ec2e4dbd017b8450f2b17c60088ead1e6c428/extensions/applicationinsights-clickanalytics-js/src/Enums.ts) for potential values we could add support for. | `click` | Required |
| `click_coordinates` | string | Click coordinates captured as a string in the format {x}X{y}.  Eg. 345X23 | `345X23` | Recommended |
| `tags` | string[] | Grab data from data-otel-* attributes in tree | `[data-otel-asd="value" -> asd attr w/ "value"]` | Recommended |

`user_action_type` MUST be one of the following:

| Value  | Description |
|---|---|
| `click` | click |
<!-- endsemconv -->

## WebVital

<!-- semconv browser.web_vital -->
The event name MUST be `web_vital`.

| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `name` | string | name of the web vital | `CLS` | Required |
| `value` | double | value of the web vital | `1.0`; `2.0` | Required |

`name` MUST be one of the following:

| Value  | Description |
|---|---|
| `CLS` | cls |
| `LCP` | lcp |
| `FID` | fid |
<!-- endsemconv -->


# Resource

## Resource
<!-- semconv browser.resource -->
| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `browser.visitor.id` | string | Anonymous user id - will be the same for a given browser session. It will persist across page navigations in the same browser session. | `is this a GUID?` | Recommended |
| [`browser.brands`](../../../resource/semantic_conventions/browser.md) | string[] | Array of brand name and version separated by a space [1] | `[ Not A;Brand 99, Chromium 99, Chrome 99]` | Recommended |
| [`browser.language`](../../../resource/semantic_conventions/browser.md) | string | Preferred language of the user using the browser [2] | `en`; `en-US`; `fr`; `fr-FR` | Recommended |
| [`browser.mobile`](../../../resource/semantic_conventions/browser.md) | boolean | A boolean that is true if the browser is running on a mobile device [3] |  | Recommended |
| [`browser.platform`](../../../resource/semantic_conventions/browser.md) | string | The platform on which the browser is running [4] | `Windows`; `macOS`; `Android` | Recommended |
| [`browser.user_agent`](../../../resource/semantic_conventions/browser.md) | string | Full user-agent string provided by the browser [5] | `Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/95.0.4638.54 Safari/537.36` | Recommended |
| [`service.instance.id`](../../../resource/semantic_conventions/README.md) | string | Unique ID generated on page load for the lifetime of the document (eg. persistent during SPA) [6] | `627cc493-f310-47de-96bd-71410b7dec09` | Recommended |
| [`service.name`](../../../resource/semantic_conventions/README.md) | string | Application name. Will be provided by customer. [7] | `shoppingcart` | Required |
| [`service.version`](../../../resource/semantic_conventions/README.md) | string | Application version. Will be provided by customer. | `2.0.0` | Recommended |
| [`telemetry.sdk.language`](../../../resource/semantic_conventions/README.md) | string | The language of the telemetry SDK. | `cpp` | Recommended |
| [`telemetry.sdk.name`](../../../resource/semantic_conventions/README.md) | string | The name of the telemetry SDK as defined above. | `opentelemetry` | Recommended |
| [`telemetry.sdk.version`](../../../resource/semantic_conventions/README.md) | string | The version string of the telemetry SDK. | `1.2.3` | Recommended |

**[1]:** This value is intended to be taken from the [UA client hints API](https://wicg.github.io/ua-client-hints/#interface) (`navigator.userAgentData.brands`).

**[2]:** This value is intended to be taken from the Navigator API `navigator.language`.

**[3]:** This value is intended to be taken from the [UA client hints API](https://wicg.github.io/ua-client-hints/#interface) (`navigator.userAgentData.mobile`). If unavailable, this attribute SHOULD be left unset.

**[4]:** This value is intended to be taken from the [UA client hints API](https://wicg.github.io/ua-client-hints/#interface) (`navigator.userAgentData.platform`). If unavailable, the legacy `navigator.platform` API SHOULD NOT be used instead and this attribute SHOULD be left unset in order for the values to be consistent.
The list of possible values is defined in the [W3C User-Agent Client Hints specification](https://wicg.github.io/ua-client-hints/#sec-ch-ua-platform). Note that some (but not all) of these values can overlap with values in the [`os.type` and `os.name` attributes](./os.md). However, for consistency, the values in the `browser.platform` attribute should capture the exact value that the user agent provides.

**[5]:** The user-agent value SHOULD be provided only from browsers that do not have a mechanism to retrieve brands and platform individually from the User-Agent Client Hints API. To retrieve the value, the legacy `navigator.userAgent` API can be used.

**[6]:** MUST be unique for each instance of the same `service.namespace,service.name` pair (in other words `service.namespace,service.name,service.instance.id` triplet MUST be globally unique). The ID helps to distinguish instances of the same service that exist at the same time (e.g. instances of a horizontally scaled service). It is preferable for the ID to be persistent and stay the same for the lifetime of the service instance, however it is acceptable that the ID is ephemeral and changes during important lifetime events for the service (e.g. service restarts). If the service has no inherent unique ID that can be used as the value of this attribute it is recommended to generate a random Version 1 or Version 4 RFC 4122 UUID (services aiming for reproducible UUIDs may also use Version 5, see RFC 4122 for more recommendations).

**[7]:** MUST be the same for all instances of horizontally scaled services. If the value was not specified, SDKs MUST fallback to `unknown_service:` concatenated with [`process.executable.name`](process.md#process), e.g. `unknown_service:bash`. If `process.executable.name` is not available, the value MUST be set to `unknown_service`.
<!-- endsemconv -->


<details>
<summary>Sample Resource Attributes</summary>

```json
    "resource": {
        "attributes": [
        {
            "// Customer configured",
            "key": "service.name",
            "value": {
            "stringValue": "Web-tracing"
            }
        },
        {
            "// Customer configured",
            "key": "service.version",
            "value": {
            "stringValue": "1.2.3"
            }
        },

        {
            "key": "telemetry.sdk.language",
            "value": {
            "stringValue": "webjs"
            }
        },
        {
            "key": "telemetry.sdk.name",
            "value": {
            "stringValue": "opentelemetry"
            }
        },
        {
            "key": "telemetry.sdk.version",
            "value": {
            "stringValue": "1.2.0"
            }
        },
        {
            "key": "browser.language",
            "value": {
            "stringValue": "en-US"
            }
        },
        {
            "key": "browser.brand",
            "value": {
                "arrayValue": [" Not A;Brand 99", "Chromium 99", "Chrome 99"]
            }
        },
        {
            "key": "browser.platform",
            "value": {
                "stringValue": "Android"
            }
        },
        {
            "key": "browser.mobile",
            "value": {
            "boolValue": "false"
            }
        },
        {
            "key": "browser.user_agent",
            "value": {
                "stringValue": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/95.0.4638.54 Safari/537.36"
            }
        }
        ],
    }

```
</details>

## ResourceVarying

<!-- semconv browser.resource_varying -->
| Attribute  | Type | Description  | Examples  | Requirement Level |
|---|---|---|---|---|
| `browser.screen_width` | int | Window width |  | Recommended |
| `screen_height` | int | Window height |  | Recommended |
| `session.id` | string | Session identifier | `Is this a GUID?` | Recommended |
| `browser.url` | string | URL of the current active page | `https://en.wikipedia.org/wiki/Main_Page` | Recommended |
| `browser.page_impression_id` | string | Unique impression id for the page impression, represented by a GUID. Eg: Page.html will yield 4 impression ids if the page is refreshed 4 times in the same browser instance. Could change in SPA usage (when url changes -> new impression) | `GUID` | Recommended |
| [`enduser.id`](../../../trace/semantic_conventions/span-general.md) | string | Username or client_id extracted from the access token or [Authorization](https://tools.ietf.org/html/rfc7235#section-4.2) header in the inbound request from outside the system. [1] | `username` | Recommended |

**[1]:** Authenticated user id
<!-- endsemconv -->


<details>
<summary>Sample ResourceVarying Attributes</summary>

```json
    "resource": {
        "varying_attributes": [
        {
            "key": "enduser.id",
            "value": {
            "stringValue": "bob.the.builder"
            }
        },
        {
            "key": "browser.page_impession_id",
            "value": {
            "stringValue": "cb05739e-600d-4b89-b994-755ba531af0b"
            }
        },

        {
            "key": "browser.url",
            "value": {
            "stringValue": "https://www.guidgenerator.com/online-guid-generator.aspx"
            }
        },
        {
            "key": "session.id",
            "value": {
            "stringValue": "0831c954-5263-41c7-a0f8-bad7a3e63146"
            }
        },
        {
            "key": "browser.screen_width",
            "value": {
            "intValue": 1080
            }
        },
        {
            "key": "browser.screen_height",
            "value": {
            "intValue": 800
            }
        },

        ],
    }

```
</details>

# Mapping to OTel Signal Types

Listing this out for the purpose of discussion.

| Object  | Current Name (if any)|OTel Signal Type | Description  | Note  |
|---|---|---|---|--|
|PageView||Event| Emitted at the earliest opportunity when a page loads. Contains Trace Context of PageLoad Span (see next item)  |---|
|PageLoad|documentLoad|Span| Emitted on body onLoad after the Performance Navigation Timing info is available. |---|
| - |  | Span | Span for page fetch ||
|Performance-NavigationTiming||Span-Event|Performance Navigation Timing of the page|Emitted either as a Span Event attached to `PageLoad` (default) or as a separate Event linked with the `PageLoad` span|
|HTTP|xhr, fetch, documentFetch, resourceFetch|Span|Emitted for xhr, fetch,sendBeacon calls and also for Page and Resource fetch|May include an `Exception` event if any exception occurs during the HTTP request|
|HttpRequestTiming||Span-Event|Attached to HTTP span||
|ResourceTiming||Span-Event| Performance Timing of the resource loaded | |
|Exception||Event|Emitted for any unhandled exception occuring outside of an HTTP request|Unlike other Events, the `exception.` attributes will all be top-level attributes and will not be in `event.data`. This is to stay consistent with `Exception` Span-Events.|
|UserAction|UserInteraction|Event|User actions||
|WebVital||Event|Web Vitals||





## Flow of browser telemetry

1. When a Page loads, a `PageView` event is emitted right away at the earliest opportunity. A `PageLoad` span is also started,and its trace context is attached to the `PageView` event emitted. 
2. The `PageLoad` span is emitted after the browser calls body onLoad. This span has `PerformanceNavigationTiming` attached as a span-event.
3. For each request made the browser further, an `HTTP` span is emitted. These requests could be any of the following: a) Initial Page/html fetch b)XHR, Fetch, sendBeacon c) resource fetch (js, css, images, etc)

    3.1 This `HTTP` will also have a Timing event attached. This will be `HttpRequestTiming` event in the case of initial page html, XHR, Fetch and sendBeacon calls and `ResourceTiming` event in the case of calls to fetch resources.
    3.2 Each of the `HTTP` spans will also point to the original `PageLoad` span emitted through one of the following:
    - The `link` in their spans will point to the `PageLoad` span
    - Both the `PageLoad` and all `HTTP` spans emitted will have the same `service.instance.id` resource attribute.
