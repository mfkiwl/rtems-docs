.. SPDX-License-Identifier: CC-BY-SA-4.0

.. Copyright (C) 2016 Chris Johns <chrisj@rtems.org>

.. _rtems-kernel:

RTEMS Kernel
============

RTEMS is an open source real-time operating system. As a user you have access
to all the source code. The ``RTEMS Kernel`` section will show you how you
build the RTEMS kernel on your host.

Development Sources
-------------------

Create a new location to build the RTEMS kernel:

.. code-block:: none

  $ cd
  $ cd development/rtems
  $ mkdir kernel
  $ cd kernel

Clone the RTEMS respository:

.. code-block:: none

  $ git clone git://git.rtems.org/rtems.git rtems
  Cloning into 'rtems'...
  remote: Counting objects: 483342, done.
  remote: Compressing objects: 100% (88974/88974), done.
  remote: Total 483342 (delta 390053), reused 475669 (delta 383809)
  Receiving objects: 100% (483342/483342), 69.88 MiB | 1.37 MiB/s, done.
  Resolving deltas: 100% (390053/390053), done.
  Checking connectivity... done.

Tools Path Set Up
-----------------

We need to set our path to include the RTEMS tools we built in the previous
section. The RTEMS tools needs to be first in your path because RTEMS provides
specific versions of the ``autoconf`` and ``automake`` tools. We want to use
the RTEMS version and not your host's versions:

.. code-block:: none

  $ export PATH=$HOME/development/rtems/5/bin:$PATH

.. _bootstrapping:

Bootstrapping
-------------

The developers version of the code from git requires we ``bootstrap`` the
source code. This is an ``autoconf`` and ``automake`` bootstrap to create the
various files generated by ``autoconf`` and ``automake``. RTEMS does not keep
these generated files under version control. The bootstrap process is slow so
to speed it up we provide a command that can perform the bootstrap in
parallel using your available cores. We need to enter the cloned source
directory then run the bootstrap commands:

.. code-block:: none

  $ cd rtems
  $ ./bootstrap -c && ./rtems-bootstrap
  removing automake generated Makefile.in files
  removing configure files
  removing aclocal.m4 files
  RTEMS Bootstrap, 5 (089327b5dcf9)
    1/139: autoreconf: configure.ac
    2/139: autoreconf: cpukit/configure.ac
    3/139: autoreconf: tools/cpu/configure.ac
    4/139: autoreconf: tools/cpu/generic/configure.ac
    5/139: autoreconf: tools/cpu/sh/configure.ac
    6/139: autoreconf: tools/cpu/nios2/configure.ac
    7/139: autoreconf: tools/build/configure.ac
    8/139: autoreconf: doc/configure.ac
   ......
  124/139: autoreconf: c/src/make/configure.ac
  125/139: autoreconf: c/src/librtems++/configure.ac
  126/139: autoreconf: c/src/ada-tests/configure.ac
  127/139: autoreconf: testsuites/configure.ac
  128/139: autoreconf: testsuites/libtests/configure.ac
  129/139: autoreconf: testsuites/mptests/configure.ac
  130/139: autoreconf: testsuites/fstests/configure.ac
  131/139: autoreconf: testsuites/sptests/configure.ac
  132/139: autoreconf: testsuites/tmtests/configure.ac
  133/139: autoreconf: testsuites/smptests/configure.ac
  134/139: autoreconf: testsuites/tools/configure.ac
  135/139: autoreconf: testsuites/tools/generic/configure.ac
  136/139: autoreconf: testsuites/psxtests/configure.ac
  137/139: autoreconf: testsuites/psxtmtests/configure.ac
  138/139: autoreconf: testsuites/rhealstone/configure.ac
  139/139: autoreconf: testsuites/samples/configure.ac
  Bootstrap time: 0:02:47.398824

Building a BSP
--------------

We build RTEMS in a directory outside of the source tree we have just cloned
and ``bootstrapped``. You cannot build RTEMS while in the source tree. Lets
create a suitable directory using the name of the BSP we are going to build:

.. code-block:: none

  $ cd ..
  $ mkdir erc32
  $ cd erc32

Configure RTEMS using the ``configure`` command. We use a full path to
``configure`` so the object files built contain the absolute path of the source
files. If you are source level debugging you will be able to access the source
code to RTEMS from the debugger. We will build for the ``erc32`` BSP with POSIX
enabled and the networking stack disabled:

.. code-block:: none

  $ $HOME/development/rtems/kernel/rtems/configure --prefix=$HOME/development/rtems/5 \
                     --target=sparc-rtems5 --enable-rtemsbsp=erc32 --enable-posix \
		     --disable-networking
  checking for gmake... no
  checking for make... make
  checking for RTEMS Version... 4.11.99.0
  checking build system type... x86_64-pc-linux-gnu
  checking host system type... x86_64-pc-linux-gnu
  checking target system type... sparc-unknown-rtems5
  checking for a BSD-compatible install... /usr/bin/install -c
  checking whether build environment is sane... yes
  checking for a thread-safe mkdir -p... /bin/mkdir -p
  checking for gawk... no
  checking for mawk... mawk
  checking whether make sets $(MAKE)... yes
  checking whether to enable maintainer-specific portions of Makefiles... no
  checking that generated files are newer than configure... done
   ......
  checking target system type... sparc-unknown-rtems5
  checking rtems target cpu... sparc
  checking for a BSD-compatible install... /usr/bin/install -c
  checking whether build environment is sane... yes
  checking for sparc-rtems5-strip... sparc-rtems5-strip
  checking for a thread-safe mkdir -p... /bin/mkdir -p
  checking for gawk... no
  checking for mawk... mawk
  checking whether make sets $(MAKE)... yes
  checking whether to enable maintainer-specific portions of Makefiles... no
  checking that generated files are newer than configure... done
  configure: creating ./config.status
  config.status: creating Makefile

  target architecture: sparc.
  available BSPs: erc32.
  'make all' will build the following BSPs: erc32.
  other BSPs can be built with 'make RTEMS_BSP="bsp1 bsp2 ..."'

  config.status: creating Makefile

Build RTEMS using two cores:

.. code-block:: none

  $ make -j 2
  Making all in tools/build
  make[1]: Entering directory '/home/chris/development/rtems/kernel/erc32/tools/build'
  make  all-am
  make[2]: Entering directory '/home/chris/development/rtems/kernel/erc32/tools/build'
  gcc -DHAVE_CONFIG_H -I. -I/home/chris/development/rtems/kernel/rtems/tools/build     -g -O2 -MT cklength.o -MD -MP -MF .deps/cklength.Tpo -c -o cklength.o /home/chris/development/rtems/kernel/rtems/tools/build/cklength.c
  gcc -DHAVE_CONFIG_H -I. -I/home/chris/development/rtems/kernel/rtems/tools/build     -g -O2 -MT eolstrip.o -MD -MP -MF .deps/eolstrip.Tpo -c -o eolstrip.o /home/chris/development/rtems/kernel/rtems/tools/build/eolstrip.c
  mv -f .deps/cklength.Tpo .deps/cklength.Po
  mv -f .deps/eolstrip.Tpo .deps/eolstrip.Po
  gcc -DHAVE_CONFIG_H -I. -I/home/chris/development/rtems/kernel/rtems/tools/build     -g -O2 -MT compat.o -MD -MP -MF .deps/compat.Tpo -c -o compat.o /home/chris/development/rtems/kernel/rtems/tools/build/compat.c
  gcc -DHAVE_CONFIG_H -I. -I/home/chris/development/rtems/kernel/rtems/tools/build     -g -O2 -MT packhex.o -MD -MP -MF .deps/packhex.Tpo -c -o packhex.o /home/chris/development/rtems/kernel/rtems/tools/build/packhex.c
  mv -f .deps/compat.Tpo .deps/compat.Po
  gcc -DHAVE_CONFIG_H -I. -I/home/chris/development/rtems/kernel/rtems/tools/build     -g -O2 -MT unhex.o -MD -MP -MF .deps/unhex.Tpo -c -o unhex.o /home/chris/development/rtems/kernel/rtems/tools/build/unhex.c
  mv -f .deps/packhex.Tpo .deps/packhex.Po
  gcc -DHAVE_CONFIG_H -I. -I/home/chris/development/rtems/kernel/rtems/tools/build     -g -O2 -MT rtems-bin2c.o -MD -MP -MF .deps/rtems-bin2c.Tpo -c -o rtems-bin2c.o /home/chris/development/rtems/kernel/rtems/tools/build/rtems-bin2c.c
  mv -f .deps/unhex.Tpo .deps/unhex.Po
  gcc -DHAVE_CONFIG_H -I. -I/home/chris/development/rtems/kernel/rtems/tools/build     -g -O2 -MT binpatch.o -MD -MP -MF .deps/binpatch.Tpo -c -o binpatch.o /home/chris/development/rtems/kernel/rtems/tools/build/binpatch.c
  mv -f .deps/rtems-bin2c.Tpo .deps/rtems-bin2c.Po
  gcc  -g -O2   -o cklength cklength.o
  mv -f .deps/binpatch.Tpo .deps/binpatch.Po
  gcc  -g -O2   -o eolstrip eolstrip.o compat.o
  gcc  -g -O2   -o packhex packhex.o
  gcc  -g -O2   -o rtems-bin2c rtems-bin2c.o compat.o
  gcc  -g -O2   -o unhex unhex.o compat.o
  gcc  -g -O2   -o binpatch binpatch.o
  make[2]: Leaving directory '/home/chris/development/rtems/kernel/erc32/tools/build'
  make[1]: Leaving directory '/home/chris/development/rtems/kernel/erc32/tools/build'
  Making all in tools/cpu
  make[1]: Entering directory '/home/chris/development/rtems/kernel/erc32/tools/cpu'
  Making all in generic
  make[2]: Entering directory '/home/chris/development/rtems/kernel/erc32/tools/cpu/generic'
  make[2]: Nothing to be done for 'all'.
  make[2]: Leaving directory '/home/chris/development/rtems/kernel/erc32/tools/cpu/generic'
  make[2]: Entering directory '/home/chris/development/rtems/kernel/erc32/tools/cpu'
  make[2]: Nothing to be done for 'all-am'.
  make[2]: Leaving directory '/home/chris/development/rtems/kernel/erc32/tools/cpu'
  make[1]: Leaving directory '/home/chris/development/rtems/kernel/erc32/tools/cpu'
  Making all in testsuites/tools
  make[1]: Entering directory '/home/chris/development/rtems/kernel/erc32/testsuites/tools'
  Making all in generic
  make[2]: Entering directory '/home/chris/development/rtems/kernel/erc32/testsuites/tools/generic'
  make[2]: Nothing to be done for 'all'.
  make[2]: Leaving directory '/home/chris/development/rtems/kernel/erc32/testsuites/tools/generic'
  make[2]: Entering directory '/home/chris/development/rtems/kernel/erc32/testsuites/tools'
  make[2]: Nothing to be done for 'all-am'.
  make[2]: Leaving directory '/home/chris/development/rtems/kernel/erc32/testsuites/tools'
  make[1]: Leaving directory '/home/chris/development/rtems/kernel/erc32/testsuites/tools'
  Making all in sparc-rtems5/c
  make[1]: Entering directory '/home/chris/development/rtems/kernel/erc32/sparc-rtems5/c'
  Making all in .
  make[2]: Entering directory '/home/chris/development/rtems/kernel/erc32/sparc-rtems5/c'
  Configuring RTEMS_BSP=erc32
  checking for gmake... no
  checking for make... make
  checking build system type... x86_64-pc-linux-gnu
  checking host system type... sparc-unknown-rtems5
   ......
  sparc-rtems5-gcc -B../../../../../erc32/lib/ -specs bsp_specs -qrtems -DHAVE_CONFIG_H -I. -I/home/chris/development/rtems/kernel/rtems/c/src/../../testsuites/samples/nsecs -I.. -I/home/chris/development/rtems/kernel/rtems/c/src/../../testsuites/samples/../support/include   -mcpu=cypress -O2 -g -ffunction-sections -fdata-sections -Wall -Wmissing-prototypes -Wimplicit-function-declaration -Wstrict-prototypes -Wnested-externs -MT init.o -MD -MP -MF .deps/init.Tpo -c -o init.o /home/chris/development/rtems/kernel/rtems/c/src/../../testsuites/samples/nsecs/init.c
  sparc-rtems5-gcc -B../../../../../erc32/lib/ -specs bsp_specs -qrtems -DHAVE_CONFIG_H -I. -I/home/chris/development/rtems/kernel/rtems/c/src/../../testsuites/samples/nsecs -I.. -I/home/chris/development/rtems/kernel/rtems/c/src/../../testsuites/samples/../support/include   -mcpu=cypress -O2 -g -ffunction-sections -fdata-sections -Wall -Wmissing-prototypes -Wimplicit-function-declaration -Wstrict-prototypes -Wnested-externs -MT empty.o -MD -MP -MF .deps/empty.Tpo -c -o empty.o /home/chris/development/rtems/kernel/rtems/c/src/../../testsuites/samples/nsecs/empty.c
  mv -f .deps/empty.Tpo .deps/empty.Po
  mv -f .deps/init.Tpo .deps/init.Po
  sparc-rtems5-gcc -B../../../../../erc32/lib/ -specs bsp_specs -qrtems -mcpu=cypress -O2 -g -ffunction-sections -fdata-sections -Wall -Wmissing-prototypes -Wimplicit-function-declaration -Wstrict-prototypes -Wnested-externs -Wl,--gc-sections  -mcpu=cypress   -o nsecs.exe init.o empty.o
  sparc-rtems5-nm -g -n nsecs.exe > nsecs.num
  sparc-rtems5-size nsecs.exe
     text    data     bss     dec     hex filename
   121392    1888    6624  129904   1fb70 nsecs.exe
  cp nsecs.exe nsecs.ralf
  make[6]: Leaving directory '/home/chris/development/rtems/kernel/erc32/sparc-rtems5/ c/erc32/testsuites/samples/nsecs'
  make[5]: Leaving directory '/home/chris/development/rtems/kernel/erc32/sparc-rtems5/ c/erc32/testsuites/samples'
  make[4]: Leaving directory '/home/chris/development/rtems/kernel/erc32/sparc-rtems5/ c/erc32/testsuites/samples'
  make[4]: Entering directory '/home/chris/development/rtems/kernel/erc32/sparc-rtems5/ c/erc32/testsuites'
  make[4]: Nothing to be done for 'all-am'.
  make[4]: Leaving directory '/home/chris/development/rtems/kernel/erc32/sparc-rtems5/ c/erc32/testsuites'
  make[3]: Leaving directory '/home/chris/development/rtems/kernel/erc32/sparc-rtems5/ c/erc32/testsuites'
  make[2]: Leaving directory '/home/chris/development/rtems/kernel/erc32/sparc-rtems5/ c/erc32'
  make[1]: Leaving directory '/home/chris/development/rtems/kernel/erc32/sparc-rtems5/c'
  make[1]: Entering directory '/home/chris/development/rtems/kernel/erc32'
  make[1]: Nothing to be done for 'all-am'.
  make[1]: Leaving directory '/home/chris/development/rtems/kernel/erc32'

Installing A BSP
----------------

All that remains to be done is to install the kernel. Installing RTEMS copies
the API headers and architecture specific libraries to a locaiton under the
`prefix` you provide. You can install any number of BSPs under the same
`prefix`. We recommend you have a separate `prefix` for different versions of
RTEMS. Do not mix versions of RTEMS under the same `prefix`. Make installs
RTEMS with the following command:

.. code-block:: none

  $ make install
  Making install in tools/build
  make[1]: Entering directory '/home/chris/development/rtems/kernel/erc32/tools/build'
  make[2]: Entering directory '/home/chris/development/rtems/kernel/erc32/tools/build'
  /bin/mkdir -p '/home/chris/development/rtems/5/bin'
  /usr/bin/install -c cklength eolstrip packhex unhex rtems-bin2c '/home/chris/development/rtems/5/bin'
  /bin/mkdir -p '/home/chris/development/rtems/5/bin'
  /usr/bin/install -c install-if-change '/home/chris/development/rtems/5/bin'
  make[2]: Nothing to be done for 'install-data-am'.
  make[2]: Leaving directory '/home/chris/development/rtems/kernel/erc32/tools/build'
  make[1]: Leaving directory '/home/chris/development/rtems/kernel/erc32/tools/build'
  Making install in tools/cpu
  make[1]: Entering directory '/home/chris/development/rtems/kernel/erc32/tools/cpu'
  Making install in generic
  make[2]: Entering directory '/home/chris/development/rtems/kernel/erc32/tools/cpu/generic'
  make[3]: Entering directory '/home/chris/development/rtems/kernel/erc32/tools/cpu/generic'
  make[3]: Nothing to be done for 'install-exec-am'.
  make[3]: Nothing to be done for 'install-data-am'.
  make[3]: Leaving directory '/home/chris/development/rtems/kernel/erc32/tools/cpu/generic'
  make[2]: Leaving directory '/home/chris/development/rtems/kernel/erc32/tools/cpu/generic'
  make[2]: Entering directory '/home/chris/development/rtems/kernel/erc32/tools/cpu'
  make[3]: Entering directory '/home/chris/development/rtems/kernel/erc32/tools/cpu'
  make[3]: Nothing to be done for 'install-exec-am'.
  make[3]: Nothing to be done for 'install-data-am'.
  make[3]: Leaving directory '/home/chris/development/rtems/kernel/erc32/tools/cpu'
  make[2]: Leaving directory '/home/chris/development/rtems/kernel/erc32/tools/cpu'
  make[1]: Leaving directory '/home/chris/development/rtems/kernel/erc32/tools/cpu'
    ....
  make[1]: Leaving directory '/home/chris/development/rtems/kernel/erc32/sparc-rtems5/c'
  make[1]: Entering directory '/home/chris/development/rtems/kernel/erc32'
  make[2]: Entering directory '/home/chris/development/rtems/kernel/erc32'
  make[2]: Nothing to be done for 'install-exec-am'.
  /bin/mkdir -p '/home/chris/development/rtems/5/make'
  /usr/bin/install -c -m 644 /home/chris/development/rtems/kernel/rtems/make/main.cfg /home/chris/development/rtems/kernel/rtems/make/leaf.cfg '/home/chris/development/rtems/5/make'
  /bin/mkdir -p '/home/chris/development/rtems/5/share/rtems5/make/Templates'
  /usr/bin/install -c -m 644 /home/chris/development/rtems/kernel/rtems/make/Templates/Makefile.dir /home/chris/development/rtems/kernel/rtems/make/Templates/Makefile.leaf /home/chris/development/rtems/kernel/rtems/make/Templates/Makefile.lib '/home/chris/development/rtems/5/share/rtems5/make/Templates'
  /bin/mkdir -p '/home/chris/development/rtems/5/make/custom'
  /usr/bin/install -c -m 644 /home/chris/development/rtems/kernel/rtems/make/custom/default.cfg '/home/chris/development/rtems/5/make/custom'
  make[2]: Leaving directory '/home/chris/development/rtems/kernel/erc32'
  make[1]: Leaving directory '/home/chris/development/rtems/kernel/erc32'

Contributing Patches
--------------------

RTEMS welcomes fixes to bugs and new features. The RTEMS Project likes to have
bugs fixed against a ticket created on our :r:url:`devel`. Please raise a
ticket if you have a bug. Any changes that are made can be tracked against the
ticket. If you want to add a new a feature please post a message to
:r:list:`devel` describing what you would like to implement. The RTEMS
maintainer will help decide if the feature is in the best interest of the
project. Not everything is and the maintainers need to evalulate how much
effort it is to maintain the feature. Once accepted into the source tree it
becomes the responsibility of the maintainers to keep the feature updated and
working.

Changes to the source tree are tracked using git. If you have not made changes
and enter the source tree and enter a git status command you will see nothing
has changed:

.. code-block:: none

  $ cd ../rtems
  $ git status
  On branch master
  Your branch is up-to-date with 'origin/master'.
  nothing to commit, working directory clean

We will make a change to the source code. In this example I change the help
message to the RTEMS shell's ``halt`` command. Running the same git status
command reports:

.. code-block:: none

  $ git status
  On branch master
  Your branch is up-to-date with 'origin/master'.
  Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working directory)

          modified:   cpukit/libmisc/shell/main_halt.c

  no changes added to commit (use "git add" and/or "git commit -a")

As an example I have a ticket open and the ticket number is 9876. I commit the
change with the follow git command:

.. code-block:: none

  $ git commit cpukit/libmisc/shell/main_halt.c

An editor is opened and I enter my commit message. The first line is a title
and the following lines form a body. My message is:

.. code-block:: none

  shell: Add more help detail to the halt command.

  Closes #9876.

  # Please enter the commit message for your changes. Lines starting
  # with '#' will be ignored, and an empty message aborts the commit.
  # Explicit paths specified without -i or -o; assuming --only paths...
  #
  # Committer: Chris Johns <chrisj@rtems.org>
  #
  # On branch master
  # Your branch is up-to-date with 'origin/master'.
  #
  # Changes to be committed:
  #       modified:   cpukit/libmisc/shell/main_halt.c

When you save and exit the editor git will report the commit's status:

.. code-block:: none

  $ git commit cpukit/libmisc/shell/main_halt.c
  [master 9f44dc9] shell: Add more help detail to the halt command.
   1 file changed, 1 insertion(+), 1 deletion(-)

You can either email the patch to :r:list:`devel` with the following git
command, and it is `minus one` on the command line:

.. code-block:: none

  $ git send-email --to=devel@rtems.org -1
   <add output here>

Or you can ask git to create a patch file using:

.. code-block:: none

  $ git format-patch -1
  0001-shell-Add-more-help-detail-to-the-halt-command.patch

This patch can be attached to a ticket.
