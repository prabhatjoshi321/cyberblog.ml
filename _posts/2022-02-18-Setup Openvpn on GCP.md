---
title: Create Your own private VPN using GCP instance
author:
  name: Wrathdemon
  link: https://github.com/prabhatjoshi321
date: 2022-02-18 20:00:00 +0800
categories: [Tutorial, Instructions, Notes]
tags: [writing]    # TAG names should always be lowercase
---

### Setup a GCP cloud Instance

* Create a GCP cloud instance and open your preferred port by going into the firewall and setting up the Inbound rules for that port.
* The preferred port is UDP port 1194 as it is the default used by the openvpn server.
* If you plan to go with your own choice of ports, remember it as it will be used later.

### Paste the below command in your terminal

```bash
wget https://git.io/vpn -O openvpn-install.sh && bash openvpn-install.sh
```

### Follow Instructions on terminal

* Follow on with the steps given by the setup script.
* At the appropriate prompt enter the chosen protocol and port. The default will be UDP port 1194. Change it if you had decided to use other port.
* Generate the .ovpn key files at the appropriate prompt.

### Generate .ovpn later in time

* Later if you want to regenerate the .ovpn files rerun the above script and proceed with the prompt.
