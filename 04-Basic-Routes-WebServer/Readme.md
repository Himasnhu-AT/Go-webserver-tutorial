## Documentation: RESTful HTTP Server

This code is an extension of the previous example, where a simple HTTP server with dynamic routing and error handling is created. In this version, an additional endpoint is added to retrieve the server's current time.

### Package and Imports

```go
package main

import (
	"GO-WebServer-Tutorial/02-REST-GET-WebServer/countryCapitals"
	"fmt"
	"io"
	"log"
	"net/http"
	"strings"
	"time"
)
```

The code begins with the `package main` declaration, indicating that this is the main package of the Go program. It imports the necessary packages: `fmt` for formatting and printing, `io` for input/output operations, `log` for logging, `net/http` for building HTTP servers and handling HTTP requests, and the external package `GO-WebServer-Tutorial/02-REST-GET-WebServer/countryCapitals` for retrieving country capital information.

### Constants

```go
const port = ":9000"
```

The code declares a constant variable `port` and assigns the value `:9000` to it. This constant represents the port on which the server will listen for incoming HTTP requests.

### Request Handler Functions

```go
func home(w http.ResponseWriter, r *http.Request) {
	urlPathElements := strings.Split(r.URL.Path, "/")

	if urlPathElements[1] == "capital" {
		capital := countryCapitals.Capitals[urlPathElements[2]]

		//If no match found, an empty string is returned. Use it for validation!
		if capital != "" {
			fmt.Fprintf(w, capital)
		} else {
			//Returns 404, Not found
			w.WriteHeader(http.StatusNotFound)
			w.Write([]byte("404 - Sorry, Resource Not Found!"))
		}
	} else {
		//Returns 400, Bad Request
		w.WriteHeader(http.StatusBadRequest)
		w.Write([]byte("400 - Sorry, Bad API Request!"))
	}
}

func getServerTime(w http.ResponseWriter, r *http.Request) {
	//Introducing io, new way to push content to writer file
	io.WriteString(w, time.Now().String())
}
```

The `home` function is the request handler function for the root URL ("/") and handles the country capital API requests, similar to the previous example.

The `getServerTime` function is a new request handler function added to handle the "/servertime" endpoint. It writes the current server time to the `http.ResponseWriter` using `io.WriteString`.

### Main Function

```go
func main() {
	http.HandleFunc("/", home)
	http.HandleFunc("/servertime", getServerTime)
	//Introducing log package that can be used to log issues
	log.Fatal(http.ListenAndServe(port, nil))
}
```

The `main` function is the entry point of the Go program. It sets up the server by registering the `home` function as the request handler for the root URL ("/") and the `getServerTime` function as the request handler for the "/servertime" endpoint using `http.HandleFunc`.

The `log.Fatal` function is used to log any errors that occur while starting the server.

### Usage

To use this code, you need to have Go installed on your machine. Save the code in a file with a `.go` extension, such as `server.go`. Make sure to have the external package `countryCapitals` available in the specified path.

Then, navigate to the directory containing the file in your terminal and run the following command:

```bash
cd server
go run main.go
```

The server will start running, and you can access it by opening a web browser and visiting `

http://localhost:9000/capital/{country}` to retrieve the capital of a specific country. For example, `http://localhost:9000/capital/USA` will return the capital of the United States. If a match is found, the browser will display the corresponding capital. If no match is found, a 404 error message will be shown. For any other API request that doesn't follow the expected format, a 400 error message will be shown.

To retrieve the server's current time, you can visit `http://localhost:9000/servertime`. The server will respond with the current time.

You can stop the server by pressing `Ctrl+C` in the terminal window where it is running.