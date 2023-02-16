## tls-cert-check

It's a basic Ruby script to warn about expiring TLS certificates.

### Protocol support

tls-cert-check will automatically perform STARTTLS handshakes for known
protocols on the following ports:

  - FTP (AUTH TLS) on port 21
  - IMAP on port 143
  - IRC on ports 194, 6667
  - LDAP on port 389
  - MySQL on port 3306
  - NNTP on port 119
  - POP3 on port 110
  - SMTP on ports 25, 587
  - regular implicit TLS on all other ports

TLS SNI extension is always sent (even if not specified explicitly).

When checksrv is used against `_xmpp-client` and SNI is not explicitly set,
then the domain itself is used for SNI in accordance with XEP-0368.

### Synopsis

Cronjob:

    @daily chronic tls-cert-check [--config ~/hosts.conf] [--syslog]

Interactive use:

    tls-cert-check HOST:PORT...

Arguments:

  - `<hostname>[:<port>][/<sni>]` - check a single host (default port 443)
  - `MX:<name>` - check all hosts from MX on port 25
  - `SRV:<name>[/<sni>]` - check all hosts from SRV

### Configuration

When run without any arguments, tls-cert-check will instead check all domain
names listed in the configuration file.

Example configuration:

    # cache success results for 3600 seconds
    # (only configured hosts are cached, not ones provided via CLI)
    every 3600

    # warn if certificate expires in 28 days or less
    grace 28

    # check these hosts on given ports (STARTTLS is used automatically)
    check example.com 389,443,636
    check mail.example.com 25,143,587

    # check all mail servers listed by MX or SRV records
    checkmx example.com
    checksrv _imaps._tls.example.com
    checksrv _submissions._tls.example.com

    # check individual HA instances using Server Name Indication
    check host1.example.com/www.example.com 443
    check host2.example.com/www.example.com 443
    check host3.example.com/www.example.com 443

The default locations for the configuration file are:

  - `~/.config/nullroute.eu.org/tls-cert-check.conf`
  - `/etc/tls-cert-check.conf`
