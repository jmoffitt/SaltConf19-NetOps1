# SaltConf19-NetOps1
Course Material for SaltConf19's intermediate-level networking lab

## Logging into your master and router
This class employs a jump box that is preconfigured to provide access to your networking lab.  To begin, use any terminal emulator to log in to the lab jump box using the IP, username, and credentials provided during the presentation.

## Configuring Salt for NetOps: Arista and NAPALM
- We will use pip (already installed) to bring in many python dependencies.
`pip install pyeapi` - works great!
`pip install napalm` - error!

```error in ncclient setup command: 'install_requires' must be a string or list of strings containing valid project/version requirement specifiers```
    
- yum brings in an ancient python-setuptools.  Update it.
`pip install -U setuptools` - Yes, it was really old.
`pip install napalm` - another error!

```ERROR: Cannot uninstall 'requests'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.```

- yum has given us another gift, this time in the form of an unresolvable dependency.  Let's kill it and get a non-distro version.
`rpm -e --nodeps python-requests`
`pip install requests`
`pip install napalm` - finally! (still a problem with junos-eznc but we won't be using netconf)

## Let's get salt ready for action
- Files are located in this repo if you don't like typing.
`mkdir /srv/pillar`
`vi /srv/pillar/top.sls`
`vi /srv/pillar/pyeapi.sls`
`vi /srv/pillar/napalm.sls`

## Handling user and API configuration for vEOS
`ssh ec2-user@veos`
```enable
conf t
username admin secret 0 SaltConf19
management api http-commands
no shutdown
protocol http
exit
exit
write
exit```

## Starting and Troubleshooting Proxy Minions
`salt-proxy --proxyid=pyeapi -l debug` -- any problems?
- A good thing to see is:
```[INFO    ] Running scheduled job: __mine_interval```
`salt-proxy --proxyid=pyeapi -d` -- unless you like it in the foreground
`salt-proxy --proxyid=napalm -l debug`
- Hopefully you get:
```[DEBUG   ] eapi_response: {u'jsonrpc': u'2.0', u'id': u'139930508619856', u'result': [...```
`salt-proxy --proxyid=napalm -d`

## Maybe the execution modules just work
`salt pyeapi pyeapi.run_commands 'show version'`
`salt napalm napalm.cli 'show version'`

## Next Steps
- Using information from a CMDB (e.g. Netbox as an external pillar)
- Building States and templates:
  - States:
    - Generate expected configuration
    - Compare to running configuration
    - Remediate if necessary
  - Templates:
    - Resolve language differences between vendors, so that your states are vendor agnostic
- Take over the world!

## What about SaltStack Enterprise?
- We're working on considerably simplifying:
  - Creation and designation of proxy hosts
  - Database-backed enrollment of proxy minions (with CMDB import support)
  - Proxy process toggling and status

## Call to Action
- NetOps content for Salt is as strong as you make it!  Both the open and Enterprise sides of the Salt+NetOps need your input!
