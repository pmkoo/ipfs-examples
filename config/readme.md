## The ipfs Config File
ipfs is configured through a json formatted text file, located by default at
`~/.go-ipfs/config`.

### Addresses
The config file stores a few different address types, all of which use the
multiaddr addressing format. Lets go over what each address type means.

```
"Addresses": {
    "Swarm": [
      "/ip4/0.0.0.0/tcp/4001"
    ],
    "API": "/ip4/127.0.0.1/tcp/5001",
    "Gateway": "/ip4/127.0.0.1/tcp/8080"
  }
```

#### Swarm
Swarm addresses are addresses that the local daemon will listen on for
connections from other ipfs peers. You should try to ensure that these
addresses can be dialed from a separate computer and that there are no
firewalls blocking the ports you specify.

#### API
The API address is the address that the daemon will serve the http API from.
This API is used to control the daemon through the command line, or simply
via curl if youre feeling adventurous. You should ensure that this address
is not dialable from outside of your machine, or other potentially malicious
parties may be able to send commands to your ipfs daemon.

#### Gateway
The Gateway address is the address that the daemon will serve the gateway
interface from. The gateway may be used to view files through ipfs, and serve
static web content. This port may or may not be dialable from outside of your
machine, thats entirely up to you. The gateway address is optional, if you
leave it blank, the gateway server will not start.

### Mounts
The mounts config values specifies the default mountpoints for the ipfs and
ipns virtual filesystems, if no other directories are specified by the
`ipfs mount` command. These folders should exist, and have permissions for your
user to be able to mount to them via fuse.

### Bootstrap
The Bootstrap config array specifies the list of ipfs peers that your daemon
will connect to on startup. The default values for this are the 'ipfs solarnet'
nodes, which are a set of VPS servers distributed around the country.
