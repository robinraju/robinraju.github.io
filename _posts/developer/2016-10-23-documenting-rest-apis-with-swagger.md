---
layout: single
title: Documenting REST APIs using swagger
read_time: true
category: developer
tags: [REST API, swagger]
header:
    teaser: /assets/images/posts/developer/swagger-logo.png
comments: true
author_profile: false
share: true
related: true
permalink: /:categories/:year-:month-:day-:title/
excerpt: An introduction to swagger, the most popular Framework for designing and documenting REST APIs.
---


In the software industry several standards got introduced that were crucial for the modern day business.
REST is now the most common way to expose web services. But how the clients are supposed to connect to it depends on the mutual understanding between each developers.
There is no real standard for exposing a REST API. It became the most hard part in the API documentation space, as the developers has to manually edit it and keep it
perfectly synchronized with the API.

### OpenAPI Specification
It was the initiative taken in order to solve the lack of an industry standard for creating and documenting APIs.
> The OpenAPI Specification (OAS) defines a standard, language-agnostic interface to RESTful APIs which allows both humans and computers to discover and understand the capabilities of the service without access to source code,

### Swagger
[Swagger](https://swagger.io) is a framework released by [Wordnik](https://www.wordnik.com/) and is based on technologies that power its own API documentation and are able to work with both XML and JSON APIs.
It specifies the format (URL, method, and representation) to describe REST web services. It provides also tools to generate/compute the documentation from application code.

With Swagger you have a single JSON/YAML file, were you're defining everything about your API.
You define the data models e.g. a 'Product' model, that has fields like "productId", "productName", etc.
You define all the operations e.g. add a user, update a user, etc. And for each of those operations you're defining the URI,
the inputs and outputs, the description, summary, etc. Everything about your API is described in that one file.
The swagger specific features that make it more useful are

- Comprehensive for developers and non-developers
- Human readable and machine readable
- Can modify easily

Swagger is built along with several other tools to ease the development process.

### Swagger Editor
The [swagger editor](https://swagger.io/swagger-editor/) provides a clean UI where you can write documentation in YAML or JSON and have it automatically compared against the Swagger spec.

{% include figure image_path="/assets/images/posts/developer/swagger-docs/swagger-editor.png" alt="Swagger editor" caption="The swagger editor." %}

This makes the documentation error free, as the editor provides validation against swagger spec and alternatives are being suggested.

### Swagger UI

The [swagger ui](https://swagger.io/swagger-codegen/) lets anyone to visualise your API and interact with its resources.
This process can be even made without having any of the backend functionality in place. This is automatically generated from
your swagger specification and makes it easy for the backend implementation and client side consumption.

{% include figure image_path="/assets/images/posts/developer/swagger-docs/swagger-ui.png" alt="Swagger ui" caption="The swagger UI." %}

Users can test each endpoint and get the response as JSON/XML. It’s not necessary to install some special sort of software to make the Swagger UI work.
It is built with JavaScript, HTML and CSS and may be run both on the server as well as locally.

### Swagger Codegen

The [swagger codegen](https://swagger.io/swagger-codegen/) helps to [generate](https://github.com/swagger-api/swagger-codegen/wiki/server-stub-generator-howto) server stubs and client SDKs from your swagger specification.
A developer can make use of this feature to ease the development of his API, as far as he is concerned with implementing some standards.

{% include figure image_path="/assets/images/posts/developer/swagger-docs/swagger-codegen.png" alt="Swagger codegen" caption="The swagger Codegen." %}

Swagger has become the most popular approach in API design/documentation. It is a helping hand for developers during API creation.
Generating swagger spec has become even simpler with the support for JAX-RS, Spring etc..