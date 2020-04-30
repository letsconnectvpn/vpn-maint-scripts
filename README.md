This is a collection of scripts for maintaining your VPN server(s).

**NOTE**: run the commands as `root` or use `sudo`!

# Single System

On a single system, i.e. you are using only one physical machine or VM you 
can use the following scripts:

| Script                    | Function                                                                               |
|-------------------------- | -------------------------------------------------------------------------------------- |
| `vpn-maint-apply-changes` | Apply all outstanding configuration changes for the OpenVPN daemon and restart OpenVPN |
| `vpn-maint-update-system` | Stop services, install updates, update OpenVPN configuration, start services           |
| `vpn-maint-reset-system`  | Full *data* RESET. Configuration remains! Users deleted, New CA, new OAuth key, ...    |

The `vpn-maint-reset-system` script is useful when you are testing with your 
VPN server install and want to go to production. It will keep the server 
configuration, but throw away all users, reset the CA and OAuth key. Clients 
will be forced to authorize/authenticate again.

# Controller / Node(s)

When having a controller and node(s), you use the following scripts:

## Controller

**NOTE**: first stop all nodes by running `vpn-maint-stop-node` on all nodes!

| Script                        | Function                                            |
|------------------------------ | --------------------------------------------------- |
| `vpn-maint-update-controller` | Stop services, install all updates, restart service |

## Node

**NOTE**: after stopping all nodes, update the controller first by running 
`vpn-maint-update-controller` on the controller. Then run 
`vpn-maint-update-node` and then `vpn-maint-start-node`.

| Script                    | Function                                                                               |
|-------------------------- | -------------------------------------------------------------------------------------- |
| `vpn-maint-apply-changes` | Apply all outstanding configuration changes for the OpenVPN daemon and restart OpenVPN |
| `vpn-maint-stop-node`     | Stop OpenVPN processes                                                                 |
| `vpn-maint-update-node`   | Install updates, update OpenVPN configuration                                          |
| `vpn-maint-start-node`    | Start OpenVPN processes                                                                |
