## Random Ipfs Objects
During the development, ive frequently found the need to just get a hash of some
random ipfs object. At first, I would just ask in irc "can someone give me a
hash?". But I decided I could do better, So I decided to make it a service. In
this article, im going to go over how I did that (hint: its really simple!)

First, lets get some imports:
```
package main

import (
	"io"
	"net/http"
	"os"

	"github.com/jbenet/go-ipfs/core"
	"github.com/jbenet/go-ipfs/core/coreunix"
	"github.com/jbenet/go-ipfs/repo/fsrepo"
	u "github.com/jbenet/go-ipfs/util"

	"code.google.com/p/go.net/context"
)
```

This just pulls in some basic ipfs packages, and the default golang http server.
Now, since im lazy, im going to have a global for our ipfsnode.

```
var gnode *core.IpfsNode
```

Now, lets write the http handler func for generating our random objects.

```
func ServeIpfsRand(w http.ResponseWriter, r *http.Request) {
	read := io.LimitReader(u.NewTimeSeededRand(), 2048)

	str, err := coreunix.Add(gnode, read)
	if err != nil {
		w.WriteHeader(504)
		w.Write([]byte(err.Error()))
	} else {
		w.Write([]byte(str))
	}
}
```

And now, lets tie it all together in a main function.

```
func main() {
	builder := core.NewNodeBuilder().Online()
```

Set up our builder, and use the users standard ipfs configuration directory.

```
	r := fsrepo.At("~/.go-ipfs")
	if err := r.Open(); err != nil {
		panic(err)
	}

	builder.SetRepo(r)
```

Now we need to set up our context and finally build our node!

```
	// Make our 'master' context and defer cancelling it
	ctx, cancel := context.WithCancel(context.Background())
	defer cancel()

	node, err := builder.Build(ctx)
	if err != nil {
		panic(err)
	}

	// Set the global node for access in the handler
	gnode = node

	http.HandleFunc("/ipfsobject", ServeIpfsRand)
	http.ListenAndServe(":8080", nil)
}
```
