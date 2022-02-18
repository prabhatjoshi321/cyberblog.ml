---
title: Create Your own private VPN using GCP instance
author:
  name: Wraithdemon
  link: https://github.com/prabhatjoshi321
date: 2022-02-18 20:00:00 +0800
categories: [Tutorial]
tags: [writing]    # TAG names should always be lowercase
---

### Setup a GCP cloud Instance

* Create a GCP clod instance and open any port by going into the firewall ans setting up the Inbound rules for that port.
* The preferred port is UDP port 1194 as it is the default used by the openvpn server.

### Paste the below command in your terminal

```bash
wget https://git.io/vpn -O openvpn-install.sh && bash openvpn-install.sh
```

### Follow Instructions on terminal

* follow on with the steps given by the setup script.
* At the appropriate prompt enter the chosen port. The default will be UDP port 1194. Change it if you decide to use other port.
* Generate the .ovpn key files at the appropriate prompt.

### Generate .ovpn later

* Later if you want to regenerate the .ovpn files rerun the above script and proceed with the prompt.
