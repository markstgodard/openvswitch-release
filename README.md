# openvswitch-release
[Bosh](https://bosh.io) release for [openvswitch](https://github.com/openvswitch/ovs)


## Create release
```sh
 bosh create release && bosh upload release
```

## Prerequisites
- [bosh-lite](https://github.com/cloudfoundry/bosh-lite)
  - I used bosh-lite v9000.131.0 with stemcell bosh-warden-boshlite-ubuntu-trusty-go_agent 3312.8
  - Note: I had to update the linux kernel version in bosh-lite to get openvswitch to compile and deploy properly (see #Issues)

## Deploy
Update bosh manifest to include in `release` and `template` in bosh job:

For example:

### Add to releases
```yaml
releases:
- name: diego
  version: latest
- name: netman
  version: latest
- name: garden-runc
  version: latest
- name: cf
  version: latest
- name: openvswitch
  version: latest
```
### Add job template
```yaml
```yaml
- instances: 1
  name: cell_z1
  templates:
  - name: rep
    release: diego
  - name: garden
    release: garden-runc
    . . .
  - name: openvswitch
    release: openvswitch
```

## Issues
- If deploying in bosh-lite and you see an error like this, you may need to upgrade your bosh-lite linux kernel to match /lib/modules/x.x.x-xx-generic.
```
modprobe: ERROR: ../libkmod/libkmod.c:556 kmod_search_moddep() could not open moddep file '/lib/modules/4.2.0-42-generic/modules.dep.bin'
```
**Solution: upgrade bosh-lite kernel to 4.4.0-53**
```
cd ~/workspace/bosh-lite
vagrant ssh
sudo su -
apt-get update
apt-get install -y linux-headers-4.4.0-53 linux-headers-4.4.0-53-generic linux-image-4.4.0-53-generic linux-image-extra-4.4.0-53-generic
(wait until completes and exit)
vagrant halt && vagrant up
```
