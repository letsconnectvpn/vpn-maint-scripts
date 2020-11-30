**Summary**: Collection of scripts to maintain your VPN server

**Description**: This is a collection of scripts for maintaining your VPN 
server(s). These SHOULD be used for handling day to day operation, like 
applying changes and installing updates. 

**License**:  AGPL-3.0-or-later

# Introduction

This is a collection of scripts for maintaining your VPN server(s). These 
SHOULD be used for handling day to day operation, like applying changes and 
installing updates. Feel free to take the content of these scripts and use them
in something like [Ansible](https://www.ansible.com/).

**NOTE**: only use these scripts during _maintenance windows_ as they will in 
most cases restart the OpenVPN processes!

# Single System

## Verify Configuration Files

In order to make sure your VPN configuration files are without syntax errors:

    $ sudo vpn-maint-verify-config

Run this after every configuration change!

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

**NOTE**: make sure you are NOT connected to the VPN server itself when running
these scripts as they will terminate the connection!

## Single System

If you have multiple servers to maintain, you may want to use a script like 
this. Use the `--reboot` flag to reboot the servers after updating...

    #!/bin/sh

    SERVER_LIST="
        vpn.tuxed.net
        vpn-dev.tuxed.net
    "

    for SERVER in ${SERVER_LIST}; do
        echo "*** ${SERVER} ***"
        ssh -t "${SERVER}" "/usr/bin/tmux new '/usr/bin/sudo /usr/sbin/vpn-maint-update-system'"
        if [ "--reboot" = "${1}" ]; then
            echo "Rebooting SERVER ${SERVER}..."
	        ssh "${SERVER}" "/usr/bin/sudo /sbin/reboot"
        fi
    done

## Multiple Systems

This stops the nodes, updates the controller, node(s) and starts the node(s) 
again. If you want to reboot the controller and node(s), use the `--reboot` 
flag. Modify `REBOOT_TIME_CONTROLLER` if you want to give your controller more
time to recover from a reboot...

    #!/bin/sh

    CONTROLLER=frkovpn.tuxed.net
    NODES="
        node-a.frkovpn.tuxed.net
        node-b.frkovpn.tuxed.net
    "

    REBOOT_TIME_CONTROLLER=180

    # stop all nodes
    for NODE in ${NODES}
    do
        echo "Stopping NODE ${NODE}..."
        ssh -t "${NODE}" "/usr/bin/tmux '/usr/bin/sudo /usr/sbin/vpn-maint-stop-node'"
    done

    # update controller
    echo "Updating CONTROLLER ${CONTROLLER}..."

    ssh -t "${CONTROLLER}" "/usr/bin/tmux new '/usr/bin/sudo /usr/sbin/vpn-maint-update-controller -y'"
    if [ "--reboot" = "${1}" ]; then
    echo "Rebooting CONTROLLER ${CONTROLLER}..."
        ssh "${CONTROLLER}" "/usr/bin/sudo /sbin/reboot"
        echo "Waiting ${REBOOT_TIME_CONTROLLER}s for CONTROLLER ${CONTROLLER} to come back..."
        sleep ${REBOOT_TIME_CONTROLLER}
    fi

    # update nodes
    for NODE in ${NODES}
    do
        echo "Updating NODE ${NODE}..."
        ssh -t "${NODE}" "/usr/bin/tmux new '/usr/bin/sudo /usr/sbin/vpn-maint-update-node'"
        if [ "--reboot" = "${1}" ]; then
            echo "Rebooting NODE ${NODE}..."
            ssh "${NODE}" "/usr/bin/sudo /sbin/reboot"
        else
            echo "Starting NODE ${NODE}..."
            ssh -t "${NODE}" "/usr/bin/tmux new '/usr/bin/sudo /usr/sbin/vpn-maint-start-node'"
        fi
    done
