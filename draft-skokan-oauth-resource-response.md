---
title: "Resource Indicator Response Parameter for OAuth 2.0"
abbrev: "OAuth Resource Response"
category: std

docname: draft-skokan-oauth-resource-response-latest
updates: 8707
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Security"
workgroup: "Web Authorization Protocol"
keyword:
 - OAuth
 - Resource
 - Audience
venue:
  group: "Web Authorization Protocol"
  type: "Working Group"
  mail: "oauth@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/oauth/"
  github: "panva/draft-oauth-rfc8707bis"
  latest: "https://panva.github.io/draft-oauth-rfc8707bis/draft-skokan-oauth-resource-response.html"

author:
 -
    fullname: Filip Skokan
    organization: Okta
    email: panva.ip@gmail.com

normative:
  RFC3986:
  RFC6749:
  RFC8707:
  IANA.OAuth.Parameters:
    title: "OAuth Parameters"
    target: https://www.iana.org/assignments/oauth-parameters
    author:
      - org: IANA
    date: false

...

--- abstract

This document defines the `resource` parameter for OAuth 2.0 access
token responses, enabling an authorization server to indicate to the
client the resource(s) which an issued access token is for. It updates
"Resource Indicators for OAuth 2.0" (RFC 8707).


--- middle

# Introduction

"Resource Indicators for OAuth 2.0" {{RFC8707}} defines the `resource`
request parameter for use in authorization requests and access token
requests, enabling a client to signal the target protected resource(s)
to an authorization server. However, it does not define a corresponding
response parameter that would allow the authorization server to
communicate back to the client which resource(s) the issued access token
is actually for.

Without a response parameter, a client cannot reliably determine the
effective resource(s) of an issued access token when the authorization
server restricts the token to a subset of the requested resources, or
when it applies a default resource policy in cases where the client did
not include the `resource` parameter in its request.

This document addresses that gap by defining the `resource` parameter
for use in access token responses.

## Requirements Notation and Conventions

{::boilerplate bcp14-tagged}

# Access Token Response Resource Parameter {#ResourceResponseParameter}

In access token responses, the `resource` parameter is represented as a
JSON array of strings, unlike the repeated form-encoded or query
parameter used in requests defined in {{RFC8707}}.

The `resource` parameter defined for an access token response
({{Section 5.1 of RFC6749}}) is used to indicate to the client the
resource(s) which an issued access token is for.

resource:
: OPTIONAL, if identical to the `resource` value(s) requested by the
  client; otherwise, REQUIRED. Its value is a JSON array of strings,
  where each string is an absolute URI as specified by
  {{Section 4.3 of RFC3986}}, identifying a protected resource for
  which the access token is valid. The array MUST contain at least
  one value.

The `resource` response parameter serves a similar role to the `scope`
response parameter defined in {{Section 5.1 of RFC6749}}: it informs the
client when the resource(s) associated with the issued access token
differ from what the client requested. This can occur when the
authorization server restricts the token to a subset of the requested
resources, or when the authorization server applies a default resource
policy in cases where the client did not include the `resource` parameter
in its request.

If the client requested access to multiple resources but the
authorization server issues an access token that is restricted to a
subset of those resources, the authorization server MUST include the
`resource` parameter in the response to inform the client of the
effective resource(s). The client can then make additional token requests
for the remaining resources as needed.

The following is a non-normative example of a token endpoint response
where the authorization server indicates that the issued access token is
valid for use at `https://cal.example.com/` (extra line breaks and
indentation are for display purposes only).

~~~
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-cache, no-store

{
   "access_token":"eyJhbGciOiJFUzI1NiIsImtpZCI6Ijc3In0.eyJpc3MiOi
    JodHRwOi8vYXV0aG9yaXphdGlvbi1zZXJ2ZXIuZXhhbXBsZS5jb20iLCJzdWI
    iOiJfX2JfYyIsImV4cCI6MTU4ODQyMDgwMCwic2NvcGUiOiJjYWxlbmRhciIs
    ImF1ZCI6Imh0dHBzOi8vY2FsLmV4YW1wbGUuY29tLyJ9.nNWJ2dXSxaDRdMUK
    lzs-cYIj8MDoM6Gy7pf_sKrLGsAFf1C2bDhB60DQfW1DZL5npdko1_Mmk5sUf
    zkiQNVpYw",
   "token_type":"Bearer",
   "expires_in":3600,
   "refresh_token":"4LTC8lb0acc6Oy4esc1Nk9BWC0imAwH7kic16BDC2",
   "scope":"calendar",
   "resource":["https://cal.example.com/"]
}
~~~
{: #response-example-resource title="Access Token Response with Resource"}

# Security Considerations

This document inherits the security considerations of {{RFC8707}}.

Knowledge of the resource(s) for which an access token is valid does not
introduce new security concerns for the client. The `resource` response
parameter merely makes explicit information that the client either
already requested or that the authorization server determined based on
its policy.

# Privacy Considerations

The `resource` response parameter conveys information about the
resource(s) associated with an access token back to the client. Since
the client either requested these resources or they were determined by
authorization server policy, no new privacy-sensitive information is
disclosed by this parameter.

# IANA Considerations

## OAuth Parameters Registration

This specification updates the following value in the IANA "OAuth
Parameters" registry {{IANA.OAuth.Parameters}} established by
{{RFC6749}}.

{:compact}
Parameter name:
: resource

Parameter usage location:
: authorization request, token request, token response

Change controller:
: IETF

Specification document(s):
: {{Section 2 of RFC8707}} and {{ResourceResponseParameter}} of this
  document


--- back

# Acknowledgments
{:numbered="false"}

The original "Resource Indicators for OAuth 2.0" specification
{{RFC8707}} was authored by Brian Campbell, John Bradley, and Hannes
Tschofenig.

# Document History
{:numbered="false"}

draft-skokan-oauth-resource-response-00

- Initial draft defining the `resource` access token response parameter
