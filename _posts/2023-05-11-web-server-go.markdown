---
layout: post
title:  "A Web Server In Go"
date:   2023-05-11 07:13:00 -0400
---
*I originally wrote this as part of an interview circuit to demonstrate my technical writing ability. Given that there is no company-specific information, I've chosen to put this online.*

---

Go is a programming language created by Google and has quickly become one of the most popular languages used by developers. Today, we'll take a look at how to write a simple Go program that accepts HTTP requests.

----------------------
## Prerequisites

This guide assumes the following things:

* You're using macOS
* You're familar with how HTTP requests work
* You swear to use this knowledge for good

We also assume that you know how to use a text editor (our preference is VSCode) and the command line. If you don't, take some time and go the guides linked below before moving on:

* [A text editor](https://www.learnhowtoprogram.com/introduction-to-programming/getting-started-with-intro-to-programming/text-editor-visual-studio-code) (our preference is VSCode)
* [The command line](https://www.youtube.com/watch?v=aKRYQsKR46I)

----------------------
## Installing Go & Project Setup

The Go team provides an easy to follow guide for installing Go. For the purposes of this guide, we're assuming you're using macOS. The Go team provides an Installer for macOS - all you need to do is follow the directions here:

[Official Go Installation Guide](https://go.dev/doc/install)

 In order to download Go, you'll need to know whether or not your Mac is ARM64 or X86-64. This references the type of chip in your Mac: Apple chips are ARM64, while Intel chips are X86-64. [Here's a helpful guide](https://www.howtogeek.com/706226/how-to-check-if-your-mac-is-using-an-intel-or-apple-silicon-processor/) if you don't know which chips your computer is using.

To confirm that you've installed Go correctly, open a Terminal window and run the following:
```sh
$ go version
```
This should output the installed version of Go (as of May 5th, 2023: go1.20.4).

Next, set up your project by running the following:
```sh
$ mkdir first-project
$ cd first-project
```

We'll write a simple Go program first to become familar with the boilerplate of the language, and then move on to creating a web server.

----------------------
## Writing A Simple Program

Open the text editor of your choice (our preference is VSCode) in this folder and save the following code as `main.go`.

```go
package main

import "fmt"

func main() {
  fmt.Println("My first Go program!")
}
```

Let's break this down line by line so we understand what's going on.

```go
package main
```

Every piece of Go code has to have an associated package. At a high level, packages are how we define which pieces of code belong together.

Right now, we're putting our code in the `main` package. which is a special type of package. The `main` package is where Go starts running code from, and so every program you'll ever write in Go will need a `main` package.

```go
import "fmt"
```

Here's a package! We're importing the `"fmt"` package because it provides a lot of great functionality around formatting text. The `"fmt"` package is built into the Go language, so all you need to do is import it in order to use it. This is opposed to other packages you may need to download first before you use them.

In the future, you may find yourself using packages written by other people available for download, or even writing and using your own packages.

```go
func main() {

}
```

A function defines a block of code within its' braces `{}`. Because we're in the `main` package, we need a `main()` function because that's where Go starts running the code for our program. You should only have 1 function called `main()` in your program.

```go
fmt.Println("My first Go program!")
```
This is where we're using the `"fmt"` package! Specifically, we're calling the `Println()` function within the package, which just prints a line of text.


To verify that our code works as intended, run this command in your  Terminal:
```sh
$ go run main.go
```
This should print out `My first Go program!`. Congratulations!

----------------------
## A Web Server

Ok, so now that we've gotten our feet wet, we can dive into creating a basic web server. Replace everything in `main.go` with the following:

```go
package main

import (
    "fmt"
    "net/http"
)

func main() {
    http.HandleFunc("/", HelloServer)
    http.ListenAndServe(":8080", nil)
}

func HelloServer(w http.ResponseWriter, r *http.Request) {
    fmt.Println("Received a request!")
    fmt.Fprintf(w, "Hello!")
}
```

Some things here are similar to what we had before, but there's some differences. Let's take a look at what's changed:

```go
import (
    "fmt"
    "net/http"
)
```

We're importing the `"net/http"` package in addition to `"fmt"` because we'll need HTTP capabilities for our web server. Just like the `"fmt"` package, `"net/http"` is built into Go.

You'll also notice that rather than having to import each package separately, we can define a list of packages, enclosed in parentheses `()`.

```go
http.HandleFunc("/", HelloServer)
```

This is a new line in our `main()` function. `HandleFunc` is a function in the `http` package which maps a HTTP route - in this case `/` - to a handler function that will *handle* the incoming request to that route - in this case, the `HelloServer` function. Let's take a look at `HelloServer`.

```go
func HelloServer(w http.ResponseWriter, r *http.Request) {
}
```

This is a new function we've written, and it takes in two arguments: `w` which is of type `http.ResponseWriter`, and `r` which is of type `*http.Request`. We know to include these two arguments because that's what `http.HandleFunc` requires for our handler function.

We'll use `w`, or the `http.ResponseWriter`, to write a response back to whomever is sending us a request. We're not going to use `r` in our example, but `*http.Request` has the data from the request if we need to access it.

```go
fmt.Println("Received a request!")
```

This is the function in the `"fmt"` package we used before, just with different text that we're printing out - just a simple acknowledgement that we received a request!

```go
fmt.Fprintf(w, "Hello!")
```

Here, we're using the `Fprintf()` function to print the text `"Hello!"` to `w`, our ResponseWriter from earlier. In essence, we're just returning `"Hello!"` as a response to whomever sent us the request.

```go
http.ListenAndServe(":8080", nil)
```

Now that we have a function to handle requests, we need to have our web server listening for requests. Here, we've specified that we'll listen on port `8080`.

We can now run our web server the same way that we ran our simple program:
```sh
$ go run main.go
```

To test if our server works as we intended, we'll need to send a request to it and see if we get "Hello!" back. Because we're running this web server on our own machine, we can simply open a browser and go to [`http://localhost:8080/`](http://localhost:8080/) to send a request.

Going to [`http://localhost:8080/`](http://localhost:8080/) should have two outcomes.

1. You see `Hello!` as a response in your browser
2. Your Terminal should print `"Received a request!"`

Congratulations! You've stood up your first web server in Go.