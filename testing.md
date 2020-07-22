# OpenEmbedded Testing Integration for Oryx/Mion

The following is a summary of the following two documents:  
https://www.yoctoproject.org/docs/current/dev-manual/dev-manual.html#performing-automated-runtime-testing  
https://wiki.yoctoproject.org/wiki/Ptest  

For the system, there are two categories of testing that will be implemented:
Runtime and Package.

The Automated Runtime Testing is part of OpenEmbedded and provides a set of 
tests which check and log various runtime functionality of basic utilities such
as ping, ssh, and dmesg. This can be run on a qemu image or on the hardware.
It is also possible to create your own test. Runtime Testing modules use
the python unittest framework. 

Ptest, or Package test, enables the building and running of test suites that
may be included with a packages source. These are then ran in sequence using
ptest-runner on the target. 

-------------------------------------------------------------------------------

## Automated Runtime Testing

The Automated Runtime Testing contains a suite of tests which are run on Host
and connect to the target via SSH. The included tests are primarily geared
towards testing network utilities, boot/init, and power control.  

### General setup:
The automated tests use runqemu, and relate scripts which needs to be 
added to your path, and can be found in `mion/openembedded-core/scripts` the
simplest way is to source `openembedded-core/oe-init-build-env`. 
#### QEMU
1. On the host/build system, interaction with sudo for networking must be avoided. 
There are two methods:
    * Add `NOPASSWD` for user in `/etc/sudoers for runeqemu-ifup`.
    * Configure a tap interface, either manually or by running
      `scripts/runeqemu-gen-tapdevs` as root using the absolute path. To run this
      script, the package qemu-helper-native must be built with bitbake. 
**This option should not be used on the build server due to a possibility of 
disconnecting the server from the network**

2. Configure the host firewall to accept connections from 192.168.7.0/24.
3. Install `sysstat` and `iproute2`.

#### Hardware

To enable runtime tests on hardware, a number of options depend on setup and
deployment, and may require an additional partition.  
This will be covered at a later time. 


### Running the Tests:

These tests can be run automatically or manually. Automatically running the 
tests will cause the tests to be run after the image is created. To enable 
this, add to local.conf
    `TESTIMAGE_AUTO = "1"`
To enable running tests manually, add to local.conf  
    `INHERIT += "testimage"`  
and run  
    `bitbake -c testimage <image>`
The test files can be found in `meta/lib/oeqa/runtime`.

Once the tests are started, a copy of the root file system is created to
`{WORKDIR}/testimage`, runqemu boots the QEMU image, running most tests through
an ssh connection.
    * Boot log: `${WORKDIR}/testimage/qemu_boot_log`
    * Test command log: `${WORKDIR}/testimgage/ssh_target_log`
    * unit test log:  `${WORKDIR}/temp/log.do_testimage`
TODO: document exporting tasks to test server
### Creating your own test:
TODO
-------------------------------------------------------------------------------

## Ptest
To enable package testing, edit `local.conf` and add the following:
    `DISTRO_FEATURES_append = " ptest"`  
    `EXTRA_IMAGE_FEATURES += "ptest-pkgs"`  
To run all the installed ptests,without needing to run them individually add:
    `IMAGE_INSTALL_append = "ptest-runner"`
Run `ptest-runner >> <logFile>` outputing the results to a log file.
ptests are installed in `usr/lib/<package>/ptest` on the image

TODO: writing a ptest for a package

-------------------------------------------------------------------------------
## Oryx Testing Considerations:
Due to the nature of Oryx using guest containers TODO: first check what testing
is already available for both host-test and if anything exists for 
guest testing
