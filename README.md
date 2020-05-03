This is a collection of scripts for maintaining your VPN server(s).

# Single System

## Apply Changes

    $ sudo vpn-maint-apply-changes

## Update

    $ sudo vpn-maint-update-system

## Reset

    $ sudo vpn-maint-reset-system

# Multiple Systems

## Controller

### Update

**NOTE**: first stop the node(s)!

    $ sudo vpn-maint-update-controller

### Reset

    $ sudo vpn-maint-reset-controller

## Node

### Apply Changes

    $ sudo vpn-maint-apply-changes

### Update

**NOTE**: after stopping the node(s) update the controller!

    $ sudo vpn-maint-stop-node

After stopping the node(s), update the controller first before continuing!

    $ sudo vpn-maint-update-node
    $ sudo vpn-maint-start-node

### Reset

There is no need to reset the node(s) as applying changes takes care of that
already.

    $ sudo vpn-maint-apply-changes
