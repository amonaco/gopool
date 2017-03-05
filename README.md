# pool - A generic resource pool for golang

Forked from braindev/gopool since needed to be able to pass variables to the create anonymous function, this becomes useful in an scenario of using Redis sharding together with connection pools combined, something surprisingly Redis libraries don't offer, as they offer pooling and sharding as separate features. I need them combined.

### Example Usage

```go
package main

import "pool"

func main() {
    create := func() (interface{}) {
        return "whatever interface you want" // return a resource
    }
    destroy := func(resource interface{}) {
        // callback for cleanup, you can also use nil in Initialize
    }

    // create a pool named "localhost:6379" with min 5 and max 10 resources
    pool.Initialize("localhost:6379", 5, 10, create, destroy) 
    pool.Initialize("localhost:6380", 5, 10, create, destroy) 
    pool.Initialize("localhost:6381", 5, 10, create, destroy) 
    // you get the idea, if you're using sharding you'd need a pool for each shard

    // in your coroutine
    resource := pool.Name("localhost:6379") // or any other string will do

    /* do something with this resource */

    // you can release it afterwards
    rpool.Release(resource)

    // or call a function that iterates all and release them
    rpool.Drain() // free up all resources
}
```

### Features

- Simple interface
- Pass argument to a creation function
- Very little code
- Lazy creation of resources (create resources when needed, but keep a min on hand)

