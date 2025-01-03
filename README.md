# postfix_send_only_relay

An [Ansible](https://www.ansible.com) role to install [Postfix](https://www.postfix.org) and configure as send-only via an SMTP relay.

<p align="center">
<a href="https://github.com/salvoxia/ansible-role-postfix_send_only_relay/actions/workflows/molecule.yml"><img alt="GitHub Workflow Status" src="https://img.shields.io/github/actions/workflow/status/salvoxia/ansible-role-postfix_send_only_relay/molecule.yml?label=molecule&logo=ansible&style=flat-square"></a>
<a href="https://galaxy.ansible.com/salvoxia/postfix_send_only_relay"><img alt="GitHub tag (latest by date)" src="https://img.shields.io/github/v/tag/salvoxia/ansible-role-postfix_send_only_relay?color=EE0000&label=release&logo=ansible&style=flat-square"></a>
<a href="https://github.com/salvoxia/ansible-role-postfix_send_only_relay/blob/main/LICENSE.md"><img src="https://img.shields.io/github/license/gameservermanagers/docker-steamcmd?style=flat-square" alt="MIT License"></a>
</p>

## About

This role is designed to be used on a server that will only send emails and not receive them. This is useful for recieving emails from cron jobs or other services/applications that want to send emails from a server.

## Disclaimer

This role is based on [dgibbs64/ansible-role-postfix_send_only_relay](https://github.com/dgibbs64/ansible-role-postfix_send_only_relay) and includes the following changes:  
- Adds a fix for escaping `postfix_relayhost` regular expression characters
- Adds a fix for modifying `postfix_mailutils_config_file` file
- Adds `postfix_state` variable with values `present` or `absent`; when using `absent`, Postfix is uninstalled via package manager, `postfix_config_file` is removed and any changes to `postfix_mailutils_config_file` are reverted
- Adds `postfix_additional_main_config` to set arbitrary values in `postfix_config_file` for more flexibility in postfix configuration


## Requirements

> A SMTP relay service is required.

Your email provider may offer a SMTP relay service. If not, there are several popular SMTP relays that offer free tiers (others are also available).

| Service                            | Free Tier Email Limit       | Docs                                                                   |
| ---------------------------------- | --------------------------- | ---------------------------------------------------------------------- |
| [Mailjet](https://www.mailjet.com) | 6000 per month, 200 per day | [link](https://dev.mailjet.com/smtp-relay/overview)                    |
| [Sendgrid](https://sendgrid.com)   | 3000 per month, 100 per day | [link](https://docs.sendgrid.com/for-developers/sending-email/postfix) |

### Supported Distros

- Debian >= 11
- Ubuntu >= 22.04

## Role Variables

```yaml
---
---
# defaults file for postfix_send_only_relay

# Flag indicating whether to install or remove postfix
postfix_state: "present"

# Additional generic settings to apply to postfix main.cf file
postfix_additional_main_config: {}
#  Example:
#  smtpd_client_restrictions: "permit_mynetworks, reject"

# Config path for postfix
postfix_config_file: "/etc/postfix/main.cf"

# The domain that is used to send emails.
postfix_mydomain:

# The domain that is used to send emails if no domain is specified. This is usually the same as mydomain or myhostname
postfix_myorigin: "$mydomain"

# mydestination controls a list of domains that postfix considers itself the final destination for.
## When aliases are set, postfix needs to "proccess" the email to then forward it on using /etc/aliases.
## example: root -> postfix -> root@example.com - > aliases -> fobar@outlook.com -> relayhost -> foobar@outlook.com
postfix_mydestination: "$myhostname, localhost.$mydomain, localhost, $mydomain"
## When aliases are not required localhost only entry will forward on all emails without any "processing" from postfix.
## example: root -> postfix -> root@example.com -> relayhost -> root@example.com
# postfix_mydestination: "localhost"

# interfaces that can send emails - loopback-only allowes only localhost to send.
postfix_inet_interfaces: "loopback-only"

# The relay host of SMTP server.
postfix_relayhost:

# Username for SMTP server.
postfix_sasl_username:
# Password for SMTP server.
postfix_sasl_password:

# The domain that the mail command will use.
postfix_mail_send_domain: "{{ postfix_mydomain }}"

# Email address alias that will recieve all emails sent to the servers root user.
postfix_root_alias:

# Email address to recieve test email when role is run.
postfix_test_send_email:

# The email address that will be used to send test emails.
postfix_mailutils_config_file: "/etc/mailutils.conf"
```

## Dependencies

```yaml
community.general
```

## Example Playbook

```yaml
---
- name: Linux Admin Packages
  hosts: all
  vars:
    postfix_mydomain: "example.com"
    postfix_relayhost: "[in-v3.mailjet.com]:587"
    postfix_sasl_username: "username"
    postfix_sasl_password: "password"
    postfix_root_alias: "foobar@outlook.com"
  roles:
    - role: "salvoxia.postfix_send_only_relay"
```

## License

MIT

## Author Information

- [Daniel Gibbs](https://danielgibbs.co.uk)
- This fork is maintained by [Salvoxia](https://github.com/Salvoxia)
