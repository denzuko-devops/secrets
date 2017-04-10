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

## External References

- [https://gist.github.com/andrewlkho/7373190](How to use authentication subkeys in gpg for SSH public key authentication)
- [http://evans.io/legacy/posts/ssh-agent-for-sudo-authentication/](SSH agent for sudo authentication)
- [https://www.ibm.com/developerworks/aix/library/au-sshlocks/](Three locks for ssh door)
- [https://wiki.archlinux.org/index.php/SSH_keys#pam_ssh](Pam SSH)
- [https://hobo.house/2016/06/20/fun-and-profit-with-reverse-ssh-tunnels-and-autossh/](Auto reverse ssh tunnels)
- [https://samsaffron.com/archive/2008/07/02/SSH+Jumphosts+tunneling+and+other+curiosities+](Jumphost tunnling)
- [https://karlgrz.com/2fa-gpg-ssh-keys-with-pass-and-yubikey-neo/](2fa with gpg keys and yubikey)
