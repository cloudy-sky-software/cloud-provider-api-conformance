# Cloud Provider API Spec

This repository contains a spec (COMING SOON!) that describes how cloud providers can ensure a highly-compatible OpenAPI docs in order to generate native Pulumi providers. Native Pulumi providers do not rely on anything other than the cloud provider's API and communicating with them using plain HTTP requests.
This spec ensures that [`pulschema`](https://github.com/cloudy-sky-software/pulschema) is able to consistently transform the cloud provider's OpenAPI doc to a Pulumi JSON schema document with little to no modifications required.
Generating a Pulumi JSON schema is the entrypoint to being able to auto-generate SDKs in Pulumi using [crosscode](https://www.pulumi.com/crosscode/).

The main goal for this document is to ensure cloud providers are able to create a RESTful API that achieves automated conversion to the Pulumi schema spec using `pulschema`.

## Highlights

- `operationId`
- Proper use of HTTP verbs
- Proper use of RESTful API principles

## GraphQL vs. RESTful API
