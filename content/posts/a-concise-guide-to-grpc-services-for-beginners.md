---
title: "A concise guide to gRPC services for beginners"
date: 2021-03-25
draft: false
tags: 
  - grpc
  - protocol buffers
categories:
  - programming	
cover: 
  image: "/grpc.jpeg"
---
*This post was originally published on my Medium profile : [A concise guide to gRPC for beginners](https://penthaa.medium.com/a-concise-guide-to-grpc-for-beginners-4fab6a4e3de3)*

> A series of simple, beginner friendly tutorials that explain the what, why and how of gRPC.

## Getting started - Understanding RPC

### What is RPC?
- RPC aka Remote Procedure Calls - are just like functions. 
- These functions are executed on some remote system and hence the name. 
- It follows a request - response model. 
- A request is initiated from the client - this request is a function call with certain parameters and then, the server returns a response. 
- Real world application - to construct distributed, scalable, client - server based applications.

## gRPC
 - gRPC is a technology to implement RPC APIs.
 ![](/grpc-server.jpeg)
 - Can write servers and clients using different programming languages - a client written using Python can interact with a server written in Golang. 
 - By default gRPC uses protocol buffers as the interface definition language (IDL) to define the RPC services and the structure of the payload messages.

## Understanding protocol buffers and .proto files

### What is protobuf?
 - Protocol buffers are a way to serialize structured data in an efficient manner.
 - It uses the method of binary serialization, which is faster than text based serialization methods like JSON.
 - The protobuf specification can be implemented in various programming languages.

### What is a .proto file
A .proto file will contain the necessary definitions to see the protocol buffers in action.
A file with .proto extension will contain the definitions of **messages** and **services**. The services are analogous to procedures/functions and the messages are analogous to the data types of the parameters that can be passed to these functions. 

```go
syntax = "proto3";

message UrlRequest{
    string req = 1;
}

message UrlResponse{
    int res = 1;
}

service Url{
    rpc CallUrl(UrlRequest) returns (UrlResponse){}
}
```
In the above example, the first line of the file specifies that you're using `proto3` syntax, **CallUrl** is a **service**, **UrlRequest** and **UrlResponse** are the **message** definitions. We can see that the RPC service - CallUrl acts as a function with input parameter as UrlRequest and output as UrlResponse. The message definitions can have **fields** within them. Each field has a name and a type. Here we have **req** field which has the data type string in UrlRequest and **res** is a field whose data type is int in UrlResponse.

**Important step - Compiling a .proto file**
- This generates helper code to implement the server and client code in the programming language of your choice.
- The generated code also handles the parsing of structured data as the data is serialised into a binary format which can be transferred more efficiently as compared to text based serialisation methods like JSON, XML.

***Further Reading*** - [Language Guide (proto3)](https://developers.google.com/protocol-buffers/docs/proto3)


## Let's Code

### Basic Workflow
 - Define the protocol - Write a .proto file
	 - Define messages
	 - Define RPC service procedures with their respective parameters and return types. There can be 4 types of RPC procedures:
		 - _simple RPC_
		 - _server-side streaming RPC_
		 - _client-side streaming RPC_
		 - _bidirectional streaming RPC_
	 - Compile .proto file - generates protobuf related code for binary serialization of data; also generates client and server stubs
 - Write Server and Client code using language of your choice with the help of the generated stubs
	 - gPRC currently supports the following languages -   
		 -  [C#](https://grpc.io/docs/languages/csharp/)
		-   [C++](https://grpc.io/docs/languages/cpp/)
		-   [Dart](https://grpc.io/docs/languages/dart/)
		-   [Go](https://grpc.io/docs/languages/go/)
		-   [Java](https://grpc.io/docs/languages/java/)
		-   [Kotlin](https://grpc.io/docs/languages/kotlin/)
		-   [Node](https://grpc.io/docs/languages/node/)
		-   [Objective-C](https://grpc.io/docs/languages/objective-c/)
		-   [PHP](https://grpc.io/docs/languages/php/)
		-   [Python](https://grpc.io/docs/languages/python/)
		-   [Ruby](https://grpc.io/docs/languages/ruby/)
 - Compile and run the final application


***Further reading:***

[gRPC Documentation](https://www.grpc.io/docs/)

[gRPC vs REST: Understanding gRPC, OpenAPI and REST and when to use them in API design](https://www.google.com/amp/s/cloudblog.withgoogle.com/products/api-management/understanding-grpc-openapi-and-rest-and-when-to-use-them/amp/)

