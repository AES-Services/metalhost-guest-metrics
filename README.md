# Metalhost guest metrics

The optional Linux collector for expanded monitoring on Metalhost VMs. It sends
OS metrics over a private connection to the VM's host—no public listening port,
remote shell or log collection. Basic VM monitoring works without it.

## Get started

Open your VM's **Monitoring** tab in Metalhost and follow the setup instructions.
Where automatic installation is available, use **Install monitoring agent**, or
select **Install expanded monitoring** when creating a supported VM.

For manual installation, download the exact version offered by Metalhost from
[Releases](https://github.com/AES-Services/metalhost-guest-metrics/releases),
[verify the package](VERIFY.md), then follow the `INSTALL.md` inside the archive.
That guide also covers updates and removal.
