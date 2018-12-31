# Harald-Berghoff/fedora-cups-airprint
Docker image for CUPS intended as an AirPrint relay on QNAP TS, based on Fedora 

(as a new implementation from [quadportnick/docker-cups-airprint][https://github.com/quadportnick/docker-cups-airprint])

This Fedora-based Docker image runs a CUPS instance that is meant as an AirPrint relay for printers that are already on the network but not AirPrint capable. After updating the Ubuntu base as a from quadportnick/cups-airprint, who is using it on a Synology NAS, I wanted to use Fedora as a means of getting a Samsung CLP-310 printer. I am lacking the knowledge to get the foomatic driver up and running on the Ubuntu image, so I choosed to give it a go with a Fedora base.

The local Avahi will be utilized for advertising the printers on the network.

Hopefully someone else finds this useful. Credits go to quadportnick.

## Prereqs
* No other printers should be shared under Control Panel>External Devices>Printer so that the DSM's CUPS is not running. 
* `Enable Bonjour service discovery` needs to be marked under Control Panel>Network>DSM Settings 

## Configuration

### Volumes:
* `/config`: where the persistent printer configs will be stored
* `/services`: where the Avahi service files will be generated

### Variables:
* `CUPSADMIN`: the CUPS admin user you want created
* `CUPSPASSWORD`: the password for the CUPS admin user

### Ports:
* `631`: the TCP port for CUPS must be exposed

## Using
To start the image either use the GUI of your NAS or use on the commandline:

`docker run -p <<hostport>>:631 --env CUPSADMIN=<<adminuser>> --env CUPSPASSWORD=<<adminpwd>> fedora-cups-airprint:latest`

It is recommended to use also port 631 as the hostport, as this is expected by some other software.

CUPS will be configurable at http://[yourNAS]:<<hostport>>. using the CUPSADMIN/CUPSPASSWORD when you do something administrative.

If the `/services` volume isn't mapping to `/etc/avahi/services` then you will have to manually copy the .service files to that path at the command line.

## Notes
* CUPS doesn't write out `printers.conf` immediately when making changes even though they're live in CUPS. Therefore it will take a few moments before the services files update
* Don't stop the container immediately if you intend to have a persistent configuration for this same reason
 
