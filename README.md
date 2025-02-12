Harica-api
=========

An ansible role to request, approve and download server certificates from harica.gr.

Requirements
------------

- The shell tool `xmllint` should be installed. This is provided by the package:
  - Debian: `libxml2-utils`
  - Ubuntu: `libxml2-utils`
  - RHEL: `libxml2`
  - Arch Linux: `libxml2`
  - Gentoo: `dev-libs/libxml2` (enable python use flag)
- The python library `jmespath` should be installed for the `json_query` filter.

Role Variables
--------------

This role requires the following variables to work:
- `harica_api_base_url`: The base url to use for requesting certificates. Defaults to `https://cm.harica.gr`.
- `harica_api_accounts`: A hash that should contain two entries: `issuer` and `signer`. Each of these entries should have an `email` and `password`, with an optional `mfa_token` for the TOTP token. Note that this should be the calculated token itself, so a lookup is probably needed. Example:
  ```
  ---
  harica_api_accounts:
    issuer:
      email: issuer@domain.tld
      password: mysupersecretpass
    signer:
      email: signer@domain.tld
      password: myothersupersecretpass
      mfa_token: "{{ lookup('ansible.builtin.pipe', '/path/to/my/totp/binary') }}"
  ```
- `harica_api_certificates`: A hash that can contain any number of certificates. Each of the entries should have a `common_name`, an optional `subject_alt_names` list, one or all of `fullchain_dest`, `cert_dest` and `chain_dest`, and one of `csr` or `csr_path` (the former being the csr in string form, the latter a filepath to the csr). An example:
  ```
  harica_api_certificates:
    cert1:
      common_name: my.domain.tld
      csr_path: /tmp/csr.pem
      fullchain_dest: /etc/pki/httpd/my.domain.tld.pem
    cert2:
      common_name: my.second.domain.tld
      subject_alt_names:
        - www.my.second.domain.tld
      cert_dest: /etc/pki/httpd/my.second.domain.tld.crt
      chain_dest: /etc/pki/httpd/my.second.domain.tld.chain
  ```
  A certificate can also have a `request_id`, which can then be used to skip the issuing task and go directly to the sign and download task. Example:
  ```
  harica_api_certificates:
    cert1:
      request_id: my-request-id
      fullchain_dest: /etc/pki/httpd/my.domain.tld.pem
   ```

License
-------

Apache 2.0
