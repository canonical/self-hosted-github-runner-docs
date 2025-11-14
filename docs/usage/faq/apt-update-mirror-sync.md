# What can I do if apt update fails due to mirror sync issues?

If you encounter issues with `apt update` frequently in your workflows, and you see an error like

```
 Err:42 http://az2.clouds.ports.ubuntu.com/ubuntu-ports noble-updates/universe arm64 Components
   File has unexpected size (376260 != 376296). Mirror sync in progress? [IP: 185.125.190.36 80]
   Hashes of expected file:
    - Filesize:376296 [weak]
    - SHA256:b707b2c287b502363446156f0ced3c4edfa85293c9b0f899b6980d9bb5deaec0
    - SHA1:924c48db8faec15ec864b517f291878032c228c4 [weak]
    - MD5Sum:6641ff059ed506f3d83bad8f84cf0e06 [weak]
   Release file created at: Tue, 11 Nov 2025 07:17:52 +0000
```

it is likely that the package mirror sync is not correctly working (e.g. multiple versions of the mirror have different sync dates).

Reach out to either us or IS to investigate the problem.
