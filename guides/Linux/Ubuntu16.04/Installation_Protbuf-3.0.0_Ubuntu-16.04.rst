===================================================================
 Installation of Google Protocol Buffers 3.0.0 on Ubuntu 16.04 LTS
===================================================================

:Author: François Mauger, LPC Caen <mauger@lpccaen.in2p3.fr>
:Date:   2017-01-18
:Version: 0.1

In this document  we propose an installation procedure  for the Google
Protocol Buffer  C++ library, version  3.0.0, on top of  Ubuntu 16.04
LTS (Xenial  Xerus) for a  64-bit system. Google Protocol  Buffer Java
support is also installed.

.. contents::

Useful links
------------

 * https://github.com/google/protobuf
 * https://developers.google.com/protocol-buffers/
 * https://developers.google.com/protocol-buffers/docs/cpptutorial
 * https://developers.google.com/protocol-buffers/docs/downloads
 * http://blog.codeclimate.com/blog/2014/06/05/choose-protocol-buffers/

Download
--------

In the following, we  will use the ``/opt/sw/GoogleProtocolBuffer`` as
the working directory to build and install the *protobuf* software:

.. code:: sh

   $ cd /opt/sw/
   $ mkdir GoogleProtocolBuffer
   $ wget https://github.com/google/protobuf/releases/download/v3.0.0/protobuf-cpp-3.0.0.tar.gz
   $ tar xvzf protobuf-cpp-3.0.0.tar.gz
   $ wget https://github.com/google/protobuf/releases/download/v3.0.0/protobuf-java-3.0.0.tar.gz
   $ tar xvzf protobuf-java-3.0.0.tar.gz
..


Installation
--------------------

Installation instructions: https://github.com/google/protobuf/blob/master/src/README.md

.. code:: sh

   $ export PROTOBUF_BASE_DIR=/opt/sw/GoogleProtocolBuffers
   $ mkdir -p ${PROTOBUF_BASE_DIR}
   $ cd ${PROTOBUF_BASE_DIR}
..

Unless it is  already available on your system,  install the following
software:

.. code:: sh

   $ sudo apt-get install autoconf automake libtool curl make g++ unzip
..

**Note:** For SuperNEMO  users, we switch to a  ``brew`` shell because
we provide  a specific version  of the GCC  compiler as well  as Boost
through it:

.. code:: sh

   $ brewsh
..

Build it !

.. code:: sh

   $ export PROTOBUF_INSTALL_DIR=${PROTOBUF_BASE_DIR}/install-3.0.0
   $ cd ${PROTOBUF_BASE_DIR}/protobuf-3.0.0/
   $ ./autogen.sh
   $ ./configure --help
   $ ./configure --prefix=${PROTOBUF_INSTALL_DIR}
   $ make -j4
   $ make check
   $ make install
   ...
   ----------------------------------------------------------------------
   Libraries have been installed in:
      /opt/sw/GoogleProtocolBuffers/install-3.0.0/lib

   If you ever happen to want to link against installed libraries
   in a given directory, LIBDIR, you must either use libtool, and
   specify the full pathname of the library, or use the `-LLIBDIR'
   flag during linking and do at least one of the following:
   - add LIBDIR to the `LD_LIBRARY_PATH' environment variable
     during execution
   - add LIBDIR to the `LD_RUN_PATH' environment variable
     during linking
   - use the `-Wl,-rpath -Wl,LIBDIR' linker flag
   - have your system administrator add LIBDIR to `/etc/ld.so.conf'

   See any operating system documentation about shared libraries for
   more information, such as the ld(1) and ld.so(8) manual pages.
..

Java
--------------------

Install Maven
~~~~~~~~~~~~~~~~~~~~~~~~~

Java support is provided throught the Maven utility:

.. code:: sh

   $ sudo apt-get install default-jdk default-jdk-doc maven
..

See also http://maven.apache.org/guides/mini/guide-proxies.html to use
Maven behind a proxy (use your own ``~/.m2/settings.xml`` file).


Example of a ``~/.m2/settings.xml`` file that supports a proxy (with authentication):

.. code:: sh

   <?xml version="1.0" encoding="UTF-8"?>
   <settings>
     <proxies>
       <proxy>
       <id>lpccaen-proxy</id>
       <active>true</active>
       <protocol>http</protocol>
       <host>proxy.acme.org</host>
       <port>3128</port>
       <username>LOGIN</username>
       <password>PASSWORD</password>
       <nonProxyHosts>www.google.com|*.example.com</nonProxyHosts>
       </proxy>
     </proxies>
   </settings>
..


Build/install Protobuf Java
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Build the protobuf *jar* files:

.. code:: sh

   $ export PATH="${PROTOBUF_INSTALL_DIR}/bin:${PATH}"
   $ protoc --version
   libprotoc 3.0.0
   $ mvn -v
   Apache Maven 3.3.9
   ...
   $ cd ${PROTOBUF_BASE_DIR}/protobuf-3.0.0/java
   $ mvn test
   $ mvn install
   $ mvn package
   $ find . -name "protobuf-*.jar"
   ./core/target/protobuf-java-3.0.0-beta-3.jar
   ./util/target/protobuf-java-util-3.0.0-beta-3.jar
..

Install them in some standard directory:

.. code:: sh

   $ mkdir -p ${PROTOBUF_INSTALL_DIR}/lib/java
   $ cp ./core/target/protobuf-java-3.0.0.jar ${PROTOBUF_INSTALL_DIR}/lib/java
   $ cp ./util/target/protobuf-java-util-3.0.0.jar ${PROTOBUF_INSTALL_DIR}/lib/java
..

Check your Protobuf installation
---------------------------------

.. code:: sh

   $ LANG=C tree -L 1 ${PROTOBUF_INSTALL_DIR}
   /opt/sw/GoogleProtocolBuffers/install-3.0.0
   |-- bin
   |-- include
   `-- lib
..

.. code:: sh

   $ LANG=C tree -L 2 ${PROTOBUF_INSTALL_DIR}/lib
   /opt/sw/GoogleProtocolBuffers/install-3.0.0/lib/
   |-- java
   |   |-- protobuf-java-3.0.0.jar
   |   `-- protobuf-java-util-3.0.0.jar
   |-- libprotobuf-lite.a
   |-- libprotobuf-lite.la
   |-- libprotobuf-lite.so -> libprotobuf-lite.so.10.0.0
   |-- libprotobuf-lite.so.10 -> libprotobuf-lite.so.10.0.0
   |-- libprotobuf-lite.so.10.0.0
   |-- libprotobuf.a
   |-- libprotobuf.la
   |-- libprotobuf.so -> libprotobuf.so.10.0.0
   |-- libprotobuf.so.10 -> libprotobuf.so.10.0.0
   |-- libprotobuf.so.10.0.0
   |-- libprotoc.a
   |-- libprotoc.la
   |-- libprotoc.so -> libprotoc.so.10.0.0
   |-- libprotoc.so.10 -> libprotoc.so.10.0.0
   |-- libprotoc.so.10.0.0
   `-- pkgconfig
       |-- protobuf-lite.pc
       `-- protobuf.pc
..


Setup Protobuf
------------------------------

In your ``.bashrc`` script, add:

.. code:: sh

   export PROTOBUF_BASE_DIR=/opt/sw/GoogleProtocolBuffers
   function do_protobuf_300_setup()
   {
     if [ -n "${PROTOBUF_INSTALL_DIR}" ]; then
       echo "WARNING: Google Protocol Buffers is already setup !" >&2
       return 1
     fi
     export PROTOBUF_INSTALL_DIR="${PROTOBUF_BASE_DIR}/install-3.0.0"
     export PATH="${PROTOBUF_INSTALL_DIR}/bin:${PATH}"
     echo "NOTICE: Google Protocol Buffers is now setup." >&2
     return 0;
   }
   export -f do_protobuf_300_setup

   # Special aliases:
   alias protobuf_setup="do_protobuf_300_setup"
..


To setup the protobuf libraries and tools from a terminal, run :

.. code:: sh

   $ protobuf_setup
..

Check the ``protoc`` compiler:

.. code:: sh

   $ which protoc
   /opt/sw/GoogleProtocolBuffers/install-3.0.0/bin/protoc
..


.. end
