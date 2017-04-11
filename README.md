# secrets

A git based secret store with gpg and x509 names.

## Requirements
   - gnupg
   - git
   - pass(1) - http://passwordstore.org

## How to retrieve a user's public key
```
GET http://github.com/denzuko.keys
```

## How to authorize additional users
```
Content-Type: application/json
Authorization: token "OAUTH-TOKEN"

PUT https://api.github.com/repos/denzuko-devops/secrets/collaborators/:github_username
permission=pull
```

### Build public key chain

1. `gpg --recv-key "user email or gpg key id"`
2. `gpg --batch --yes --edit-key "0xgpgkeyid" tsign 5`

### Authorizing a gpg user to a set of keys

To restrict only a set of specific gpg-id or set of gpg-ids is assigned for that specific sub folder of the password store.

1. `pass init -p 'x509 directory' "list of keys that are space deliminated"`
2. `pass gpg push origin`

For example to authorizie for the Org:

`pass init -p 'DC=net,DC=dapla,O=devops/'`

#### For the Org Unit:

1. * `pass init -p 'DC=net,DC=dapla,O=devops/OU=operations/'`
2. `pass gpg push origin`


#### For the service or application:

1. * `pass init -p 'DC=net,DC=dapla,O=devops/OU=operations/OU=Server Keys/'`
   * `pass init -p 'DC=net,DC=dapla,O=devops/OU=operations/OU=Application Keys/'`
   * `pass init -p 'DC=net,DC=dapla,O=devops/OU=operations/OU=Api Keys/'`
2. `pass gpg push origin`

## How to Deauthorize additional users

```
Content-Type: application/json
Authorization: token "OAUTH-TOKEN"

DELETE https://api.github.com/repos/denzuko-devops/secrets/collaborators/:github_username
```

### Decommission key
1. `gpg --batch --yes --edit-key "0xgpgkeyid" trust 2`
2. `gpg --send-key "0xgpgkeyid"`
3. `pass init "list of keys that are space deliminated"`
4. `pass gpg push origin`

## Changing Context/Orgs
`pass git checkout iso.org.dod.internet.42387.customers.OID`

## Directory Structure

Directories begin with the baseDN and originization (ie DC=net,DC=dapla,O=devops). Following this the next level is originization/business unit then class of key, protocol name for the common port if applicaple and common name.

For example to access a web service key one would use:

```
pass 'DC=net,DC=dapla,O=devops/OU=operations/OU=Application Keys/OU=https/CN=routerlogin.matrix.net'
```

## External References

- [How to use authentication subkeys in gpg for SSH public key authentication](https://gist.github.com/andrewlkho/7373190)
- [SSH agent for sudo authentication](http://evans.io/legacy/posts/ssh-agent-for-sudo-authentication/)
- [Three locks for ssh door](https://www.ibm.com/developerworks/aix/library/au-sshlocks/)
- [Pam SSH Authentication](https://wiki.archlinux.org/index.php/SSH_keys#pam_ssh)
- [Auto reverse ssh tunnels](https://hobo.house/2016/06/20/fun-and-profit-with-reverse-ssh-tunnels-and-autossh/)
- [Jumphost tunnling](https://samsaffron.com/archive/2008/07/02/SSH+Jumphosts+tunneling+and+other+curiosities+)
- [2fa with gpg keys and yubikey](https://karlgrz.com/2fa-gpg-ssh-keys-with-pass-and-yubikey-neo/)
- [Create CSR from gpg key and openssl](http://wiki.cacert.org/ConvertingPgpKeyToCertificate)
- [Stunnel Client auth](http://wiki.cacert.org/StunnelConfiguration)
- [Automatic SSL Creation](https://gist.github.com/denzuko/05c4c51ea451d4196df68ab22b6ca3c6)
