BGP Peer Demo
=============

This is a rough example of setting up BGP peering using [Quagga](https://www.quagga.net/) and [ExaBGP](https://github.com/Exa-Networks/exabgp). I also tried using [GIXLG](https://github.com/dpiekacz/gixlg), primarily as a way to test if ExaBGP was processing received routes.

I barely know anything about BGP so if you see something crazy here, please let me know.

I don't even know if this works, I'm just trying to keep track of the steps I've taken. So far I have 2 hosts that establish a connection and Quagga appears to be able to send routes to ExaBGP (though I don't know what it does with them). ExaBGP can send routes to Quagga, and I can't tell that Quagga cares. I really don't know what I'm doing.

Instructions
------------

Set up 2 Debian VMs. I used VirtualBox with the Bridged Adapter with the following IPs:

* Quagga: 192.168.0.100
* ExaBGP: 192.168.0.200

Run the ansible playbook:

    ansible-playbook -i hosts --user --ask-pass site.yml

Run exabgp on 192.168.0.200:

    exabgp -d /opt/gixlg/exabgp/sample.conf

From a separate terminal on 192.168.0.200 you can view the connection status or announce a route:

    exabgpcli show neighbor summary
    exabgpcli announce route 192.168.43.0/24 next-hop 192.168.0.100


On 192.168.0.100:

    vtysh
    debian# configure terminal
    debian(config)# router bgp 65321
    debian(config-router)# network 192.168.99.0/24
    debian(config-router)# exit
    debian(config)# exit
    debian# write
    debian# exit

This route should be announced to ExaBGP.