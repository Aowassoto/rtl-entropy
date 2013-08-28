rtl-entropy
===========

rtl-entropy is software using rtl-sdr to turn your DVB-T dongle into a high quality entropy source. It samples atmospheric noise, runs it throught the FIPS 140-2 tests, if it passes write the entropy to the specified output. 

If you're serious about the cryptographic security of your entropy source, you should probably short the antenna port, and put the whole assembly in a shielded box. Then you're getting entropy from the thermal noise of the amplifiers which is much harder to interfere with than atmospheric radio.

Both of these are analog entropy sources.

This software has been tested on debian linux 7.1, but should work on any linux distribution, and might run on OS X and other POSIX compliant operating systems.

Dependencies
------------

* [rtlsdr](http://sdr.osmocom.org/trac/wiki/rtl-sdr)

Build
-----

make 

Assumes you've got the rtl-sdr libraries and gcc installed.

Usage
-----

./rtl_entropy > high_entropy.bin
and press CTRL+C to stop, and do whatever you like with it.

or

./rtl_entropy -s 2.4M -f 101.5M | rngtest -c 1280 -p > high_entropy.bin

to set the sample rate to 2.4Msamples/s and the frequency to tune to as 101.5 MHz, piped to rngtest which checks 1280 runs and stores it in high_entropy.bin

You should be able to use rndaddentropy from [twuwand](http://github.com/rfinnie/twuewand)

./rtl_entropy -s 2.4M -f 101.5M | rndaddentropy

for Daemon mode, along with rngd from rng-tools on linux:

rtl_entropy -b
rngd -r /var/run/rtl_entropy.fifo -W95%

The daemon mode uses /var/run/rtl_entropy.fifo for output and /var/run/rtl_entropy.pid for it's PID file. You may want to tweak the size of your entropy pool, and explore other options for rngd to maximise performance.

To Do
-----

- Further research and consultation with security experts is needed on:
 * Need a mixing step xor fresh random bytes by with the old data
 * maybe a hash as well?
 * add Kaminsky debiasing to my von neumann debiasing

- Code Review


Done!
-----

* Implement daemon mode that adds entropy to the system pool.


Credits
-------

rtl_entropy was written by Paul Warren <pwarren@pwarren.id.au>

Uses code from:

  * rtl_test. Copyright (C) 2012 by Steve Markgraf http://sdr.osmocom.org/trac/wiki/rtl-sdr
  * rng-test-4. Copyright (C) 2001 Philipp Rumpf http://sourceforge.net/projects/gkernel/
  * http://openfortress.org/cryptodoc/random/noise-filter.c by Rick van Rein
  * snd-egd http://code.google.com/p/snd-egd/
  * Keenerd on the osmocom-sdr mailing list

