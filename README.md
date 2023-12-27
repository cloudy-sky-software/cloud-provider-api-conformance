# Pulumi Resource Provider API Conformance

This repository contains a conformance test and guidelines (both are COMING SOON!) that describe how resource providers can ensure a highly-compliant OpenAPI document in order to generate native Pulumi providers. Native Pulumi providers are Pulumi SDKs that rely on the provider's API and communicate with them using HTTP requests.

Following the guidelines outlined in this repository ensures that [`pulschema`](https://github.com/cloudy-sky-software/pulschema) is able to consistently and predictably transform the resource provider's OpenAPI document to a Pulumi JSON schema document with little to no modifications required, except when conformance tests fail and changes at the source of the OpenAPI doc are not possible for any reason.

Generating a Pulumi JSON schema is the entrypoint to being able to generate Pulumi provider SDKs using [crosscode](https://www.pulumi.com/crosscode/).

## Conformance

- Ensure `operationId` is not empty for all endpoint paths/operations
- Well-designed REST API
- Use `OneOf` schema with a discriminator instead of `AnyOf`
- Define enums as reusable schema refs instead of inline enums
- Mark read-only properties as such using the `readOnly` attribute

## Anatomy Of A Pulumi Provider

A Pulumi provider package consists of SDKs for each of the programming languages supported by Pulumi.
The SDKs each contain resources that can be created, read, updated and deleted. Commonly acronymized
as CRUD.

### What is a resource?

A resource can be anything your service offers. For example, if you were a cloud provider, a resource
can represent a cloud service like a managed database service or a virtual machine. Or if your service
is an identity provider platform, then a resource could be a user group or a user. These are generally
known as resources.

### Resource lifecycle

A resource's lifecycle begins when it is created, either through Pulumi or manually by the user
using your service's UI (console, dashboard etc.) When created via Pulumi, the resource needs
to be accessible to Pulumi so it can get the latest representation of that resource as seen by
your service and it can diff that with the configuration in the infrastructure app.

The result of the diffing process informs Pulumi if it needs to invoke an update endpoint for
the resource in question or if it needs to create a replacement because the configuration of
the existing one has changed in a way that your service would not support in-place updates
to it.

### Mapping HTTP methods to CRUD

So given the understanding of the lifecycle of a resource, you might have already guessed
how each action maps to the HTTP methods of a RESTful API.

| **Resource Action** | **HTTP Method** |
|---------------------|-----------------|
| Create              | POST            |
| Read                | GET             |
| Update              | PATCH           |
| Delete              | DELETE          |

In addition to that a `PUT` HTTP request may be used to replace the configuration
of a certain type of resource that does not care about its previous configuration
state. For example, consider a list environment variables. If your service supports
setting environment variables for some service, you may simply allow users to
replace the entire set of environment variables as opposed to supporting
updating a single env or deleting a single env var.

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

