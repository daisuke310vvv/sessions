# sessions

[![Build Status](https://travis-ci.org/daisuke310vvv/sessions.svg)](https://travis-ci.org/daisuke310vvv/sessions)
[![codecov](https://codecov.io/gh/daisuke310vvv/sessions/branch/master/graph/badge.svg)](https://codecov.io/gh/daisuke310vvv/sessions)
[![Go Report Card](https://goreportcard.com/badge/github.com/daisuke310vvv/sessions)](https://goreportcard.com/report/github.com/daisuke310vvv/sessions)
[![GoDoc](https://godoc.org/github.com/daisuke310vvv/sessions?status.svg)](https://godoc.org/github.com/daisuke310vvv/sessions)
[![Join the chat at https://gitter.im/gin-gonic/gin](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/gin-gonic/gin)

Gin middleware for session management with multi-backend support (currently cookie, Redis, Memcached, MongoDB, memstore).

## Usage

### Start using it

Download and install it:

```bash
$ go get github.com/daisuke310vvv/sessions
```

Import it in your code:

```go
import "github.com/daisuke310vvv/sessions"
```

## Examples

#### cookie-based

[embedmd]:# (example/cookie/main.go go)
```go
package main

import (
	"github.com/daisuke310vvv/sessions"
	"github.com/daisuke310vvv/sessions/cookie"
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()
	store := cookie.NewStore([]byte("secret"))
	r.Use(sessions.Sessions("mysession", store))

	r.GET("/incr", func(c *gin.Context) {
		session := sessions.Default(c)
		var count int
		v := session.Get("count")
		if v == nil {
			count = 0
		} else {
			count = v.(int)
			count++
		}
		session.Set("count", count)
		session.Save()
		c.JSON(200, gin.H{"count": count})
	})
	r.Run(":8000")
}
```

#### Redis

[embedmd]:# (example/redis/main.go go)
```go
package main

import (
	"github.com/daisuke310vvv/sessions"
	"github.com/daisuke310vvv/sessions/redis"
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()
	store, _ := redis.NewStore(10, "tcp", "localhost:6379", "", []byte("secret"))
	r.Use(sessions.Sessions("mysession", store))

	r.GET("/incr", func(c *gin.Context) {
		session := sessions.Default(c)
		var count int
		v := session.Get("count")
		if v == nil {
			count = 0
		} else {
			count = v.(int)
			count++
		}
		session.Set("count", count)
		session.Save()
		c.JSON(200, gin.H{"count": count})
	})
	r.Run(":8000")
}
```

#### Memcached (ASCII protocol)

[embedmd]:# (example/memcached/ascii.go go)
```go
package main

import (
	"github.com/bradfitz/gomemcache/memcache"
	"github.com/daisuke310vvv/sessions"
	"github.com/daisuke310vvv/sessions/memcached"
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()
	store := memcached.NewStore(memcache.New("localhost:11211"), "", []byte("secret"))
	r.Use(sessions.Sessions("mysession", store))

	r.GET("/incr", func(c *gin.Context) {
		session := sessions.Default(c)
		var count int
		v := session.Get("count")
		if v == nil {
			count = 0
		} else {
			count = v.(int)
			count++
		}
		session.Set("count", count)
		session.Save()
		c.JSON(200, gin.H{"count": count})
	})
	r.Run(":8000")
}
```

#### Memcached (binary protocol with optional SASL authentication)

[embedmd]:# (example/memcached/binary.go go)
```go
package main

import (
	"github.com/daisuke310vvv/sessions"
	"github.com/daisuke310vvv/sessions/memcached"
	"github.com/gin-gonic/gin"
	"github.com/memcachier/mc"
)

func main() {
	r := gin.Default()
	client := mc.NewMC("localhost:11211", "username", "password")
	store := memcached.NewMemcacheStore(client, "", []byte("secret"))
	r.Use(sessions.Sessions("mysession", store))

	r.GET("/incr", func(c *gin.Context) {
		session := sessions.Default(c)
		var count int
		v := session.Get("count")
		if v == nil {
			count = 0
		} else {
			count = v.(int)
			count++
		}
		session.Set("count", count)
		session.Save()
		c.JSON(200, gin.H{"count": count})
	})
	r.Run(":8000")
}
```

#### MongoDB

[embedmd]:# (example/mongo/main.go go)
```go
package main

import (
	"github.com/daisuke310vvv/sessions"
	"github.com/daisuke310vvv/sessions/mongo"
	"github.com/gin-gonic/gin"
	"gopkg.in/mgo.v2"
)

func main() {
	r := gin.Default()
	session, err := mgo.Dial("localhost:27017/test")
	if err != nil {
		// handle err
	}

	c := session.DB("").C("sessions")
	store := mongo.NewStore(c, 3600, true, []byte("secret"))
	r.Use(sessions.Sessions("mysession", store))

	r.GET("/incr", func(c *gin.Context) {
		session := sessions.Default(c)
		var count int
		v := session.Get("count")
		if v == nil {
			count = 0
		} else {
			count = v.(int)
			count++
		}
		session.Set("count", count)
		session.Save()
		c.JSON(200, gin.H{"count": count})
	})
	r.Run(":8000")
}
```

#### memstore

[embedmd]:# (example/memstore/main.go go)
```go
package main

import (
	"github.com/daisuke310vvv/sessions"
	"github.com/daisuke310vvv/sessions/memstore"
	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()
	store := memstore.NewStore([]byte("secret"))
	r.Use(sessions.Sessions("mysession", store))

	r.GET("/incr", func(c *gin.Context) {
		session := sessions.Default(c)
		var count int
		v := session.Get("count")
		if v == nil {
			count = 0
		} else {
			count = v.(int)
			count++
		}
		session.Set("count", count)
		session.Save()
		c.JSON(200, gin.H{"count": count})
	})
	r.Run(":8000")
}
```
