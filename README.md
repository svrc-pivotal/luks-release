## dm-crypt LUKS BOSH overlay release

Use this release as a deployment overlay or runtime add-on to create an encrypted persistent volume via LUKS
This is a loopback volume backed by a file in `/var/vcap/store/encrypted/data`

By default it will use most of the available space on your BOSH persistent drive
Set `luks.volume_size` to pick a custom size.

Resize (grow/shrink) is supported, but you'll need to explicitly set `luks.volume_size` to shrink the volume size.
Also, remember to perform a `bosh deploy` independently before shrinking the underlying BOSH persistent volume.


