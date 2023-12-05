# Cloud Provider API Spec

This repository contains a spec (COMING SOON!) that describes how cloud providers can ensure a highly-compatible OpenAPI docs in order to generate native Pulumi providers. Native Pulumi providers do not rely on anything other than the cloud provider's API and communicating with them using plain HTTP requests.
This spec ensures that [`pulschema`](https://github.com/cloudy-sky-software/pulschema) is able to consistently transform the cloud provider's OpenAPI doc to a Pulumi JSON schema document with little to no modifications required.
Generating a Pulumi JSON schema is the entrypoint to being able to auto-generate SDKs in Pulumi using [crosscode](https://www.pulumi.com/crosscode/).

The main goal of this spec is to not introduce a new piece of underlying technology that cloud providers need to maintain.
