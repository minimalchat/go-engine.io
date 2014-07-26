# go-engine.io

[![GoDoc](http://godoc.org/github.com/googollee/go-engine.io?status.svg)](http://godoc.org/github.com/googollee/go-engine.io)  [![Build Status](https://travis-ci.org/googollee/go-engine.io.svg)](https://travis-ci.org/googollee/go-engine.io)

go-engine.io is the implement of engine.io in golang. It supported long-polling and websocket transport.

It is compatible with node.js implement.

## Install

Install the package with:

```bash
go get github.com/googollee/go-engineio
```

Import it with:

```go
import "github.com/googollee/go-engineio"
```

and use `engineio` as the package name inside the code.

## Example

Please check example folder for details.

```go
package main

import (
	"io/ioutil"
	"log"
	"net/http"

	"github.com/googollee/go-engine.io"
)

func main() {
	server := engineio.NewServer(engineio.DefaultConfig)

	go func() {
		for {
			conn, _ := server.Accept()
			go func() {
				defer conn.Close()
				for i := 0; i < 10; i++ {
					t, r, _ := conn.NextReader()
					b, _ := ioutil.ReadAll(r)
					r.Close()
					log.Println(t, string(b))
					w, _ := conn.NextWriter(engineio.MessageText)
					w.Write([]byte("pong"))
					w.Close()
				}
			}()
		}
	}()

	http.Handle("/engine.io/", server)
	http.Handle("/", http.FileServer(http.Dir("./asset")))
	log.Println("Serving at localhost:5000...")
	log.Fatal(http.ListenAndServe(":5000", nil))
}
```

## License

The 3-clause BSD License  - see LICENSE for more details