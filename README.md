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

    $ sudo vpn-maint-update-controller

### Reset

    $ sudo vpn-maint-reset-controller

## Node

### Apply Changes

    $ sudo vpn-maint-apply-changes

### Update

    $ sudo vpn-maint-stop-node
    $ sudo vpn-maint-update-node
    $ sudo vpn-maint-start-node

### Reset

    $ sudo vpn-maint-apply-changes
