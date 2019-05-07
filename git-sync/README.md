# git-sync

_Automatically provision SSH and GPG keys with remote hosts_

## Background

When it comes to managing SSH and GPG keys across multiple machines, there are essentially two schools of thought:

- Every machine gets its own SSH and GPG key. This unique key identifies only that machine to whatever service has
the corresponding public key. If a key is compromised, the attack vector is limited to just the access that machine has
to other services. However, key management becomes a pain, as a unique key needs to be generated and provisioned with
the service or remote host for every machine or environment.

- A single pair of SSH or GPG keys are shared amongst all of the hosts, and is provisioned to all the services. 
This requires the least amount of setup, as key
provisioning with a remote service happens only once, when the service or remote host is first used. Every existing 
environment can use the service once the key has been provisioned. New environments just need to copy the key from a 
central, secure location. However, if a key gets compromised, then a new key pair needs to be generated, applied to all
the connecting hosts, and provisioned to all remote services. This process can be quite cumbersome if there isn't 
a exhaustive list of all of the hosts or services. 

## Idea

Using the first approach yields the most amount of security, as each host is still individually protected if a key is 
compromised. But manual key management can be quite a pain. The ideal solution would be a local application that could
interact with these services to provision the keys. This exists for simple SSH provisioning with the `ssh-copy-id` command,
however that only works with SSH and with hosts that support interactive logins. For services like GitHub, you can 
only add SSH keys through the web or REST interface. There exists not a tool for provisioning GPG keys. 

For example: 
```bash
$ git-sync --service=github --ssh-key=~/.ssh/id_ed25519.pub --gpg-key="$(gpg --armor --export 3AA5C34371567BD2)"
Username: wheelerlaw
Password: $password
Key name: Work Laptop (Linux)
2 keys [GPG,SSH] copied to GitHub
```

