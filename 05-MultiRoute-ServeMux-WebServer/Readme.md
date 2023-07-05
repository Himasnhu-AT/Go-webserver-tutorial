## Documentation: Multi-Route HTTP Server

This code extends the previous example and introduces a multi-route HTTP server using `http.NewServeMux`. It adds two new endpoints: one to retrieve a random number and another to retrieve the server's current time.

### Package and Imports

```go
package main

import (
	"GO-WebServer-Tutorial/05-MultiRoute-ServeMux-WebServer/countryCapitals"
	"fmt"
	"io"
	"log"
	"math/rand"
	"net/http"
	"strings"
	"time"
)
```

The code begins with the `package main` declaration, indicating that this is the main package of the Go program. It imports the necessary packages: `fmt` for formatting and printing, `io` for input/output operations, `log` for logging, `math/rand` for generating random numbers, `net/http` for building HTTP servers and handling HTTP requests, and the external package `GO-WebServer-Tutorial/05-MultiRoute-ServeMux-WebServer/countryCapitals` for retrieving country capital information.

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
	io.WriteString(w, time.Now().String())
}

func getRandomNumber(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, rand.Int())
}
```

The `home` function is the request handler function for the root URL ("/") and handles the country capital API requests, similar to the previous example.

The `getServerTime` function is a request handler function for the "/servertime" endpoint. It writes the current server time to the `http.ResponseWriter` using `io.WriteString`.

The `getRandomNumber` function is a request handler function for the "/random" endpoint. It generates a random number using `rand.Int` and writes it to the `http.ResponseWriter` using `fmt.Fprintln`.

### Main Function

```go
func main() {
	//Introducing NewServeMux for multi-routing
	rtr := http.NewServeMux()

	rtr.HandleFunc("/", home)
	rtr.HandleFunc("/servertime", getServerTime)
	rtr.HandleFunc("/random", getRandomNumber)
	log.Fatal(http.ListenAndServe(port, rtr))
}
```

The `main` function is the entry point of the Go program. It sets up the server using `http.NewServeMux` to create a new instance of `http.ServeMux` for multi-routing.

The `HandleFunc` method of the `http.ServeMux` is used to register the `home` function as the request handler for the root URL ("/"), the `getServerTime` function as the request handler for the "/servertime

" endpoint, and the `getRandomNumber` function as the request handler for the "/random" endpoint.

The server is started using `http.ListenAndServe` with the specified port and the `http.ServeMux` instance (`rtr`) as the handler.

### Usage

To use this code, you need to have Go installed on your machine. Save the code in a file with a `.go` extension, such as `server.go`. Make sure to have the external package `countryCapitals` available in the specified path.

Then, navigate to the directory containing the file in your terminal and run the following command:

```bash
cd server
go run main.go
```

The server will start running, and you can access it by opening a web browser and visiting:

- `http://localhost:9000/capital/{country}` to retrieve the capital of a specific country. For example, `http://localhost:9000/capital/USA` will return the capital of the United States.
- `http://localhost:9000/servertime` to retrieve the server's current time.
- `http://localhost:9000/random` to retrieve a random number.

You can stop the server by pressing `Ctrl+C` in the terminal window where it is running.