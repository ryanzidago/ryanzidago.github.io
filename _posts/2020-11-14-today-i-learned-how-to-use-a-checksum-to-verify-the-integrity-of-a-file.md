---
layout: post
title:  "Today I learned how to use a checksum to verify the integrity of a file"
date:   2020-11-14 17:30:00 +0100
categories: security
---
## What are checksums and sha256, and how does they relate with each other?

As I was looking for the latest stable [Ruby release](https://www.ruby-lang.org/en/downloads/) at the time of this writing, I noticed a checksum was provided, but I still did not know what to do with it.

![ruby-lang.org stable releaes](/assets/images/ruby-lang.org-stable-releases.png)

\
According to the Wikipedia [article](https://en.wikipedia.org/wiki/Checksum) on Checksum:

> A checksum is a small-sized datum derived from a block of digital data for the purpose of detecting errors that may have been introduced during its transmission or storage. By themselves, checksums are often used to verify data integrity but are not relied upon to verify data authenticity.

> The procedure which generates this checksum is called a checksum function or checksum algorithm. Depending on its design goals, a good checksum algorithm will usually output a significantly different value, even for small changes made to the input. This is especially true of cryptographic hash functions, which may be used to detect many data corruption errors and verify overall data integrity; if the computed checksum for the current data input matches the stored value of a previously computed checksum, there is a very high probability the data has not been accidentally altered or corrupted.

\
So an algorithm is used to compute a hashed version of a file (for Ruby versions from ruby-lang.org, `sha256` is used). This hash represent the checksum of the file, i.e. the content of the file at the time of hashing. Also note the last sentence of the quote:
> a very high probability the data has not been accidentally altered or corrupted

<br /><br />
## How to verify a file's integrity using its checksum?

Turns out on Ubuntu, there is a command (`sha256sum`) to verify checksums and files. First, we need to download the file that we want to check:

```bash
$ wget https://cache.ruby-lang.org/pub/ruby/2.7/ruby-2.7.2.tar.gz
```

\
Then, we can either check the checksum ourselves with our own eyes by computing the checksum of the downloaded file and comparing it with the one displayed on the Ruby releases page:

```bash
$ sha256sum ruby-2.7.2.tar.gz
6e5706d0d4ee4e1e2f883db9d768586b4d06567debea353c796ec45e8321c3d4  ruby-2.7.2.tar.gz
```

\
Or we can create a file, paste the checksum of the file provided by ruby-lang.org as well as the name of the file:

```bash
$ echo "6e5706d0d4ee4e1e2f883db9d768586b4d06567debea353c796ec45e8321c3d4 ruby-2.7.2.tar.gz" > check.text | sha256sum --check
ruby-2.7.2.tar.gz: OK
```

\
If someone would have changed the content of the archived containing Ruby, the checksum would have not matched with the archive. Let's verify this by decompressing the archive, creating a file named `some_malicious_code.rb`, recompressing everything and checking the checksums:

```bash
$ gunzip ruby-2.7.2.tar.gz && tar xvf ruby-2.7.2.tar
$ echo "puts 'some malicious code has been injected!!!'" > ruby-2.7.2/some_malicious_code.rb
$ tar -cvf ruby-2.7.2.tar.gz ruby-2.7.2
$ echo "6e5706d0d4ee4e1e2f883db9d768586b4d06567debea353c796ec45e8321c3d4 ruby-2.7.2.tar.gz" > check.text | sha256sum --check
ruby-2.7.2.tar.gz: FAILED
sha256sum: WARNING: 1 computed checksum did NOT match
```

\
As expected it failed!
Indeed, the checksum of the altered Ruby archive is totally different from the one provided by ruby-lang.org:
```bash
$ sha256sum ruby-2.7.2.tar.gz
dc02c41872a479ffe2630e5636783c3ae0821f788444d416de68354c3a4a96d3  ruby-2.7.2.tar.gz
```
