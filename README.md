# Pulumi Resource Provider API Conformance

This repository contains a conformance test and guidelines (COMING SOON!) that describe how resource providers can ensure a highly-compliant OpenAPI document in order to generate native [Pulumi providers](./pulumi.md). Native Pulumi providers are Pulumi SDKs that rely on the provider's API and communicate with them using HTTP requests.

Following the guidelines outlined in this repository ensures that [`pulschema`](https://github.com/cloudy-sky-software/pulschema) is able to consistently and predictably transform the resource provider's OpenAPI document to a Pulumi JSON schema document with little to no modifications required, except when conformance tests fail and changes at the source of the OpenAPI doc are not possible for any reason.

Generating a Pulumi JSON schema is the entrypoint to being able to generate Pulumi provider SDKs using [crosscode](https://www.pulumi.com/crosscode/).

## Conformance

- Frist, ensure OpenAPI spec is free of validation errors
- Ensure `operationId` is not empty for all endpoint paths/operations
- Well-designed REST API
- Use `OneOf` schema with a discriminator instead of `AnyOf`
- Define enums as reusable schema refs instead of inline enums
- Mark read-only properties using the `readOnly` attribute
- Ensure the `id` path param in a URI is used for the resource being operated on
<details>
<summary>Path params explainer</summary>

If a resource is a child resource linked by the ID of a parent resource, then ensure that the
path param that represents the parent resource's ID is not called just `id`. The `id` path param
should always only be used by the primary resource that the endpoint serves.

For example, in the endpoint `/servers/{server_id}/volumes/{id}`, `servers` is the parent resource
for `volumes`. The `{server_id}` path param is automatically added as a required input when generating
the resource schema for a `volume` resource. And this endpoint in particular is to "get" a single volume
belonging to a server. Therefore, the volume is the primary resource in this case and the server is the
parent resource.

In Pulumi, every resource automatically gets an `id` attribute which is the unique provider-assigned
id.

</details>

## Deciding Between GraphQL vs. REST API

Are you having discussions about whether or not your public API should be based on
GraphQL or REST? Then this is for you.

**tl;dr;** Choose REST API if you want to be able to generate SDKs for use with
infrastructure automation platforms. In some cases, you may want to have both.
Probably because your own client applications may use the GraphQL API. You might
be interested in describing your API spec using Microsoft's [TypeSpec](https://microsoft.github.io/typespec/)
which allows you to emit OpenAPI and GraphQL schema
docs from a single source.

The philosophical decision of whether or not you should choose GraphQL for any
number of reasons is out-of-scope for this document.
You'll have to do the due dilligence to see if it's the right fit for your
product. That said, GraphQL can be great for fields of applications that
are able to take advantage of the granularity in the request payload.

However, GraphQL lacks proper semantics that indicate the type of
action being performed. That is, in GraphQL, every non-query (read)
is simply a mutation. The only way to identify what _type_ of mutation
an operation is, is by looking at the name of the operation. There are
no standards for naming operations. While it's possible to solve that
by agreeing upon standard naming convention for operations, there is
still the problem of associating the mutations with each other.
That is, there is no way to know when a group of mutations
belong to a specific resource. By contrast, in a REST API,
all mutations for a resource can be easily identified by
the request URI path. REST by design operates at a
resource level. Hence, why it makes it perfect for
infrastructure automation platforms like Pulumi.

