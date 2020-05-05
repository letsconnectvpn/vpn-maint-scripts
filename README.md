# Introduction

This is a collection of scripts for maintaining your VPN server(s). These 
SHOULD be used for handling day to day operation, like applying changes and 
installing updates. Feel free to take the content of these scripts and use them
in something like Ansible.

**NOTE**: only use these scripts during _maintenance windows_ as they wil in 
most cases restart the OpenVPN processes!

# Single System

## Apply Changes

(Re)write OpenVPN server configuration files based on updated configuration and 
restart OpenVPN:

    $ sudo vpn-maint-apply-changes

## Update

Install OS and VPN software updates:

    $ sudo vpn-maint-update-system

## Reset

**NOTE**: only use this right before going to production to get rid of all 
data!

    $ sudo vpn-maint-reset-system

# Multiple Systems

See the scripts under [Automation](#automation) to see the order in which to
execute the scripts.

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
this. **NOTE**: this does not consider the need to reboot the server(s).

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

**NOTE**: this does not consider the need to reboot the server(s).

    #!/bin/sh

    CONTROLLER=frkovpn.tuxed.net
    NODES="
        node-a.frkovpn.tuxed.net
        node-b.frkovpn.tuxed.net
    "

    # stop all nodes
    for NODE in ${NODES}
    do
        echo "Stopping NODE ${NODE}..."
        ssh "${NODE}" "/usr/bin/sudo /usr/sbin/vpn-maint-stop-node"
    done

    # update controller
    echo "Updating CONTROLLER ${CONTROLLER}..."
    ssh "${CONTROLLER}" "/usr/bin/sudo /usr/sbin/vpn-maint-update-controller -y"

    # update nodes
    for NODE in ${NODES}
    do
        echo "Updating NODE ${NODE}..."
        ssh "${NODE}" "/usr/bin/sudo /usr/sbin/vpn-maint-update-node"
        echo "Starting NODE ${NODE}..."
        ssh "${NODE}" "/usr/bin/sudo /usr/sbin/vpn-maint-start-node"
    done

