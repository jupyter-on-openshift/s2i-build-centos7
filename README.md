# s2i-build-centos7

This repository contains source code for an OpenShift Source-to-Image (S2I)
builder image for creating S2I builders using the S2I build process.

## Overview

This S2I builder is a very small layer on top of the existing
``s2i-base-centos7`` base image provided by OpenShift, which includes
essential libraries and tools for creating S2I builders for application
language runtime environments such as Ruby, NodeJS, Python, Perl and PHP.

This S2I builder needs to exist as the ``s2i-base-centos7`` base image
doesn't not switch to the ``default`` user with UID of 1001 and so cannot
be used directly as a S2I builder itself.

In addition to switching the user, this image also enables write access to
the directory ``/usr/libexec/s2i`` to make it easy to place custom S2I
scripts in the default S2I script location, avoiding the need to change the
image metadata describing where they reside.

The ``/etc/passwd`` file is also made writable and a ``fix-passwd-entry``
script provided. This will be run automatically from the container
entrypoint script to add an entry to ``/etc/passwd`` for the assigned user
ID that a container is run as. This avoids problems with applications which
expect a valid user entry in the ``/etc/passwd`` file for the user the
application runs as.

Finally, to make it easier to use, this S2I builder will do the work of
moving the S2I scripts provided in the source code used with the builder to
the appropriate S2I script location. It will also copy any image metadata
file defining the labels for the S2I builder created to the appropriate
location so the S2I build process finds them at the end of the build
process.

## Importing

To use the S2I builder in your OpenShift project, you can import it by
running:

```
oc import-image jupyteronopenshift/s2i-build-centos7 --confirm
```

If the configuration of your OpenShift environment allows you to use the
``docker`` build strategy, you can build the image yourself by running:

```
oc new-build https://github.com/jupyter-on-openshift/s2i-build-centos7
```

