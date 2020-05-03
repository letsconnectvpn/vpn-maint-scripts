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

# Automation

If you are on a host where you can do password-less SSH logins and don't need
to provide a password when using `sudo`, you can use the following scripts to
update a bunch of servers at once.

## Single System

If you have multiple servers to maintain, you may want to use a script like 
this.

    #!/bin/sh

    SERVER_LIST="
        vpn.tuxed.net
        vpn-dev.tuxed.net
    "

    for SERVER in ${SERVER_LIST}; do
        echo "*** ${SERVER} ***"
	    ssh "${SERVER}" "/usr/bin/sudo /usr/sbin/vpn-maint-update-system"
    done

## Multiple Systems

**NOTE**: this is UNTESTED! Also, it does not consider the need to reboot the
machines, e.g. when there are kernel or system library updates.

    #!/bin/sh

    CONTROLLER=frkovpn.tuxed.net
    NODES="
	    node-a.frkovpn.tuxed.net
	    node-b.frkovpn.tuxed.net
    "

    # stop all nodes
    for NODE in ${NODES}
    do
	    ssh "${NODE}" "/usr/bin/sudo /usr/sbin/vpn-maint-stop-node"
    done

    # update controller
    ssh "${CONTROLLER}" "/usr/bin/sudo /usr/sbin/vpn-maint-update-controller"

    # update nodes
    for NODE in ${NODES}
    do
	    ssh "${NODE}" "/usr/bin/sudo /usr/sbin/vpn-maint-update-node"
	    ssh "${NODE}" "/usr/bin/sudo /usr/sbin/vpn-maint-start-node"
    done
