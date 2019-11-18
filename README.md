# SaltConf19-NetOps1
Course Material for SaltConf19's intermediate-level networking lab

## Logging into your master and router
This class employs a jump box that is preconfigured to provide access to your networking lab.  To begin, use any terminal emulator to log in to the lab jump box using the IP, username, and credentials provided during the presentation.

Configuring Salt for NetOps: Arista and NAPALM
- we will use pip to bring in many python dependencies
pip install pyeapi - works great!
pip install napalm - error!
    `error in ncclient setup command: 'install_requires' must be a string or list of strings containing valid project/version requirement specifiers`
- yum brings in an ancient python-setuptools.  update it.
pip install -U setuptools
pip install napalm - another error!
    `ERROR: Cannot uninstall 'requests'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.`
- yum has given us another gift, in the form of an unresolvable dependency
rpm -e --nodeps python-requests
pip install requests
pip install napalm - finally (still a problem with junos-eznc but we won't be using netconf)

mkdir /srv/pillar
vi /srv/pillar/top.sls
vi /srv/pillar/pyeapi.sls
vi /srv/pillar/napalm.sls

ssh ec2-user@veos
enable
conf t
username admin secret 0 SaltConf19
management api http-commands
no shutdown
protocol http
exit
exit
write
exit



salt-proxy --proxyid=pyeapi -l debug
  should see in there somewhere `[INFO    ] Running scheduled job: __mine_interval`
salt-proxy --proxyid=pyeapi -d
salt-proxy --proxyid=napalm -l debug
  hopefully you get `[DEBUG   ] eapi_response: {u'jsonrpc': u'2.0', u'id': u'139930508619856', u'result': [...`
salt-proxy --proxyid=napalm -d

salt pyeapi pyeapi.run_commands 'show version'
salt napalm napalm.cli 'show version'

Next Steps
Using information from a CMDB (e.g. Netbox as an external pillar)

## Packages you need
pyeapi
requests (from pip, not yum)
oh yeah, pip

## Call to Action
