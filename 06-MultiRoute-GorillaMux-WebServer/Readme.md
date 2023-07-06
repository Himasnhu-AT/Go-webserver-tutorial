## Documentation: Multi-Route HTTP Server with Gorilla Mux

This code extends the previous example and introduces the use of Gorilla Mux, a popular third-party router package for building HTTP servers in Go. It adds a new endpoint for handling user-related routes with path variables.

### Package and Imports

```go
package main

import (
	"GO-WebServer-Tutorial/06-MultiRoute-GorillaMux-WebServer/countryCapitals"
	"fmt"
	"io"
	"log"
	"math/rand"
	"net/http"
	"strings"
	"time"

	"github.com/gorilla/mux"
)
```

The code begins with the `package main` declaration, indicating that this is the main package of the Go program. It imports the necessary packages: `fmt` for formatting and printing, `io` for input/output operations, `log` for logging, `math/rand` for generating random numbers, `net/http` for building HTTP servers and handling HTTP requests, `strings` for string manipulation, `time` for working with time, and the external package `GO-WebServer-Tutorial/06-MultiRoute-GorillaMux-WebServer/countryCapitals` for retrieving country capital information. Additionally, it imports the Gorilla Mux package `github.com/gorilla/mux`.

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

func handleUsers(w http.ResponseWriter, r *http.Request) {
	vars := mux.Vars(r)
	w.WriteHeader(http.StatusOK)
	fmt.Fprintf(w, "User belongs to category: %v\n", vars["category"])
	fmt.Fprintf(w, "User ID is: %v\n", vars["id"])
}
```

The `home` function is the request handler function for the root URL ("/") and handles the country capital API requests, similar to the previous examples.

The `getServerTime` function is a request handler function for the "/servertime" endpoint. It writes the current server time to the `http.ResponseWriter` using `io.WriteString`.

The `getRandomNumber` function is a request handler function for the "/random" endpoint. It generates a random number using `rand.Int` and writes it to the `http.ResponseWriter` using `fmt.Fprintln`.

The `handleUsers` function is a request handler function for the "/users/{category}/{id:[0-9]+}" endpoint. It uses Gorilla Mux's `mux.Vars` function to extract the path variables (`category` and `id`)

from the URL. It then writes a response with the extracted variables to the `http.ResponseWriter` using `fmt.Fprintf`.

### Main Function

```go
func main() {
	//Introducing NewServeMux for multi-routing
	rtr := mux.NewRouter()

	rtr.HandleFunc("/", home)
	rtr.HandleFunc("/servertime", getServerTime)
	rtr.HandleFunc("/random", getRandomNumber)
	rtr.HandleFunc("/users/{category}/{id:[0-9]+}", handleUsers)
	log.Fatal(http.ListenAndServe(port, rtr))
}
```

The `main` function is the entry point of the Go program. It creates a new instance of Gorilla Mux router using `mux.NewRouter()`.

The `HandleFunc` method of the Gorilla Mux router is used to register the `home` function as the request handler for the root URL ("/"), the `getServerTime` function as the request handler for the "/servertime" endpoint, the `getRandomNumber` function as the request handler for the "/random" endpoint, and the `handleUsers` function as the request handler for the "/users/{category}/{id:[0-9]+}" endpoint.

The server is started using `http.ListenAndServe` with the specified port and the Gorilla Mux router instance (`rtr`) as the handler.

### Usage

To use this code, you need to have Go installed on your machine. Save the code in a file with a `.go` extension, such as `server.go`. Make sure to have the external package `countryCapitals` available in the specified path and the Gorilla Mux package `github.com/gorilla/mux` installed.

Then, navigate to the directory containing the file in your terminal and run the following command:

```bash
cd server
go run main.go
```

The server will start running, and you can access it by opening a web browser and visiting:

- `http://localhost:9000/capital/{country}` to retrieve the capital of a specific country. For example, `http://localhost:9000/capital/USA` will return the capital of the United States.
- `http://localhost:9000/servertime` to retrieve the server's current time.
- `http://localhost:9000/random` to retrieve a random number.
- `http://localhost:9000/users/{category}/{id}` to handle user-related routes. Replace `{category}` and `{id}` with actual values to see the response.

You can stop the server by pressing `Ctrl+C` in the terminal window where it is running.