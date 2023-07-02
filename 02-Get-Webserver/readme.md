## Documentation: Simple HTTP Server with Dynamic Routing

This code is an extension of the previous example, where a simple HTTP server is created. In this version, the server implements dynamic routing based on the URL path. It uses an external package called "countryCapitals" to retrieve capital names based on country names.

### Package and Imports

```go
package main

import (
	"Go-WebServer-Tutorial/02-Get-Webserver/countryCapitals"
	"fmt"
	"net/http"
	"strings"
)
```

The code begins with the `package main` declaration, indicating that this is the main package of the Go program. It imports the necessary packages: `fmt` for formatting and printing, `net/http` for building HTTP servers and handling HTTP requests, and the external package `Go-WebServer-Tutorial/02-Get-Webserver/countryCapitals` for retrieving country capital information.

### Constants

```go
const port = ":9000"
```

The code declares a constant variable `port` and assigns the value `:9000` to it. This constant represents the port on which the server will listen for incoming HTTP requests.

### Request Handler Function

```go
func home(w http.ResponseWriter, r *http.Request) {
	urlPathElements := strings.Split(r.URL.Path, "/")

	if urlPathElements[1] == "capital" {
		capital := countryCapitals.Capitals[urlPathElements[2]]

		//If no match found, an empty string is returned. Use it for validation!
		if capital != "" {
			fmt.Fprintf(w, capital)
		} else {
			fmt.Fprintf(w, "Sorry! No capital found for this country.")
		}
	}
}
```

The `home` function is the request handler function, which will be invoked whenever a request is made to the server. It takes two arguments: `w`, an `http.ResponseWriter` object used to send the response back to the client, and `r`, an `*http.Request` object representing the incoming request.

In this function, the URL path is split using `strings.Split` to extract individual path elements. If the second element of the path is "capital", it indicates that a request for a country's capital is being made.

The `countryCapitals.Capitals` map is then used to retrieve the capital for the country specified in the third path element. If a match is found, the capital is written to the `http.ResponseWriter` using `fmt.Fprintf`. If no match is found, an appropriate error message is returned.

### Main Function

```go
func main() {
	http.HandleFunc("/", home)
	http.ListenAndServe(port, nil)
}
```

The `main` function is the entry point of the Go program. It sets up the server by registering the `home` function as the request handler for the root URL ("/") using `http.HandleFunc`.

The `http.ListenAndServe` function starts the server and listens for incoming HTTP requests on the specified `port`. The second argument, `nil`, indicates that the default server configuration should be used.

### Usage

To use this code, you need to have Go installed on your machine. Save the code in a file with a `.go` extension, such as `server.go`. Make sure to have the external package `countryCapitals` available in the specified path.

Then, navigate to the directory containing the file in your terminal and run the following command:

```bash
cd server
go run main.go
```

The server will start running, and you can access it by opening a web browser and visiting `http://localhost:9000/capital/{country}`, where `{country}` is the name of the country for which you want to retrieve the capital

. If a match is found, the browser will display the corresponding capital. If no match is found, an error message will be shown.

You can stop the server by pressing `Ctrl+C` in the terminal window where it is running.