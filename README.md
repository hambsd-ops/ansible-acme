# hambsd-acme

An Ansible role to configure acme-client to install a single certificate with
optional alternative names.

## Overview

This is an Ansible role to configure acme-client to install a single
certificate with optional alternative names.
It can optionally configure httpd for use with the http-01 challenge used by
acme-client.

On the first run, this role will run acme-client to fetch and install a
certificate. It will also install a cronjob in root's crontab to periodically
renew the certificate as required.

## Usage

This role is intended to be used as a part of other roles, for example:

```sh
git clone https://github.com/hambsd-ops/ansible-acme roles/hambsd-acme
```

In the dependent role's tasks:

```yaml
- name: set up acme-client
  include_role:
    name: hambsd-acme
  vars:
    hambsd_acme_domain: "{{ inventory_hostname }}"
```

## Variables

<table>
<tr>
<th>Variable</th><th>Default</th><th>Description</th>
</tr>
<tr>
<td>hambsd_acme_alternative_names</td><td></td><td>A list of alternative names to include in the certificate.</td>
</tr>
<tr>
<td>hambsd_acme_domain †</td><td></td><td>The domain name to use for the primary name in the certificate.</td>
</tr>
<tr>
<td>hambsd_acme_httpd</td><td>false</td><td>A boolean value indicating if a minimal httpd configuration should also be installed.</td>
</tr>
<tr>
<td>hambsd_acme_post_renew_command</td><td>rcctl restart httpd</td><td>A command to be run after a successful renewal of the certificate, usually to reload the daemon that uses the certificate.</td>
</tr>
</table>

*Variables with † are required.*

## Notes

When using the minimal httpd configuration, httpd will be configured to listen
only on port 80 and to only serve the files related to the acme challenge. If
you need to use httpd as part of the service you are configuring, ensure that
the following configuration is present for the server listening on port 80:

```
	location "/.well-known/acme-challenge/*" {
		root "/acme"
		request strip 2
	}
```

## Copyright

(C) Iain R. Learmonth 2020.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the above copyright notice, this
   list of conditions and the following disclaimer.

2. Redistributions in binary form must reproduce the above copyright notice,
   this list of conditions and the following disclaimer in the documentation
   and/or other materials provided with the distribution.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
