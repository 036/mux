[![Build Status](https://travis-ci.org/donutloop/mux.svg?branch=master)](https://travis-ci.org/donutloop/mux)

# What is mux ?

mux is a lightweight fast HTTP request router (also called multiplexer or just mux for short) for Go 1.7.

The difference between the default mux of Go's net/http package and this mux is,
it's supports variables and regex in the routing pattern and matches against the request method. It also scales better.

## Features:

* REGEX URL Matcher
* Vars URL Matcher
* GetVars in handler
* GetQueries in handler
* URL Matcher
* Header Matcher
* Scheme Matcher 
* Custom Matcher
* Route Validators 
* Http method declaration
* Support for standard lib http.Handler and http.HandlerFunc
* Custom NotFound handler
* Respect the Go standard http.Handler interface
* Routes are sorted
* Context support

## Feature request are welcome

## Example (Method GET):

```go
    package main

    import (
        "net/http"
        "fmt"
        "os"

        "github.com/donutloop/mux"
    )

    func main() {
        r := mux.Classic()

        //URL: https://localhost:8080/home
        r.HandleFunc(http.MethodGet, "/home", homeHandler)
        
        //URL: https://localhost:8080/home-1
        r.Handle(http.MethodGet, "/home-1", http.HandlerFunc(homeHandler))
        
        //URL: https://localhost:8080/home-2
        r.Get("/home-2", homeHandler)
        
        //URL: https://localhost:8080/home-3
        r.RegisterRoute(http.MethodGet, r.NewRoute().Path("/home-3").HandlerFunc(homeHandler))
        
        //URL: https://localhost:8080/home-4
        r.RegisterRoute(http.MethodGet, r.NewRoute().Path("/home-4").Handler(http.HandlerFunc(homeHandler)))
        
    	errorHandler := func(errs []error) {
            for _ , err := range errs {
                fmt.Print(err)
            }
            if 0 != len(errs) {
                os.Exit(2)
            }
	    }

        r.ListenAndServe(":8080", errorHandler)
    }

    func homeHandler(rw http.ResponseWriter, req *http.Request) {
        //...
        rw.Write([]byte("Hello World!"))
    }
```

## Example (Method POST):

```go
    package main

    import (
        "net/http"
        "fmt"
        "os"

        "github.com/donutloop/mux"
    )

    func main() {
        r := mux.Classic()

        //URL: https://localhost:8080/user/create
        r.HandleFunc(http.MethodPost, "/user/create", userHandler)

        //URL: https://localhost:8080/user-1/create
        r.Handle(http.MethodPost, "/user-1/create", http.HandlerFunc(userHandler))

        //URL: https://localhost:8080/user-2/create        
        r.Post("/user-2/create", userHandler)

        //URL: https://localhost:8080/user-3/create
        r.RegisterRoute(http.MethodPost, r.NewRoute().Path("/user-3/create").HandlerFunc(userHandler))

        //URL: https://localhost:8080/user-4/create        
        r.RegisterRoute(http.MethodPost, r.NewRoute().Path("/user-4/create").Handler(http.HandlerFunc(userHandler)))
        
    	errorHandler := func(errs []error) {
            for _ , err := range errs {
                fmt.Print(err)
            }
            if 0 != len(errs) {
                os.Exit(2)
            }
	    }

        r.ListenAndServe(":8080", errorHandler)
    }

    func userHandler(rw http.ResponseWriter, req *http.Request) {
        //...
        rw.Write([]byte("Created successfully a new user"))
    }
```

## Example (Method GET & Scheme Matcher):

```go
    package main

    import (
        "net/http"
        "fmt"
        "os"

        "github.com/donutloop/mux"
    )

    func main() {
        r := mux.Classic()
        
        //URL: https://localhost:8080/home
        r.Get("/home", homeHandler).Schemes("https")
        
    	errorHandler := func(errs []error) {
            for _ , err := range errs {
                fmt.Print(err)
            }
            if 0 != len(errs) {
                os.Exit(2)
            }
	    }

        r.ListenAndServe(":8080", errorHandler)
    }

    func homeHandler(rw http.ResponseWriter, req *http.Request) {
        //...
        rw.Write([]byte("Hello world"))
    }
```
## Example (Method Put & GetVars):

```go
    package main

    import (
        "net/http"
        "fmt"
        "os"

        "github.com/donutloop/mux"
    )

    func main() {
        r := mux.Classic()
        
        //URL: http://localhost:8080/user/update/1
        r.Put("/user/update/:number", userHandler)

    	errorHandler := func(errs []error) {
            for _ , err := range errs {
                fmt.Print(err)
            }
            if 0 != len(errs) {
                os.Exit(2)
            }
	    }

        r.ListenAndServe(":8080", errorHandler)
    }

    func userHandler(rw http.ResponseWriter, req *http.Request) {
        userId := mux.GetVars(req).Get(":number")
        //...
        rw.Write([]byte("Updated successfully a new user"))
    }
```

## Example (Method GET & GetQueries):

```go
    package main

    import (
        "net/http"
        "fmt"
        "os"

        "github.com/donutloop/mux"
    )

    func main() {
        r := mux.Classic()
        
        //URL: http://localhost:8080/users?limit=10
        r.Get("/users", userHandler)

    	errorHandler := func(errs []error) {
            for _ , err := range errs {
                fmt.Print(err)
            }
            if 0 != len(errs) {
                os.Exit(2)
            }
	    }

        r.ListenAndServe(":8080", errorHandler)
    }

    func userHandler(rw http.ResponseWriter, req *http.Request) {
        limit := mux.GetQueries(req).Get("limit")[0]
        //...
    }
```

## Example (Method GET & Regex):

```go
    package main

    import (
        "net/http"
        "fmt"
        "os"

        "github.com/donutloop/mux"
    )

    func main() {
        r := mux.Classic()
        
        //URL: http://localhost:8080/user/1
        r.Get("/user/#([0-9]){1,}", userHandler)

    	errorHandler := func(errs []error) {
            for _ , err := range errs {
                fmt.Print(err)
            }
            if 0 != len(errs) {
                os.Exit(2)
            }
	    }

        r.ListenAndServe(":8080", errorHandler)
    }

    func userHandler(rw http.ResponseWriter, req *http.Request) {
        //...
    }
```
## More documentation comming soon