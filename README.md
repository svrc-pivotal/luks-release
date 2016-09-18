## dm-crypt LUKS BOSH overlay release

Use this release as a deployment overlay or runtime add-on to create an encrypted persistent volume via LUKS
This is a loopback volume backed by a file in `/var/vcap/store/encrypted/data`

By default it will use most of the available space on your BOSH persistent drive
Set `luks.size` to pick a custom size in bytes, or kb/mb/gb with an indicator (e.g. 5000k, 500m, 10g).
Minimum volume size is 4 MB.

Resize (grow/shrink) is supported, but you'll need to explicitly set `luks.size` to shrink the volume size.
Also, remember to perform a `bosh deploy` independently before shrinking the underlying BOSH persistent volume.

Major TODOs
* using a vault for the encryption key.  Feedback welcome here; currently we pass the key in,
move it immediately to tmpfs storage, and shred it on disk, then remove it from tmpfs after we're done.
Likely will wait for BOSH/pivotal standard vault in PCF 1.9+ rather than using hashicorp, etc.

* an option to make this transparent for ANY bosh persistent VM, this will likely always need to be optional
as any job with a pre-start script will be run in parallel with this one and might conflict if they use
/var/vcap/store while this job tries to unmount and replace the whole partition with a dm-crypt drive

* eventually porting this into the bosh agent itself, as a filesystem supported without an addon release

## Usage

```
git clone https://github.com/svrc-pivotal/luks-release
cd luks-release
bosh target BOSH_HOST
bosh upload release releases/luks-0.5.1.yml
```

To deploy, include this as an overlay or as a BOSH runtime-config addon:
```
releases:
- name: luks
  version: '0.5.1'

jobs:
- name: my_persistent_job
  templates:
  - name: luks
    release: luks
  properties:
    luks:
      current_key: 'encryption key here'  # required
      size: 5g                            # optional, defaults to persistent storage available space minus 2 MB
      encryptionPath: /var/vcap/encrypted # optional
      encryptionDevice: encrpted          # optional, placed under /dev/mapper/encrypted
```
