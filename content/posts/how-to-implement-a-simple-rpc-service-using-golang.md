---
title: "How to implement a simple RPC service using Golang"
date: 2021-03-25
draft: false
tags: 
  - golang
  - grpc
  - protocol buffers
categories:
  - programming
cover: 
  image: "/grpc-golang.jpeg"
---

*This post was originally published on my Medium profile : [How to implement a simple gRPC service using Golang](https://penthaa.medium.com/how-to-implement-a-simple-grpc-service-using-golang-b9c58cab0929)*

If you want a general introduction to RPC you might want to check out my previous article in this series - [A concise guide to gRPC for beginners](https://penthaa.medium.com/a-concise-guide-to-grpc-for-beginners-4fab6a4e3de3)

The implemented code can be found on my GitHub Repository :
[Link to GitHub repository](https://github.com/penthaapatel/grpcblog)

### Before you begin 
 - [Install Golang](https://golang.org/doc/install)
 - [Install proto compilers](https://grpc.io/docs/protoc-installation/)

### Workflow
 - Define protocol - Write .proto file
 - Compile it - generate necessary protobuf related files and client and server stubs
 - Write Client code
 - Write Server code
 - Run the server and client code, check if it works

## A simple RPC service that can create blog posts 

**Overview**

 - Client sends a request to the server to create a blog post with a given title and contents.
 - The server saves the newly created blog posts in an in-memory storage.
 - Since this particular article focuses on implementing RPC services, we use an in-memory storage to save out blog posts. This in-memory storage can be replaced with any database at the backend later.
 - The database can be integrated later by utilising the power of interfaces in Golang.
### Directory structure
```bash
grpcblog
├── Makefile
├── blog
│   ├── blog.pb.go
│   ├── blog.proto
│   └── blog_grpc.pb.go
├── client
│   └── client.go
├── server
│   └── server.go
├── storage
    └── storage.go
```

### .proto file

 - **message Blog** defines the structure of the blog post and contains 2 fields - **title** and **body**.
```go
message Blog{
   string title = 1;
   string body =2;
 }
```
 - **message BlogRequest** defines the structure of the client request - has 1 field - **Blog**.
```go
message BlogRequest {
   Blog blog = 1;
 }
```
 - **message BlogResponse** defines the structure of the server response - contains 2 fields - 
	 - **id** : a universally unique identifier (**UUID**) generated on successful creation of a new post.
	 - **created** : a boolean value set to true if CreatePost is successful; false otherwise.
```go
 message BlogResponse{
   string id = 1;
   bool created = 2;
 }
```
 - The proto file contains a simple RPC service **CreatePost** - client sends a single request to the server and the server responds back with a single response. The RPC service **CreatePost** sends a **BlogRequest** from the client and the server responds with a **BlogResponse**.
 ```go
  service BlogService{
   rpc CreatePost(BlogRequest) returns (BlogResponse) {}
 }
 ```


### Compiling the .proto files
Use the following command to compile the .protofiles:

```bash
protoc --go_out=. --go_opt=paths=source_relative --go-grpc_out=. --go-grpc_opt=paths=source_relative blog/blog.proto
```

This will generate two files:
```bash
blog/blog_grpc.pb.go
```
***blog_grpc.pb.go*** contains server and client stubs. The interfaces **BlogServiceClient** and **BlogServiceServer** are not implemented. These interfaces will be later implemented in our server and client code. We will have to write own own implementation of server and client.
 
```bash
blog/blog.pb.go
```
***blog.pb.go*** contains protocol buffer code - responsible for binary serialization of data when it is transported between server and client.
### Define in-memory storage

 - **storage.go** contains custom functions to implement the temporary in-memory storage.
 - Contains an interface **BlogStorage interface** with a **Save()** and a **View()** function.
 ```go
type BlogStorage interface {
	Save(blog *blog.Blog, id string) error
	View()
}

type InMemoryBlogStorage struct {
	mutex sync.RWMutex
	blogs map[string]*blog.Blog
}
 ```
 - Blogs are saved in a map where key is the UUID and the value associated with it is the blog post.
 - To save the posts to a database, we can add another function like **SaveToDB()** that implements the **BlogStorage** interface.

### Client code

 - Reads in the blog title from the console.
 - Reads in the blog contents from the console.
 -  Contains the implementation of the function **CreatePost** that was unimplemented in **BlogServiceClient** interface in **blog/blog_grpc.pb.go**.

### Server code

 - Creates and saves new blog posts to the in-memory storage by taking input data from the client.
 - Contains the implementation of the function **CreatePost** that was unimplemented in **BlogServiceServer** interface in **blog/blog_grpc.pb.go**.
 - Generates a universally unique identifier (UUID) when a new post is created and saved to the in-memory storage
 - Displays UUID of each new post upon successful creation of a new post


### Final result
Run the server and client code in two separate terminals. 
Run server
```bash
go run server/server.go
```
Run client - Enter title and contents for the new blog post. 


And hurray! We have successfully created a new post using gRPC service.

Further reading - Some useful resources:

 - [gRPC Go Quick Start](https://grpc.io/docs/languages/go/quickstart/)
 - [Protocol Buffer Basics: Go](https://developers.google.com/protocol-buffers/docs/gotutorial)
 - [Basic introduction to gRPC in Go](https://grpc.io/docs/languages/go/basics/)
 - [proto3 language Guide](https://developers.google.com/protocol-buffers/docs/proto3)

