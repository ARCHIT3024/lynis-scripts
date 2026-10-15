# lynis-scripts

Mail server test scripts for [Lynis](https://github.com/CISOfy/lynis), the open-source security auditing tool for Unix/Linux systems.

The repository holds three versions of Lynis test files: the original upstream e-mail test file, an improved copy of it, and a new test group for mail server hardening.

## Repository structure

```
.
├── reference/
│   └── tests_mail_messaging    # Upstream Lynis file, unmodified (baseline)
├── enhanced/
│   └── tests_mail_messaging    # Improved version of the upstream file
└── new/
    └── tests_mail_hardening    # New test group (not part of upstream Lynis)
```

## Files

### `reference/tests_mail_messaging`

The original "E-mail and messaging" test file from upstream Lynis, kept for comparison. It checks the status and basic configuration of common mail daemons:

| Test ID   | Description                          |
|-----------|--------------------------------------|
| MAIL-8802 | Check Exim status                    |
| MAIL-8804 | Exim configuration options           |
| MAIL-8814 | Check Postfix process status         |
| MAIL-8816 | Check Postfix configuration          |
| MAIL-8817 | Check Postfix configuration errors   |
| MAIL-8818 | Check Postfix configuration: banner  |
| MAIL-8820 | Postfix configuration scan           |
| MAIL-8838 | Check Dovecot process                |
| MAIL-8860 | Check Qmail status                   |
| MAIL-8880 | Check Sendmail status                |
| MAIL-8920 | Check OpenSMTPD status               |

### `enhanced/tests_mail_messaging`

A drop-in replacement for the upstream file that fixes bugs and adds new checks.

**Fixes**
- OpenSMTPD was not reported as `smtp_daemon`
- qmail was not detected on idle servers
- Wrong Exim option name (`tls_verify_certificates`)
- Exim values containing `=` were parsed incorrectly
- GNU-only `sed` syntax replaced with portable syntax
- Postfix tests ran even when `postconf` was not available

**New tests** (in addition to all tests in the reference file)

| Test ID   | Description                                        |
|-----------|----------------------------------------------------|
| MAIL-8806 | Check Exim configuration: banner                   |
| MAIL-8822 | Check Postfix relay restrictions                   |
| MAIL-8824 | Check Postfix TLS configuration                    |
| MAIL-8840 | Check Dovecot SSL and authentication configuration |
| MAIL-8882 | Check Sendmail privacy options and banner          |
| MAIL-8922 | Check OpenSMTPD configuration                      |
| MAIL-8930 | Check for multiple running MTAs                    |

It also checks private key permissions and adds hardening suggestions.

### `new/tests_mail_hardening`

A new test group, not part of upstream Lynis. Where `tests_mail_messaging` looks at the configuration of each mail daemon, this group looks at what surrounds them:

| Test ID   | Description                                        |
|-----------|----------------------------------------------------|
| MAIL-9010 | Check mail aliases file                            |
| MAIL-9012 | Check aliases for command and file deliveries      |
| MAIL-9014 | Check .forward files of users                      |
| MAIL-9020 | Check mail spool and mailbox permissions           |
| MAIL-9022 | Check permissions of MTA queue directories         |
| MAIL-9030 | Check TLS certificates of mail services            |
| MAIL-9040 | Check for unencrypted mail protocols on the network|
| MAIL-9050 | Check mail log files                               |
| MAIL-9060 | Check for DKIM/DMARC and spam/virus filtering      |

## Usage

These are Lynis include files, so they run inside Lynis rather than on their own.

1. Get Lynis:
   ```sh
   git clone https://github.com/CISOfy/lynis.git
   cd lynis
   ```
2. Copy the test files into the `include/` directory:
   ```sh
   # Replace the upstream mail test file with the enhanced one
   cp /path/to/lynis-scripts/enhanced/tests_mail_messaging include/tests_mail_messaging

   # Add the new hardening test group
   cp /path/to/lynis-scripts/new/tests_mail_hardening include/tests_mail_hardening
   ```
3. Run the audit (as root for full results):
   ```sh
   sudo ./lynis audit system --tests-from-group mail_messaging
   sudo ./lynis audit system --tests-from-group mail_hardening
   ```

> To run `tests_mail_hardening` as part of a full `lynis audit system`, add `mail_hardening` to the list of test groups in the main `lynis` script, next to `mail_messaging`.

## License

These files are based on Lynis by Michael Boelen, CISOfy, and contributors, and are distributed under the GNU General Public License v3, the same license as Lynis. They come with ABSOLUTELY NO WARRANTY.
