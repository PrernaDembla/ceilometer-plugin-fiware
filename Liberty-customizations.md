The starting point for the installation is the one used for the Kilo
version of Openstack, as OS will use Ubuntu 14.04.

Ceilometer - Monasca Installation
---------------------------------

We must start from a Working Ceilometer installation which is well
documented in the official documentation page.

### Install the plugins

Acording to this manual, we can install Monasca plugins for Ceilometer:

<https://github.com/SmartInfrastructures/ceilometer-plugin-fiware>

### Install Monasca Agent

In a new Virtualenvironment,

`virtualenv --system-site-packages --system-dist-packages fiware-mon`

I Install

`pip install monasca-agent=1.1.21-FIWARE`

After installing the agent, we need no modify the file
...../publisher/monclient.py

`- from ceilometer.openstack.common import loopingcall`\
`+ from oslo_service import loopingcall`

Problems
--------

### Installing Controllers

About: 3. Copy region directory structure from this repository into
Ceilometer package location (by default, at
\$PYTHON\_SITE\_PKG/ceilometer). After that, RegionPollster class should
be available:

-   Tenemos que usar el Pollster:
    <https://github.com/SmartInfrastructures/ceilometer-plugin-fiware>

fichero **region.py** --- Errores en la ejecución del fichero

`-from oslo.config import cfg`\
`-from oslo.utils import timeutils`\
`-from ceilometer.openstack.common import log`

`+from oslo_config import cfg`\
`+from oslo_utils import timeutils`\
`+from oslo_log import log`

### Installing Computes

About: 2. Copy **host.py** file from the compute\_pollster directory of
this repository into the compute/pollsters/ subdirectory at Ceilometer
package location. After that, HostPollster class should be available:

File **host.py** needs to be changed:

`-from ceilometer.openstack.common import log`\
`from ceilometer.compute import pollsters`\
`from ceilometer.i18n import _, _LW`\
`from ceilometer import sample`\
`-from oslo.utils import timeutils`\
`-from oslo.config import cfg`

`+from oslo_log import log`\
`from ceilometer.compute import pollsters`\
`from ceilometer.i18n import _, _LW`\
`from ceilometer import sample`\
`+from oslo_utils import timeutils`\
`+from oslo_config import cfg`

### Installing Monasca Libraries (monasca ceilometer plugin)

In **/opt** directory

`virtualenv --system-site-packages --system-dist-packages monasca`

And inside the virtualenv I install Monasca client libraries

`pip install python-monascaclient --allow-all-external`

We can have some problems because of the libraries in
monasca/ceilometer/other components, so I changed the init scripts used
to launch ceilometer agents, changing the line used to exec python in
those components in every file **/etc/init/ceilometer-\*.conf**, as an
example I'll show the difference between the file
**/etc/init/ceilometer-agent-central.conf** and the original one:

`-  --exec /opt/monasca/bin/python /usr/bin/ceilometer-polling -- --config-file=/etc/ceilometer/ceilometer.conf ${DAEMON_ARGS}`\
`+  --exec /opt/monasca/bin/python /usr/bin/ceilometer-polling -- --config-file=/etc/ceilometer/ceilometer.conf ${DAEMON_ARGS}`

This way, ceilometer services are always run inside a virtualenv'd
python.
