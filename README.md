# Amazon Affiliate Report Fetcher

A simple Rake task that fetches Amazon Affiliate/Associate reports from, well, Amazon. Uses phantomjs and [olivaw](https://github.com/L1quid/Olivaw).

The Amazon password is fetched from the OS X Keychain. The username should be set in a `config.yml` file (see `config.example.yml`).

## Install

```sh
git clone https://github.com/matiaskorhonen/amazon-affiliate-report-fetcher.git
cd amazon-affiliate-report-fetcher
git submodule update --force
cp config.example.yml config.yml
```

Add a password to your keychain for `www.amazon.com`

## Usage

Just run `rake` to clear out the old CSVs and fetch new ones

Run `rake -T` to see the options.

---

MIT Licensed, see the `LICENSE` file for more details.

Copyright © 2014 by Matias Korhonen
