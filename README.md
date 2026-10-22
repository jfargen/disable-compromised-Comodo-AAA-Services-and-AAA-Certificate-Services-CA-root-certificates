## Author
Jamie Fargen
j@fargenable.com

## Warranty and testing disclaimer

This Ansible playbook is provided "AS IS", without any express or implied
warranty, including the implied warranties of merchantability or fitness for
a particular purpose, to the extent permitted by applicable law.

Running this playbook could damage your system, disrupt services, or change
certificate trust in ways that affect applications. It may not correct the
issue it is intended to address. You assume responsibility for its use and
any resulting damage.

This playbook has only been tested on **Red Hat Enterprise Linux 10.2**.
Review it and test it in a non-production environment before deploying it
to production systems.


# Certificate blocklist mitigation

`blocklist-certificates.yml` extracts the emSign ECC Root CA - G3 and
Comodo AAA Services root certificates, marks them rejected for
`anyExtendedKeyUsage`, and rebuilds the system CA trust store.

The playbook uses `/etc/pki/ca-trust/source/blocklist` when it exists,
otherwise `/etc/pki/ca-trust/source/blacklist`. It fails if neither directory
exists. Target hosts must provide `trust`, `openssl`, and `update-ca-trust`
and have both certificates available in their trust store.

Run against your inventory (privilege escalation is enabled):

```bash
ansible-playbook -i inventory blocklist-certificates.yml --limit target_group
```

Add `--ask-become-pass` if sudo requires a password. Each run overwrites the
two certificate files and rebuilds CA trust, so it reports changes on every
run. Extraction and rejection must succeed before the trust store is rebuilt.
Check mode skips the certificate commands; it does not verify the resulting
trust policy.

Validate syntax without changing any target hosts:

```bash
ansible-playbook -i localhost, blocklist-certificates.yml --syntax-check
```

## License

See [LICENSE.md](LICENSE.md) for the GPLv2 license notice.

