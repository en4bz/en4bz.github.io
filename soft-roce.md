# Soft ROCE on AWS

 1. Install ```sudo apt-get install linux-modules-extra-`uname -r` ```. This installs the `rdma_rxe` (soft-roce) module.
 
 2. Install `sudo apt-get install perftest infiniband-diags`. This installs IB test and perf tools.
 
 3. Append the following to `/etc/modules` to load the RDMA modules on boot.
 
 ```
 rdma_rxe
 rdma_cm
 rdma_ucm
 ```
 
 4. Edit `/etc/security/limits.conf` to enable locking larger amounts of memory.
 
 ```
 * soft memlock unlimited
 * hard memlock unlimited
 ```
 
 5. `reboot`
 
 6. Use `ifconfig` to determine the name of your network adapter. On this machine it's `ens5`.

 ```
ubuntu@ip-172-30-0-253:~$ ifconfig 
ens5: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 9001
        inet 172.30.0.253  netmask 255.255.255.0  broadcast 172.30.0.255
        inet6 fe80::10ab:f3ff:fe17:703d  prefixlen 64  scopeid 0x20<link>
        ether 12:ab:f3:17:70:3d  txqueuelen 1000  (Ethernet)
        RX packets 15325  bytes 20380679 (20.3 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 5675  bytes 777541 (777.5 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
 
 7. As root run `echo X > /sys/modules/rdma_rxe/parameters/add` where X is the network adapter name found in step 6.
 
 8. Run `ibstat` to ensure the adapater is configured properly. State should be `Active`.
 
 ```
ubuntu@ip-172-30-0-253:~$ ibstat
CA 'rxe0'
	CA type: 
	Number of ports: 1
	Firmware version: 
	Hardware version: 
	Node GUID: 0x268a07fffe066e8a
	System image GUID: 0x0000000000000000
	Port 1:
		State: Active
		Physical state: LinkUp
		Rate: 100
		Base lid: 0
		LMC: 0
		SM lid: 0
		Capability mask: 0x00810000
		Port GUID: 0x268a07fffe066e8a
		Link layer: Ethernet
```

 9. Use `ib_send_bw` to test the connection and speed.
 
    * Server: `ib_send_bw -aF`
  
    * Client: `ib_send_bw -aF <client_ip>`
