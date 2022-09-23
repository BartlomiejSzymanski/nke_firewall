# PZSP2 Firewall
![nke](https://user-images.githubusercontent.com/83136462/187739878-e31f592d-5b07-496c-a6c1-373b4162c519.png)

Repository of PZSP2 (Team Project #2) project. Our team- NKE consisted of four contributors:


## TL;DR
Raspberry Firewall for SLMP and MODBUS protocols along with interactive Web interface. This package as a whole enables  administrators to selectively allow reading and writing from existing registers of the industrial system. 

<img width="1193" alt="Screenshot 2022-09-22 at 15 49 50" src="https://user-images.githubusercontent.com/83136462/191764935-1097f215-f4e5-4e7b-a725-4a93f97b02a9.png">

Software structure comprises two main modules, separated from each other for additional safety. In case od DoS attacks, the web interface (CONF module) is the only one vulnerable to shutdown. The firewall itself (FIRE module) intercepts the system FORWARD queue and filters unwanted packets. Any update within the rules is being signalised to the Firewall module using a "named pipeline", allowing for complete process separation of the FIRE and CONF modules.

<img width="1010" alt="Screenshot 2022-09-22 at 15 50 28" src="https://user-images.githubusercontent.com/83136462/191765074-42ca7bde-d280-405c-9bcb-178b6d4461f7.png">


## Short Presentation video



https://user-images.githubusercontent.com/83136462/187864406-feed3810-e762-46e0-9a1e-cd7bad9d1561.mov



## Installation
**Instalation manual as for _0791ef9_ commit**

### Server & Client

#### SLMP Simulators
SLMP Simulators are accessible in /materials/SLMP directory.
**ATTENTION1** Simulator requires python2

#### Modbus Simulators
MODBUS/TCP Simulators are accessible in /code/tests directory.
To use those, one shall execute command **pip install -Ur code/tests/requirements.txt**
**ATTENTION1** Simulator requires python3

### RaspberryPi
#### System setup
On RaspberryPi shall be installed python3 interpreter along with iptables packet - if using Rasbian distribution the superuser command **apt-get install python3 iptables** shall be sufficient.
To allow forwarding go to /etc/sysctl.conf and uncomment __net.ipv4.ip_forward=1__ line.
Be sure to preform next steps having brought up interaces - you can use command **ip link set dev <interface name> up**.
To create bridge forwarding packets between eth0 and eth1 do as below:
    1. Run as superuser **apt-get install bridge-utils**
    2. Run as superuser **brctl addbr <bridge name>**
    3. Run as superuser **ip addr add 192.168.1.55/24 dev <bridge name>**
    3. Run as superuser **brctl addif <bridge name> eth0 eth1**
    4. Add following line to /etc/dhcpcd.conf file:
        __denyinterfaces eth0 eth1__
    5. Add following lines to /etc/network/interfaces file:
        __auto <bridge name>__
        __iface <bridge name> inet manual__
        __bridge_ports eth0 eth1__
    6. Reboot

#### Python environment setup
In order to set up working environment, run as superuser commands:
**sh code/flask/setup_frontend.sh** - the command sets up Configuration module
**pip3 install -Ur code/requirements.txt** - the command sets up Fire module & unit tests

## Running
**ATTENTION!** all below actions shall be taken after the physical connection of both Client and Server to RaspberryPi.
### Setting up Client
1. Configure static IP
To do so, edit the dhcpcd.conf file or use your OS GUI and set it to 192.168.2.2/24.
2. Configure routing table
To do so, run as superuser script from repository /configuration/ClientSetup.sh.
**ATTENTION!** after running the script you are very likely to loose your internet connection

### Setting up Server
1. Configure static IP
To do so, edit the dhcpcd.conf file or use your OS GUI and set it to 192.168.1.2/24.
2. Configure routing table
To do so, run as superuser script from repository /configuration/ServerSetup.sh.
**ATTENTION!** after running the script you are very likely to loose your internet connection

### Setting up Firewall on RaspberryPi
1. Configure static IP
To do so, edit the dhcpcd.conf file.
Client Interface (Eth0) shall be configured to static IP address of 192.168.2.1/24.
Server Interface (Eth1) shall be configured to static IP address of 192.168.1.1/24.
2. Configure routing table
To do so, run as superuser script on repository /configuration/FirewallSetup.sh from main repository directory.
**ATTENTION!** after running the script you are very likely to loose your internet connection
**ATTENTION!** after running the script, the FIRE module will start automatically

### Enabeling Configuration module on RaspberryPi
1. Running the configuration module
To run Configuration module, run from main repository directory command **python3 code/flask/view.py**
2. Accessing the configuration module
To access configuration module, open in Web Browser the IP:5000 as visible after running previous command.

### Visualisation
![Visualisation of network build by scripts](doc/stage3/Fire_schema.drawio\ (2).png)

## Usage
To use product, physically connect ethernet wire to Eth0 interface of Raspberry to Client and Eth1 to Server. Next proceed as in **Instalation** and **Running** sections.

## Test
To test, run as superuser command **coverage run --source=. -m unittest discover -s code/tests/** from main repository directory.

## Support
Support availible exclusively on demand as the product comes with no warranty ("as is"). If having minor issues feel free to contact corresponding author.

## Authors and acknowledgment
Corresponding author: 
Bartłomiej Szymański (bartek.szymanski9204@gmail.com)
Richard Staszkiewicz (richard.staszkiewicz@gmail.com)
Szymon Dyszewski
Jakub Jabłoński
Bartłomiej Kosiński


*We would like to thank Professor Krzysztof Cabaj for inspiration and firm grip on project functionality aspects. Additionaly we acknowledge our grattitude towards PZSP2 expert panel, namely Jarosław Chudziak, Tomasz Kruk and Robert Nowak, for their valuable remarks along with our PZSP2 coordinator, Klara Borowa, for her personal engagement and help with timing management.*

## License
The project is availible under MIT open license.

## Project status
Projects aims for improvment of the infrastructure from router to switch. Currently no longer in development.
