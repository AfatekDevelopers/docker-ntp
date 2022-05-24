## How to Run this container

### Running from Docker Hub

Pull and run -- it's this simple.

```
# pull from docker hub
$> docker pull devhsmtek/ntpserver

# run ntpserver
$> docker run --name=ntpserver             \
              --restart=always       \
              --detach=true          \
              --publish=123:123/udp  \
              --cap-add=SYS_TIME     \
              devhsmtek/ntpserver
```


### Building and Running with Docker Compose

Using the docker-compose.yml file included in this git repo, you can build the container yourself (should you choose to).
*Note: this docker-compose files uses the `3.4` compose format, which requires Docker Engine release 17.09.0+

```
# pull from docker hub
$> docker pull devhsmtek/ntpserver

# build ntpserver
$> docker-compose build ntpserver

# run ntpserver
$> docker-compose up -d ntpserver

# (optional) check the ntpserver logs
$> docker-compose logs ntpserver
```


### Building and Running with Docker Engine

Using the vars file in this git repo, you can update any of the variables to reflect your
environment. Once updated, simply execute the build then run scripts.

```
# build ntpserver
$> ./build.sh

# run ntpserver
$> ./run.sh
```


## Configure NTP Servers

By default, this container uses CloudFlare's time server (time.cloudflare.com). If you'd
like to use one or more different NTP server(s), you can pass this container an `NTP_SERVERS`
environment variable. This can be done by updating the [vars](vars), [docker-compose.yml](docker-compose.yml)
files or manually passing `--env=NTP_SERVERS="..."` to `docker run`.

Below are some examples of how to configure common NTP Servers.

Do note, to configure more than one server, you must use a comma delimited list WITHOUT spaces.

```
# (default) cloudflare
NTP_SERVERS="time.cloudflare.com"

# google
NTP_SERVERS="time1.google.com,time2.google.com,time3.google.com,time4.google.com"

# alibaba
NTP_SERVERS="ntp1.aliyun.com,ntp2.aliyun.com,ntp3.aliyun.com,ntp4.aliyun.com"
```


## Testing your NTP Container

From any machine that has `ntpdate` you can query your new NTP container with the follow
command:

```
$> ntpdate -q <DOCKER_HOST_IP>
```


Here is a sample output from my environment:

```
$> ntpdate -q 10.13.13.9
server 10.13.1.109, stratum 4, offset 0.000642, delay 0.02805
14 Mar 19:21:29 ntpdate[26834]: adjust time server 10.13.13.109 offset 0.000642 sec
```


If you see a message, like the following, it's likely the clock is not yet synchronized.
You should see this go away if you wait a bit longer and query again.
```
$> ntpdate -q 10.13.13.9
server 10.13.13.9, stratum 16, offset 0.005689, delay 0.02837
11 Dec 09:47:53 ntpdate[26030]: no server suitable for synchronization found
```

To see details on the ntp status of your container, you can check with the command below
on your docker host:
```
$> docker exec ntpserver chronyc tracking
Reference ID    : D8EF2300 (time1.google.com)
Stratum         : 2
Ref time (UTC)  : Sun Mar 15 04:33:30 2020
System time     : 0.000054161 seconds slow of NTP time
Last offset     : -0.000015060 seconds
RMS offset      : 0.000206534 seconds
Frequency       : 5.626 ppm fast
Residual freq   : -0.001 ppm
Skew            : 0.118 ppm
Root delay      : 0.022015510 seconds
Root dispersion : 0.001476757 seconds
Update interval : 1025.2 seconds
Leap status     : Normal
```


Here is how you can see a peer list to verify the state of each ntp source configured:
```
$> docker exec ntpserver chronyc sources
210 Number of sources = 2
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^+ time.cloudflare.com           3  10   377   404   -623us[ -623us] +/-   24ms
^* time1.google.com              1  10   377  1023   +259us[ +244us] +/-   11ms
```


Finally, if you'd like to see statistics about the collected measurements of each ntp
source configured:
```
$> docker exec ntpserver chronyc sourcestats
210 Number of sources = 2
Name/IP Address            NP  NR  Span  Frequency  Freq Skew  Offset  Std Dev
==============================================================================
time.cloudflare.com        35  18  139m     +0.014      0.141   -662us   530us
time1.google.com           33  13  128m     -0.007      0.138   +318us   460us
```

## Geliştirici Bilgileri:
<img src="https://github.com/HsmTeknoloji/companyfiles/blob/master/hsmtek-logo.png?raw=true" width="200"/>
Web Site        : www.hsmteknoloji.com <br />
Developer Groups : https://t.me/HsmTeknoloji/ <br />

## Not:
Alıntı [docker-ntp](https://github.com/cturra/docker-ntp)