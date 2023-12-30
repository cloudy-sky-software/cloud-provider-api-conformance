# Anatomy Of A Pulumi Provider

A Pulumi provider package consists of SDKs for each of the programming languages supported by Pulumi.
The SDKs each contain resources that can be created, read, updated and deleted. Commonly acronymized
as CRUD.

## What is a resource?

A resource can be anything your service offers. For example, if you were a cloud provider, a resource
can represent a cloud service like a managed database service or a virtual machine. Or if your service
is an identity provider platform, then a resource could be a user group or a user. These are generally
known as resources.

## Resource lifecycle

A resource's lifecycle begins when it is created, either through Pulumi or manually by the user
using your service's UI (console, dashboard etc.) When created via Pulumi, the resource needs
to be accessible to Pulumi so it can get the latest representation of that resource as seen by
your service and it can diff that with the configuration in the infrastructure app.

The result of the diffing process informs Pulumi if it needs to invoke an update endpoint for
the resource in question or if it needs to create a replacement because the configuration of
the existing one has changed in a way that your service would not support in-place updates
to it.

## Mapping HTTP methods to CRUD

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
