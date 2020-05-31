---
layout: docs
title: Installing on Docker
short_title: Docker
---

T3ST1NG

[Docker] is an application that treats a whole Linux machine,
including its operating system and installed applications, as a
computer-within-a-computer, called a "container."  "Containers" are
similar to a [virtual machine] in many respects.  They are typically
used for "shipping" applications.  Instead of installing an
application on a server directly, you can run the application in a
"container."  This way, the application runs bundled with all of the
operating system software that it needs.  Installing applications is
quicker, simpler, and less error-prone.  There is virtually no
performance degredation.

[Docker] is a good platform for trying out **docassemble** for the
first time.  It is also ideal in a production environment.

Since the **docassemble** application depends on so many different
component parts, including a web server, SQL server, Redis server,
distributed task queue, background task system, scheduled task system,
and other components, running it inside of a [Docker] container is
convenient.  When all of these components are running inside of a
"container," you don't have to do the work of installing and
maintaining these components.

As much as [Docker] simplifies the process of installing
**docassemble**, it takes some time to understand the concepts behind
"running," "stopping," and "starting" containers.

If you are not familiar with [Docker] or with hosting web
applications, and you want to get up and running fast, you may want to
use one of the [third party providers] that provide
**docassemble**-based interview development platforms.

[Docker] can also be used to deploy even the most complex
**docassemble** installations.  For example, [Kubernetes] or Amazon's
[EC2 Container Service] can be used to maintain a cluster of
**docassemble** web server instances, created from [Docker] images,
that communicate with a central server.  For information about how to
install **docassemble** in a multi-server arrangement, see the
[scalability] section.

[Docker] is a complex and powerful tool, and the **docassemble**
documentation is not a substitute for [Docker] documentation.  If you
are new to [Docker], you should learn about [Docker] by reading
tutorials or watching videos.  Here is a brief cheat sheet based on
loose real-world analogies:

* Doing [`docker run`] is analogous to getting a Windows
  installation DVD, installing it on a computer with an empty hard
  drive, and then booting the computer for the first time.
* Doing [`docker pull`] is analogous to going to a store and obtaining
  a Windows installation DVD.
* Doing [`docker stop`] is analogous to turning off a computer (and
  forcibly unplugging it after a certain number of seconds after you
  initiate the shutdown from the Windows "start" menu).
* Doing [`docker start`] is analogous to turning on a computer.
* Doing [`docker rm`] is analogous to tossing a computer into a
  trash incinerator.
* Doing [`docker rmi`] is analogous to tossing a Windows installation
  DVD into a trash incinerator.
* Doing [`docker exec`] is analogous to sitting down at your computer
  and opening up PowerShell.
* Doing [`docker ps`] is analogous to walking around your house and
  making a list of your computers.
* Doing [`docker volume`] is analogous to doing things with USB drives.
* Doing [`docker build`] is analogous to creating a Windows
  installation DVD based on the Windows source code.

In these analogies, a [Docker] "image" is analogous to a Windows
installation DVD, a [Docker] "container" is analogous to a particular
computer that runs Windows, and a [Docker] "volume" is (very loosely)
analogous to a USB drive.

# <a name="where"></a>Choosing where to run Docker

[Docker] can be run on a Windows PC, a Mac, an on-site Linux machine,
or a Linux-based virtual machine in the cloud.  Since **docassemble**
is a web application, the ideal platform is a Linux virtual machine in
the cloud.

You can test out **docassemble** on a PC or a Mac, but for serious,
long-term deployment, it is worthwhile to run it in the cloud, or on a
dedicated on-premises server.  Running [Docker] on a machine that
shuts down or restarts frequently could lead to [database corruption].

If you have never deployed a Linux-based virtual machine in the cloud
before, this might be a good opportunity to learn.  The ability to use
virtual machines on a cloud provider like [Amazon Web Services] or
[Microsoft Azure] is a valuable and transferable skill.  Learning how
to do cloud computing is beyond the scope of this documentation, but
there are many guides on the internet.  The basic steps of running
Docker in the cloud are:

1. Create an account with a cloud computing provider.
2. Start a [sufficiently powerful](#install) virtual machine that runs
   some flavor of Linux.
3. Connect to the virtual machine using [SSH] in order to control it
   using a command line.  This can be a complicated step because most
   providers use certificates rather than passwords for authentication.
4. [Install Docker](#install) on the virtual machine.

There are also methods of controlling cloud computing resources from a
local command line, where you type special commands to deploy [Docker]
containers.  These can be very useful, but they tend to be more
complicated to use than the basic [Docker] command line.

# <a name="install"></a>Installing Docker

First, make sure you are running [Docker] on a computer or virtual
computer with at least 4GB of memory and 20GB of hard drive space.
The **docassemble** installation will use up about 10GB of space, and
you should always have at least 10GB free when you are running
**docassemble**.

If you have a Windows PC, follow the
[Docker installation instructions for Windows]{:target="_blank"}.  You
will need administrator access on your PC in order to install (or
upgrade) Docker.

If you have a Mac, follow the [Docker installation instructions for OS X]{:target="_blank"}.

On Ubuntu (assuming username `ubuntu`):

{% highlight bash %}
sudo apt-get -y update
sudo apt-get -y install docker.io
sudo usermod -a -G docker ubuntu
{% endhighlight %}

On [Amazon Linux] (assuming the username `ec2-user`):

{% highlight bash %}
sudo yum -y update
sudo yum -y install docker
sudo usermod -a -G docker ec2-user
{% endhighlight %}

The `usermod` line allows the non-root user to run [Docker].  You may
need to log out and log back in again for this new user permission to
take effect.  On some distributions, the `docker` group is not created
by the installation process, so you will need to manually create it by
running `sudo groupadd docker` before you run the `usermod` command.

[Docker] will probably start automatically after it is installed.  On
Linux, you many need to do `sudo /etc/init.d/docker start`, `sudo
systemctl start docker`, or `sudo service docker start`.

# <a name="single server arrangement"></a>Quick start

If you just want to test out **docassemble** for the first time,
follow the instructions in this section, and you'll get
**docassemble** up and running quickly in a [Docker] container,
whether you are using a laptop or [AWS].

However, you should think of this as an educational exercise; don't
start using the container for serious development work.  For a serious
implementation, you will want to go through additional setup steps,
such as configuring [HTTPS] for encryption and [data storage] for the
safe, long-term storage of development data and user data.

## <a name="starting"></a>Starting

Once [Docker] is installed, you can install and run **docassemble** from the command line.

To get a command line on Windows, run [Windows PowerShell].

To get a command line on a Mac, launch the [Terminal] application.

To get a command line on a virtual machine in the cloud, follow your
provider's instructions for using [SSH] to connect to your machine.

From the command line, simply type in:

{% highlight bash %}
docker run -d -p 80:80 -p 443:443 --stop-timeout 600 jhpyle/docassemble
{% endhighlight %}

The [`docker run`] command will download and run **docassemble**,
making the application available on the standard HTTP port (port 80)
of your machine.

It will take several minutes for **docassemble** to download, and once
the [`docker run`] command finishes, **docassemble** will start to
run.  After a few minutes, you can point your web browser to the
hostname of the machine that is running [Docker].  If you are running
[Docker] on your own computer, this address is probably
http://localhost.

The number of minutes **docassemble** takes to start up depends on the
speed of the network connection.  A number of things happen during the
boot process, but the main bottleneck is the updating of software
packages.  On some systems it may take up to 20 minutes for
**docassemble** to boot.  If you want to investigate what is happening
on the server, see the [troubleshooting] section.

If you are running [Docker] on [AWS], the address will be something
like `http://ec2-52-38-111-32.us-west-2.compute.amazonaws.com` (check
your [EC2] configuration for the hostname).  On [AWS], you will need a
[Security Group] that opens [HTTP] (port 80) to the outside world in
order to allow web browsers to connect to your [EC2] instance.

Using the web browser, you can log in using the default username
("admin@admin.com") and password ("password"), and make changes to the
configuration from the menu.

In the [`docker run`] command, the `-d` flag means that the container
will run in the background.

The `-p` flag maps a port on the host machine to a port on the
[Docker] container.  In this example, port 80 on the host machine will
map to port 80 within the [Docker] container.  If you are already
using port 80 on the host machine, you could use `-p 8080:80`, and
then port 8080 on the host machine would be passed through to port 80
on the [Docker] container.

The `jhpyle/docassemble` tag refers to a [Docker] image that is
[hosted on Docker Hub].  The image is about 2GB in size, and when it
runs, the container uses about 10GB of hard drive space.  The
`jhpyle/docassemble` image is based on the "master" branch of the
[docassemble repository] on [GitHub].  It is rebuilt every time the
minor version of **docassemble** increases.

## <a name="shutdown"></a>Shutting down

You can shut down the container by running:

{% highlight bash %}
docker stop -t 600 <containerid>
{% endhighlight %}

By default, [Docker] gives containers ten seconds to shut down before
forcibly shutting them down.  Usually, ten seconds is plenty of time,
but if the server is slow, **docassemble** might take a little longer
than ten seconds to shut down.  To be on the safe side, give the
container plenty of time to shut down gracefully.  The `-t 600` means
that [Docker] will wait up to ten minutes before forcibly shutting
down the container.  It will probably take no more than 15 seconds for
the [`docker stop`] command to complete, although it can take as long
as a minute to stop a container if you are using [Azure Blob
Storage](#persistent azure).

It is very important to avoid a forced shutdown of **docassemble**.
The container runs a [PostgreSQL] server (unless configured to use an
external SQL server), and the data files of the server may become
corrupted if [PostgreSQL] is not gracefully shut down.  To facilitate
[data storage] (more on this later), **docassemble** backs up your
data during the shutdown process and restores from that backup during
the initialization process.  If the shutdown process is interrupted,
your data may be left in an inconsistent state and there may be errors
during later initialization.

To see a list of stopped containers, run `docker ps -a`.  To remove a
container, run `docker rm <containerid>`.

## <a name="starting after shutdown"></a>Restarting the container after a shutdown

If you have shut down a [Docker] container using [`docker stop -t 600`],
you can start the container again:

{% highlight bash %}
docker start <containerid>
{% endhighlight %}

# <a name="overview"></a>Overview of the Docker container

There are a variety of ways to deploy **docassemble** with [Docker],
but this subsection will give an overview of the most common way,
which is to use a single [Docker] container hosted on a cloud
provider.

When you run [`docker run`] on the "image" `jhpyle/docassemble`,
[Docker] will go onto the internet, download ("pull") the
`jhpyle/docassemble` image, create a new container using that image,
and then "start" that container.  However, first it will check to see
if a copy of the `jhpyle/docassemble` image has already been
downloaded, and if there is a copy already downloaded, it will create
the container using that copy.  This is important to keep in mind;
when you run `docker run`, you might be thinking you will always get
the most recent version, but that is not the case.  (See [upgrading],
below, for more information.)

When the **docassemble** container starts, it runs one command:

{% highlight bash %}
/usr/bin/supervisord -n -c /etc/supervisor/supervisord.conf
{% endhighlight %}

(This is specified in the [`Dockerfile`], if you are curious.)

This command starts an application called [Supervisor].  [Supervisor]
is a "process control system" that starts up the various applications
that **docassemble** uses, including:

* A web server, [NGINX], which is called `nginx` within the Supervisor
  configuration.
* A application server, [uWSGI], called `uwsgi`.
* A background task system, [Celery], called `celery`.
* A scheduled task runner, called `cron`.
* A SQL server, [PostgreSQL], called `postgres`.
* A distributed task queue system, [RabbitMQ], called `rabbitmq`.
* An in-memory data structure store, [Redis], called `redis`.
* A watchdog daemon that looks for out-of-control processes and
  kills them, called `watchdog`.
* A [WebSocket] server that supports the [live help] functionality,
  called `websockets`.

In addition to starting background tasks, [Supervisor] coordinates the
running of ad-hoc tasks, including:

* A script called `sync` that consolidates log files in one place,
  to support the [Logs] interface.
* A script called `reset` that restarts the server.
* A script called `update` that installs and upgrades the [Python]
  packages on the system.

There is also a [Supervisor] service called `syslogng`, which is
dormant on a single-server system.  (The [syslog-ng] application is
used in the multi-server arrangement to consolidate log files from
multiple machines.)

Finally, there is a service called `initialize`, which runs
automatically when [Supervisor] starts.  This is a shell script that
initializes the server and starts the other services in the correct
order.

# <a name="troubleshooting"></a>Troubleshooting

You should not need to access the running container in order to get
**docassemble** to work, and all the log files you need will hopefully
be available from [Logs] in the web browser.  However, you might want
to gain access to the running container for some reason.

To do so, find out the ID of the running container by doing
[`docker ps`].  You will see output like the following:

{% highlight text %}
CONTAINER ID  IMAGE  COMMAND  CREATED  STATUS  PORTS  NAMES
e4fa52ba540e  jhpyle/docassemble  "/usr/bin/supervisord" ...
{% endhighlight %}

The ID is in the first column.  Then run:

{% highlight bash %}
docker exec -t -i e4fa52ba540e /bin/bash
{% endhighlight %}

using your own ID in place of `e4fa52ba540e`.  This will give you a
command prompt within the running container.

The first thing to check when you connect to a container is:

{% highlight bash %}
supervisorctl status
{% endhighlight %}

The output should be something like:

{% highlight text %}
apache2                          STOPPED   Not started
celery                           RUNNING   pid 1865, uptime 0:14:45
cron                             STOPPED   Not started
initialize                       RUNNING   pid 1014, uptime 0:14:53
nginx                            RUNNING   pid 1088, uptime 0:14:16
postgres                         RUNNING   pid 1020, uptime 0:14:42
rabbitmq                         RUNNING   pid 1045, uptime 0:14:38
redis                            RUNNING   pid 1067, uptime 0:14:35
reset                            STOPPED   Not started
sync                             EXITED    Dec 22 07:22 AM
syslogng                         STOPPED   Not started
update                           STOPPED   Not started
uwsgi                            RUNNING   pid 1070, uptime 0:14:15
watchdog                         RUNNING   pid 1013, uptime 0:14:53
websockets                       RUNNING   pid 1904, uptime 0:14:40
{% endhighlight %}

If you are running **docassemble** in a single-server arrangement, the
processes that should be "RUNNING" include `celery`, `initialize`,
`nginx`, `postgres`, `rabbitmq`, `redis`, `uwsgi`, `watchdog`, and
`websockets`.

Log files on the container that you might wish to check include:

* `/usr/share/docassemble/log/docassemble.log` (log for the web application)
* `/usr/share/docassemble/log/worker.log` (log for [background processes])
* `/usr/share/docassemble/log/uwsgi.log` (log for the core of the web application)
* `/var/log/nginx/error.log` (log for the web server)
* `/var/log/supervisor/initialize-stderr---supervisor-*.log` (log for
  the startup process)
* `/var/log/supervisor/postgres-stderr---supervisor-*.log` (log for
  the SQL server)
* Other files in `/var/log/supervisor/` (logs for other services)
* `/usr/share/docassemble/log/websockets.log` (log for parts of the
  [live help] feature)
* `/var/spool/mail/mail` (log for [scheduled tasks], generated by `cron`)
* `/tmp/flask.log` (log used by [Flask] in rare situations)

To navigate through the directories on the system, use [`cd`] to
change your current directory and [`ls`] to list the files in a
directory.  To view the contents of a file, type, e.g.,:

{% highlight bash %}
less /usr/share/docassemble/log/docassemble.log
{% endhighlight %}

Inside the [`less`] program, you can type spacebar to go to the next
page, `G` to go to the end of the file, `1G` to go to the start of the
file, and `q` to quit.

Enter `exit` to leave the container and get back to your standard
command prompt.

If `supervisorctl status` shows that the `initialize` service is in
`EXITED` or `FAILED` status, then there should be an error message in
the file `/var/log/supervisor/initialize-stderr---supervisor-*.log`
indicating what went wrong that prevented **docassemble** from
initializing.  You will need to fix that problem, then type `exit` to
leave the container, and then restart your container by doing `docker
stop -t 600 <containerid>` followed by `docker start <containerid>`.

If you are get a "server error" in your web browser when trying to
access **docassemble**, there should be an error message in
`/usr/share/docassemble/log/uwsgi.log`.  If you see a message about a
"blueprint's name collision," this is almost always not the real
error; you need to scroll up through several error messages to find
the actual error.  When the web application crashes, the error that
initiated the crash causes other errors inside of the code of the
[Flask] framework, and a "blueprint's name collision" error is
typically the last error to be recorded in the error log.

If you encounter a problem with upgrading or installing packages,
check `/usr/share/docassemble/log/worker.log`.  This is the error log
for the [Celery] background process system.  A [Celery] background
task controls the upgrading and installation of packages, so if you
get an error during upgrading or installation of packages, make sure
to check here first.

If you need to change the [Configuration] but you cannot use the [web
interface] to do so because your container failed to start, or the web
application does not work, you can edit the [Configuration] manually.
The main configuration file is located at
`/usr/share/docassemble/config/config.yml`.

Because of the way that [data storage] works, however, you need to be
careful about editing the [Configuration] file directly.  If you are
using [S3](#persistent S3) or [Azure Blob Storage](#persistent azure),
then during the container initialization process, the file will be
overwritten with the copy of `config.yml` that is stored in the cloud.
If you are not using cloud-based [data storage], then when a container
safely shuts down, `/usr/share/docassemble/config/config.yml` will be
copied to `/usr/share/docassemble/backup/config.yml`, and when a
container starts up, `/usr/share/docassemble/backup/config.yml` will
be copied to `/usr/share/docassemble/config/config.yml`, overwriting
the existing contents.  This is part of the operation of the [data
storage] feature; it makes it possible for you to remove a container
and `docker run` a new one while retaining all of your data.

If you are using [S3](#persistent S3) or [Azure Blob
Storage](#persistent azure), then you should [`docker stop -t 600`] the
container, then edit the `config.yml` file through the cloud service
web interface (usually by downloading, editing, and uploading), and
then `docker start` the container again.

If you are not using [S3](#persistent S3) or [Azure Blob
Storage](#persistent azure), then you can edit the [Configuration]
file using an editor like [`nano`].  If the status of `initialize` is
`RUNNING`, edit `/usr/share/docassemble/config/config.yml` file, and
then do `supervisorctl start reset` to restart the **docassemble**
services so that they use the new [Configuration].  When the container
stops, it will safely shut down, and
`/usr/share/docassemble/config/config.yml` will be backed up to
`/usr/share/docassemble/backup/config.yml`.  If you are using
[persistent volumes], the `backup` folder will be in the Docker volume
that will persist even if you `docker rm` the container.  If the
status of `initialize` is `FAILED` or `EXITED`, then this backup
process will not take place; in that case, you should make your
changes to `/usr/share/docassemble/backup/config.yml`, and then
restart your container by doing [`docker stop -t 600`] followed by
`docker start`.

If you need to make manual changes to the installation of [Python]
packages, note that **docassemble**'s [Python] code is installed in a
[Python virtual environment] in which all of the files are readable
and writable by the `www-data` user.  The virtual environment is
located at `/usr/share/docassemble/local3.6/`.  Thus, installing
[Python] packages through Debian's `apt-get` utility will not actually
make that [Python] code available to **docassemble**.  Before using
[pip], you need to first change the user to `www-data`, and then
switch into the appropriate [Python virtual environment].

{% highlight bash %}
su www-data
source /usr/share/docassemble/local3.6/bin/activate
{% endhighlight %}

Note that if you want to install a new version of a [Python] package
that may already be installed, you will want to use the `--upgrade`
parameter.

{% highlight bash %}
pip install --upgrade azure-storage
{% endhighlight %}

To stop using the [Python virtual environment], type the command
`deactivate`.  To stop being the `www-data` user, type the command
`exit`.

Services other than [NGINX] and [uWSGI] are an important part of
**docassemble**'s operations.  For example, the upgrading and
installation of [Python] packages takes place in a background process
operated by the `celery` service.  In addition, the [live help]
feature uses a service called `websockets`.  The `nginx`, `uwsgi`,
`celery`, and `websockets` services all need to be restarted every
time there is a change to the [Configuration] or a change to [Python]
code.  To restart all of the services at once, you can do:

{% highlight bash %}
supervisorctl start reset
{% endhighlight %}

However, if the `uwsgi` process has crashed, then you need to do:

{% highlight bash %}
supervisorctl restart uwsgi
supervisorctl start reset
{% endhighlight %}

You need to manually restart the `uwsgi` process here because the
`reset` process uses an optimized method of refreshing the application
server.  This usually works well when you make [Configuration] and
[Python] code changes, but if [uWSGI] has crashed, `supervisorctl
start reset` will not bring it back to life.

If you want to access the [Redis] data, do [`docker exec`] to get
inside the container and then run `redis-cli` (assuming that your
[Redis] server is the default local [Redis] server).  Note that
**docassemble** uses several of the [Redis] databases.  If you do
`redis-cli -n 1` (the default), you will access the database used on a
system level.  If you do `redis-cli -n 2`, you will access the
database used by [`DARedis`].

Unless you specify a different SQL server, the [PostgreSQL] data for
your **docassemble** server is inside the `docassemble` database
running on the Docker container.  The default username is
`docassemble` and the default password is `abc123`.  After doing
[`docker exec`] to get inside the container, run:

{% highlight bash %}
psql -U docassemble -d docassemble -h localhost -W
{% endhighlight %}

When prompted, enter password `abc123`.

For more information about troubleshooting **docassemble**, see
the [debugging subsection] of the [installation] section.

# <a name="configuration options"></a>Configuration options

In the example [above](#single server arrangement), we started
**docassemble** with `docker run -d -p 80:80 jhpyle/docassemble`.
This command will cause **docassemble** to use default values for all
configuration options.  You can also communicate specific
configuration options to the container.

The recommended way to do this is to create a text file called
`env.list` in the current working directory containing environment
variable definitions in standard shell script format.  For example:

{% highlight text %}
DAHOSTNAME=docassemble.example.com
USEHTTPS=true
USELETSENCRYPT=true
LETSENCRYPTEMAIL=admin@example.com
{% endhighlight %}

Then, you can pass these environment variables to the container using
the [`docker run`] command:

{% highlight bash %}
docker run --env-file=env.list -d -p 80:80 -p 443:443 --stop-timeout 600 jhpyle/docassemble
{% endhighlight %}

These configuration options will cause [NGINX] to use
docassemble.example.com as the server name and use [HTTPS] with
certificates hosted on [Let's Encrypt].  (The flag `-p 443:443` is
included so that the [HTTPS] port is exposed.)

If you want your server to be able to accept incoming [e-mails], you
will need to add `-p 25:25` in order to open port 25.  See the
[e-mailing the interview] section for information about configuring
your server to receive e-mails.

A [template for the `env.list` file] is included in distribution.

When running **docassemble** in [ECS], environment variables like
these are specified in [JSON] text that is entered into the web
interface.  (See the [scalability] section for more information about
using [ECS].)

In your `env.list` file, you can set a variety of options.  These
options are case specific, so you need to literally specify `true` or
`false`, because `True` and `False` will not work.

The following two options are specific to the particular server being
started (which, in a [multi-server arrangement], will vary from server
to server).

* <a name="CONTAINERROLE"></a>`CONTAINERROLE`: either `all` or a
  colon-separated list of services (e.g. `web:celery`,
  `sql:log:redis`, etc.) that should be started by the server.  It is
  only necessary to set a `CONTAINERROLE` if you are using a
  [multi-server arrangement].  The available options are:
  * `all`: the [Docker] container will run all of the services of
    **docassemble** on a single container.
  * `web`: The [Docker] container will serve as a web server.
  * `celery`: The [Docker] container will serve as a [Celery] node.
  * `sql`: The [Docker] container will run the central [PostgreSQL] service.
  * `cron`: The [Docker] container will run [scheduled tasks] and
    other necessary tasks, such as updating SQL tables.
  * `redis`: The [Docker] container will run the central [Redis] service.
  * `rabbitmq`: The [Docker] container will run the central [RabbitMQ] service.
  * `log`: The [Docker] container will run the central log aggregation service.
  * `mail`: The [Docker] container will run [Exim] in order to accept [e-mails].

* <a name="SERVERHOSTNAME"></a>`SERVERHOSTNAME`: In a
  [multi-server arrangement], all **docassemble** application servers
  need to be able to communicate with each other using port 9001 (the
  [supervisor] port).  All application servers "register" with the
  central SQL server.  When they register, they each provide their
  hostname; that is, the hostname at which the specific individual
  application server can be found.  Then, when an application server
  wants to send a message to the other application servers, the
  application server can query the central SQL server to get a list of
  hostnames of other application servers.  This is necessary so that
  any one application server can send a signal to the other
  application servers to install a new package or a new version of a
  package, so that all servers are running the same software.  If you
  are running **docassemble** in a [multi-server arrangement], and you
  are starting an application server, set `SERVERHOSTNAME` to the
  hostname with which other application servers can find that server.
  Note that you do not need to worry about setting `SERVERHOSTNAME` if
  you are using [EC2], because [Docker] containers running on [EC2]
  can discover their actual hostnames by querying a specific IP
  address.

The other options you can set in `env.list` are global for your entire
**docassemble** installation, rather than specific to the server being
started.

The following eight options indicate where an existing configuration
file can be found on [S3](#persistent s3) or
[Azure blob storage](#persistent azure).  If a [configuration] file
exists in the cloud at the indicated location, that [configuration]
file will be used to set the [configuration] of your **docassemble**
installation.  If no [configuration] file yet exists in the cloud at the
indicated location, **docassemble** will create an initial
[configuration] file and store it in the indicated location.

* <a name="S3ENABLE"></a>`S3ENABLE`: Set this to `true` if you are
  using [S3] (or [S3]-compatible object storage service) as a
  repository for uploaded files, [Playground] files, the
  [configuration] file, and other information.  This environment
  variable, along with others that begin with `S3`, populates values
  in [`s3` section] of the initial [configuration] file.  If this is
  unset, but [`S3BUCKET`] is set, it will be assumed to be `true`.
* <a name="S3BUCKET"></a>`S3BUCKET`: If you are using [S3], set this
  to the bucket name.  Note that **docassemble** will not create the
  bucket for you.  You will need to create it for yourself
  beforehand.  The bucket should be empty.
* <a name="S3ACCESSKEY"></a>`S3ACCESSKEY`: If you are using [S3], set
  this to the [S3] access key.  You can ignore this environment
  variable if you are using [EC2] with an [IAM] role that allows
  access to your [S3] bucket.
* <a name="S3SECRETACCESSKEY"></a>`S3SECRETACCESSKEY`: If you are
  using [S3], set this to the [S3] access secret.  You can ignore this
  environment variable if you are using [EC2] with an [IAM] role that
  allows access to your [S3] bucket.
* <a name="S3REGION"></a>`S3REGION`: If you are using [S3], set this
  to the [region] you are using (e.g., `us-west-1`, `us-west-2`,
  `ca-central-1`).
* <a name="S3ENDPOINTURL"></a>`S3ENDPOINTURL`: If you are using an
  [S3]-compatible object storage service, set `S3ENDPOINTURL` to the URL
  of the service (e.g., `https://mys3service.com`).
* <a name="AZUREENABLE"></a>`AZUREENABLE`: Set this to `true` if you
  are using [Azure blob storage](#persistent azure) as a repository
  for uploaded files, [Playground] files, the [configuration] file,
  and other information.  This environment variable, along with others
  that begin with `AZURE`, populates values in [`azure` section] of
  the [configuration] file.  If this is unset, but
  [`AZUREACCOUNTNAME`], [`AZUREACCOUNTKEY`], and [`AZURECONTAINER`]
  are set, it will be assumed to be `true`.
* <a name="AZURECONTAINER"></a>`AZURECONTAINER`: If you are using
  [Azure blob storage](#persistent azure), set this to the container
  name.  Note that **docassemble** will not create the container for you.
  You will need to create it for yourself beforehand.
* <a name="AZUREACCOUNTNAME"></a>`AZUREACCOUNTNAME`: If you are using
  [Azure blob storage](#persistent azure), set this to the account
  name.
* <a name="AZUREACCOUNTKEY"></a>`AZUREACCOUNTKEY`: If you are
  using [Azure blob storage], set this to the account key.

The options listed below are "setup" parameters that are useful for
pre-populating a fresh [configuration] with particular values.  These
environment variables are effective only during an initial `run` of
the [Docker] container, when a [configuration] file does not already
exist.

If you are using [persistent volumes], or you have set the options
above for [S3](#persistent s3)/[Azure blob storage](#persistent azure)
and a [configuration] file exists in your cloud storage, the values in
that stored [configuration] file will, by default, take precedence
over any values you specify in `env.list`.  If you are using
[S3](#persistent s3)/[Azure blob storage](#persistent azure), you can
edit these configuration files in the cloud and then stop and start
your container for the new configuration to take effect.

* <a name="DAWEBSERVER"></a>`DAWEBSERVER`: This can be set either to
  `nginx` (the default) or `apache`.  See the [`web server`]
  configuration directive.
* <a name="DBHOST"></a>`DBHOST`: The hostname of the [PostgreSQL]
  server.  Keep undefined or set to `null` in order to use the
  [PostgreSQL] server on the same host.  This environment variable,
  along with others that begin with `DB`, populates values in [`db`
  section] of the [configuration] file.  If you are using a managed
  SQL database service, set `DBHOST` to the hostname of the database
  service.  If you are using [PostgreSQL] and the database referenced
  by `DBNAME` does not exist on the SQL server, the [Docker] startup
  process will attempt to use the `DBUSER` and `DBPASSWORD`
  credentials to create the database.  Otherwise, you need to make
  sure the database by the name of `DBNAME` exists before
  **docassemble** starts.
* <a name="DBNAME"></a>`DBNAME`: The name of the database.  The
  default is `docassemble`.
* <a name="DBUSER"></a>`DBUSER`: The username for connecting to the
  [PostgreSQL] server.  The default is `docassemble`.
* <a name="DBPASSWORD"></a>`DBPASSWORD`: The password for connecting
  to the SQL server.  The default is `abc123`.  The password cannot
  contain the character `#`.
* <a name="DBPREFIX"></a>`DBPREFIX`: This sets the prefix for the
  database specifier.  The default is `postgresql+psycopg2://`.  This
  corresponds with the `prefix` of the [`db`] configuration directive.
* <a name="DBPORT"></a>`DBPORT`: This sets the port that
  **docassemble** will use to access the SQL server.  If you are using
  the default port for your database backend, you do not need to set
  this.
* <a name="DBTABLEPREFIX"></a>`DBTABLEPREFIX`: This allows multiple
  separate **docassemble** implementations to share the same SQL
  database.  The value is a prefix to be added to each table in the
  database.
* <a name="DBBACKUP"></a>`DBBACKUP`: Set this to `false` if you are
  using an off-site [PostgreSQL] `DBHOST` and you do not want the
  database to be backed up by the daily cron job.  This is important
  if the off-site SQL database is large compared to the available disk
  space on the server.  The default value is `true`.
* <a name="DASQLPING"></a>`DASQLPING`: If your docassemble server runs
  in an environment in which persistent SQL connections will
  periodically be severed, you can set `DASQLPING: true` in order to
  avoid errors.  There is an overhead cost to using this, so only
  enable this if you get SQL errors when trying to connect after a
  period of inactivity.  The default is `false`.  See the [`sql ping`]
  configuration directive.
* <a name="EC2"></a>`EC2`: Set this to `true` if you are running
  [Docker] on [EC2].  This tells **docassemble** that it can use an
  [EC2]-specific method of determining the hostname of the server on
  which it is running.  See the [`ec2`] configuration directive.
* <a name="COLLECTSTATISTICS"></a>`COLLECTSTATISTICS`: Set this to
  `true` if you want the server to use [Redis] to track the number of
  interview sessions initiated.  See the [`collect statistics`]
  configuration directive.
* <a name="KUBERNETES"></a>`KUBERNETES`: Set this to `true` if you are
  running inside [Kubernetes].  This tells **docassemble** that it can
  use the IP address of the Pod in place of the hostname.  See the
  [`kubernetes`] configuration directive.
* <a name="USEHTTPS"></a>`USEHTTPS`: Set this to `true` if you would
  like **docassemble** to communicate with the browser using
  encryption.  Read the [HTTPS] section for more information.
  Defaults to `false`.  See the [`use https`] configuration
  directive.  Do not set this to `true` if you are using a load
  balancer that forwards non-encrypted [HTTP] to your server.
* <a name="DAHOSTNAME"></a>`DAHOSTNAME`: Set this to the hostname by
  which web browsers can find **docassemble**.  This is necessary for
  [HTTPS] to function. See the [`external hostname`] configuration
  directive.
* <a name="USELETSENCRYPT"></a>`USELETSENCRYPT`: Set this to `true` if
  you are [using Let's Encrypt].  The default is `false`.  See the
  [`use lets encrypt`] configuration directive.
* <a name="LETSENCRYPTEMAIL"></a>`LETSENCRYPTEMAIL`: Set this to the
  e-mail address you use with [Let's Encrypt].  See the
  [`lets encrypt email`] configuration directive.
* <a name="LOGSERVER"></a>`LOGSERVER`: This is used in the
  [multi-server arrangement] where there is a separate server for
  collecting log messages.  The default is `none`, which causes the
  server to run [Syslog-ng].  See the [`log server`] configuration
  directive.
* <a name="REDIS"></a>`REDIS`: If you are running **docassemble** in a
  [multi-server arrangement], set this to `redis://thehostname` where
  `thehostname` is the host name at which the [Redis] server can be
  accessed.  See the [`redis`] configuration directive.
* <a name="RABBITMQ"></a>`RABBITMQ`: If you are running
  **docassemble** in a [multi-server arrangement], set this to the URL
  at which the [RabbitMQ] server can be accessed, in the form
  `pyamqp://guest@rabbitmqserver.local//` or
  `pyamqp://user:xxsecretpasswdxx@rabbitmqserver.local//`.  Note that
  [RabbitMQ] is very particular about hostnames.  If the [RabbitMQ]
  server is running on a machine on which the command `hostname -s`
  evaluates to `rabbitmqserver.local`, then your application servers
  will need to use `rabbitmqserver.local` as the hostname in the
  `RABBITMQ` URL, even if other names resolve to the same IP address.
  Note that if you run **docassemble** using the instructions in the
  [scalability] section, you may not need to worry about setting
  `RABBITMQ`.  See the [`rabbitmq`] configuration directive.
* <a name="SERVERADMIN"></a>`SERVERADMIN`: If your **docassemble** web
  server generates an error, the error message will contain an e-mail
  address that the user can contact for help.  This e-mail address
  defaults to `webmaster@localhost`.  You can set this e-mail address
  by setting the `SERVERADMIN` environment variable to the e-mail
  address you want to use.  See the [`server administrator email`]
  configuration directive.
* <a name="POSTURLROOT"></a>`POSTURLROOT`: If users access
  **docassemble** at https://docassemble.example.com/da, set `POSTURLROOT`
  to `/da/`.  The trailing slash is important.  If users access
  **docassemble** at https://docassemble.example.com, you can ignore
  this.  The default value is `/`.  See the [`root`] configuration
  directive.
* <a name="BEHINDHTTPSLOADBALANCER"></a>`BEHINDHTTPSLOADBALANCER`: Set
  this to `true` if a load balancer is in use and the load balancer
  accepts connections in HTTPS but forwards them to web servers as
  HTTP.  This lets **docassemble** know that when it forms URLs, it
  should use the `https` scheme even though requests appear to be
  coming in as HTTP requests.  See the [`behind https load balancer`]
  configuration directive.
* <a name="XSENDFILE"></a>`XSENDFILE`: Set this to `false` if the
  X-Sendfile header is not functional in your configuration for
  whatever reason.  See the [`xsendfile`] configuration directive.
* <a name="DAALLOWUPDATES"></a>`DAALLOWUPDATES`: Set this to `false`
  if you want to disable the updating of software through the user
  interface.  See the [`allow updates`] configuration directive.
* <a name="DAUPDATEONSTART"></a>`DAUPDATEONSTART`: Set this to `false`
  if you do not want the container to update its software using `pip`
  when it starts up.  Set `DAUPDATEONSTART` to `initial` if you want
  the container to update its software during the first `docker run`,
  but not on every `docker start`.  See the [`update on start`]
  configuration directive.
* <a name="TIMEZONE"></a>`TIMEZONE`: You can use this to set the time
  zone of the server.  The value of the variable is stored in
  `/etc/timezone` and `dpkg-reconfigure -f noninteractive tzdata` is
  run in order to set the system time zone.  The default is
  `America/New_York`.  See the [`timezone`] configuration directive.
* <a name="LOCALE"></a>`LOCALE`: You can use this to enable a locale
  on the server.  When the server starts, the value of `LOCALE` is
  appended to `/etc/locale.gen` and `locale-gen` and `update-locale`
  are run.  The default is `en_US.UTF-8 UTF-8`.  See the [`os locale`]
  configuration directive.
* <a name="OTHERLOCALES"></a>`OTHERLOCALES`: You can use this to set
  up other locales on the system besides the default locale.  Set this
  to a comma separated list of locales.  The values need to match
  entries in [Debian]'s `/etc/locale.gen`.  See the
  [`other os locales`] configuration directive.
* <a name="PACKAGES"></a>`PACKAGES`: If your interviews use code that
  depends on certain [Debian] packages being installed, you can
  provide a comma-separated list of [Debian] packages in the
  `PACKAGES` environment variable.  The packages will be installed
  when the container is started.  See the [`debian packages`]
  configuration directive.
* <a name="PYTHONPACKAGES"></a>`PYTHONPACKAGES`: If you want to
  install certain [Python] packages during the container start process,
  you can provide a comma-separated list of packages in the
  `PYTHONPACKAGES` environment variable.  See the [`python packages`]
  configuration directive.
* <a name="DASECRETKEY"></a>`DASECRETKEY`: The secret key for protecting
  against cross-site forgery.  See the [`secretkey`] configuration
  directive.  If `DASECRETKEY` is not set, a random secret key will be
  generated.
* <a name="DABACKUPDAYS"></a>`DABACKUPDAYS`: The number of days
  backups should be kept.  The default is 14.  See the [`backup days`]
  configuration directive.
* <a name="DAEXPOSEWEBSOCKETS"></a>`DAEXPOSEWEBSOCKETS`: You may need
  to set this to `true` if you are operating a [Docker] container
  [behind a reverse proxy] and you want to use the [WebSocket]-based
  [live help] features.  See the [`expose websockets`] configuration
  directive.
* <a name="DAWEBSOCKETSIP"></a>`DAWEBSOCKETSIP`: You can set this if
  you need to manually specify the address on which the `websockets`
  service runs.  See the [`websockets ip`] configuration directive.
* <a name="DAWEBSOCKETSPORT"></a>`DAWEBSOCKETSPORT`: You can set this
  if you need to manually specify the port on which the `websockets`
  service runs.  See the [`websockets port`] configuration directive.
* <a name="USEMINIO"></a>`USEMINIO`: Set this to `true` if you are
  setting [`S3ENDPOINTURL`] to point to [MinIO] and you would like the
  bucket to be created when the container starts.  See the [`use
  minio`] configuration directive.
* <a name="USECLOUDURLS"></a>`USECLOUDURLS`: Set this to `false` if
  you are using cloud storage but you do not want URLs for files to
  point directly to the cloud storage provider.  See the [`use cloud
  urls`] configuration directive.
* <a name="DASTABLEVERSION"></a>`DASTABLEVERSION`: Set this to `true`
  if you want **docassemble** to stay on version 1.0.x.  This is the
  `stable` branch of the [GitHub] repository, which only receives bug
  fixes and security updates.  See the [`stable version`]
  configuration directive.
* <a name="DASSLPROTOCOLS"></a>`DASSLPROTOCOLS`: This indicates the
  SSL protocols that [NGINX] should accept.  The default is `TLSv1.2`.
  You might want to set it to `TLSv1 TLSv1.1 TLSv1.2` if you need to
  support older browsers.  The value is passed directly to the [NGINX]
  directive [`ssl_protocols`].  See the [`nginx ssl protocols`]
  configuration directive.
* <a
  name="ENVIRONMENT_TAKES_PRECEDENCE"></a>`ENVIRONMENT_TAKES_PRECEDENCE`:
  It was noted above that once the [configuration] file is located in
  the [persistent volume], [S3](#persistent s3), or [Azure blob
  storage](#persistent azure), the values in that [configuration] file
  will take precedence over any values specified in [Docker]
  environment variables.  This is the default behavior; the [Docker]
  environment variables are useful for 1) telling the server where to
  find an existing [configuration] file; and 2) if no [configuration]
  file exists already, pre-populating the initial [configuration]
  file.  However, if you set `ENVIRONMENT_TAKES_PRECEDENCE` to `true`,
  then **docassemble** will override values in the [configuration]
  file with the values of [Docker] environment variables if they
  conflict.  Note that the [YAML] of the [configuration] file will not
  be altered; you will still see the same [YAML] when you go to edit
  the [Configuration].  However, internally, **docassemble** will
  override those values with the values of the [Docker] environment
  variables.  Since it can be confusing to have dueling sources of
  configuration values, it is encouraged that you update the [YAML] of
  your [Configuration] to align with the values in your [Docker]
  environment.  The `ENVIRONMENT_TAKES_PRECEDENCE` option is primarily
  used in the [Kubernetes]/[Helm] environment, where there are some
  [Docker] environment variables that cannot be known in advance.

## <a name="configuration"></a>Changing the configuration

If you already have an existing **docassemble** installation and you
want to `run` a new [Docker] container using it, but you want to
change the [configuration] of the container, there are some things you
will need to keep in mind.

The existing [configuration] file takes precedence over the
environment variables that you set using [Docker].

If you want to change the [configuration], and the server is running,
you can edit the [configuration] using the web interface.

If the server is not running, and you are using [persistent volumes],
you can use [`docker volume inspect`] to find the location of the
persistent volume.

When **docassemble** starts up on a [Docker] container, it:

* Creates a [configuration] file from a template, using environment
  variables for initial values, if a [configuration] file does not
  already exist.
* Initializes a [PostgreSQL] database, if one is not already initialized.
* Configures the [NGINX] configuration, if one is not already
  configured.
* Runs [Let's Encrypt] if the configuration indicates that
  [Let's Encrypt] should be used, and [Let's Encrypt] has not yet been
  configured.

When **docassemble** stops, it saves the [configuration] file, a
backup of the [PostgreSQL] database, and backups of the [Let's Encrypt]
configuration.  If you are using [persistent volumes], the information
will be stored there.  If you are using [S3](#persistent s3) or [Azure
blob storage](#persistent azure), the information will be stored in
the cloud.

When **docassemble** starts again, it will retrieve the
[configuration] file, the backup of the [PostgreSQL] database, and
backups of the [Let's Encrypt] configuration from storage and use them
for the container.

Suppose you have an existing installation that uses HTTPS and [Let's
Encrypt], but you want to change the [`DAHOSTNAME`].  You will need to
delete the saved configuration before running a new container.  First,
shut down the machine with [`docker stop -t 600`].  Then, if you are using
[S3](#persistent s3), you can go to the [S3 Console] and delete the
"letsencrypt.tar.gz" file.  If you are using [Azure blob
storage](#persistent azure), you can go to the [Azure Portal] and
delete the "letsencrypt.tar.gz" file.

Also, if a configuration file exists on [S3](#persistent
s3)/[Azure blob storage](#persistent azure) (`config.yml`) or in a
[persistent volume], then the values in that configuration will take
precedence over the corresponding environment variables that are
passed to [Docker].  Once a configuration file exists, you should make
changes to the configuration file rather than passing environment
variables to [Docker].  However, if your configuration is on
[S3](#persistent s3)/[Azure blob storage](#persistent azure), you will
at least need to pass sufficient access keys (e.g., [`S3BUCKET`],
[`AZURECONTAINER`], etc.) to access that storage; otherwise your
container will not know where to find the configuration.

Also, there are some environment variables that do not exist in the
configuration file because they are specific to the individual server
being started.  These include the [`CONTAINERROLE`] and
[`SERVERHOSTNAME`] environment variables.

# <a name="data storage"></a>Data storage

[Docker] containers are volatile.  They are designed to be run, turned
off, and destroyed.  When using [Docker], the best way to upgrade
**docassemble** to a new version is to destroy and rebuild your
containers.

But what about your data?  If you run **docassemble**, you are
accumulating valuable data in SQL, in files, and in [Redis].  If your
data are stored on the [Docker] container, they will be destroyed by
[`docker rm`].

There are two ways around this problem.  The first, and most
preferable solution, is to use an object storage service.  The
standard-setting object storage service is [Amazon Web Services]'s
[S3].  If you use [AWS], you can create an [S3 bucket] for your data,
and then when you launch your **docassemble** container, set the
[`S3BUCKET`], [`S3ACCESSKEY`], [`S3SECRETACCESSKEY`], and [`S3REGION`]
environment variables.

If you don't want to use [Amazon Web Services], you can use an
[S3]-compatible object storage service by setting [`S3ENDPOINTURL`] to
the URL of the service, along with the [`S3BUCKET`], [`S3ACCESSKEY`],
and [`S3SECRETACCESSKEY`]<span></span> [environment variables].  There
are [S3]-compatible object storage services available for [Google
Cloud], [Wasabi], [Linode], [Vultr], [Digital Ocean], [IBM Cloud],
[Oracle Cloud], [Scaleway], [Exoscale], and others.  If you are
operating an on-premises server, you can deploy [MinIO] ([MinIO] is
configured by default if you deploy **docassemble** [with Kubernetes])
or [Rook].

In addition to [S3] and [S3]-compatible object storage,
**docassemble** supports [Azure blob storage].  You can create a [blob
storage container] inside [Microsoft Azure] and then when you launch
your container, you set the [`AZUREACCOUNTNAME`], [`AZUREACCOUNTKEY`],
and [`AZURECONTAINER`]<span></span> [environment variables].

When [`docker stop -t 600`] is run, **docassemble** will backup the SQL
database, the [Redis] database, the [configuration], and your uploaded
files to the [S3 bucket] or [blob storage container].  Then, when you
issue a [`docker run`] command with [environment variables] pointing
**docassemble** to your [S3 bucket]/[Azure blob storage] resource,
**docassemble** will make restore from the backup.  You can [`docker
rm`] your container and your data will persist in the cloud.

The second method of persistent storage is to use [persistent
volumes], which is a feature of [Docker].  This will store the data in
directories on the [Docker] host, so that when you destroy the
container, these directories will be untouched, and when you start up
a new container, it will use the saved directories.

These two options are explained in the following subsections.

## <a name="persistent s3"></a>Using S3 or S3-compatible

To use [S3] (or an [S3]-compatible] service) for persistent storage,
you need to obtain credentials and create a bucket.

If you want to use [Amazon Web Services], you would first sign up for
an [AWS] account, and go to the [S3 Console], click "Create Bucket,"
and pick a name.  If your site is at docassemble.example.com, a good
name for the bucket is `docassemble-example-com`.  (Client software
will have trouble accessing your bucket if it contains `.`
characters.)  Under "Region," pick the region nearest you.
Then you need to obtain an access key and a secret access key for
[S3].  To obtain these credentials, go to [IAM Console] and create a
user with "programmatic access."  Under "Attach existing policies
directly," find the policy called `AmazonS3FullAccess` and attach it
to the user.

When you run your **docassemble** [Docker] container, set the
[configuration options]<span></span> [`S3BUCKET`], [`S3ACCESSKEY`],
[`S3SECRETACCESSKEY`], and [`S3REGION`].  For example, you might use
an `env.list` file such as:

{% highlight text %}
DAHOSTNAME=interviews.example.com
S3ENABLE=true
S3BUCKET=interviews-example-com
S3ACCESSKEY=YERWERGDFSGERGSDFGSW
S3SECRETACCESSKEY=WERWR36dddeg3udjfRT1+rweRTHRTookiMVASDAS
S3REGION=us-east-2
TIMEZONE=America/New_York
USEHTTPS=true
EC2=true
USELETSENCRYPT=true
LETSENCRYPTEMAIL=dev@example.com
{% endhighlight %}

Note that if you run **docassemble** on [EC2], you can launch your
[EC2] instances with an [IAM] role that allows **docassemble** to
access to an [S3] bucket without the necessity of setting
[`S3ACCESSKEY`] and [`S3SECRETACCESSKEY`].  In this case, the only
environment variable you need to pass is [`S3BUCKET`].

If you are using an [S3]-compatible object storage service, you will
need to set [`S3ENDPOINTURL`] to the URL endpoint of your service,
which you can find in the service's documentation or in your account
settings.  You likely will not need to set [`S3REGION`] unless the
service supports the "region" concept.

These secret access keys will become available to all developers who
use your **docassemble** server, since they are in the configuration
file.

If you are using [AWS] and you want to limit access to a particular
bucket, you do not have to use the `AmazonS3FullAccess` policy when
obtaining [S3] credentials.  Instead, you can create your own policy
with the following definition:

{% highlight json %}
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::docassemble-example-com"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:GetObject",
                "s3:DeleteObject"
            ],
            "Resource": [
                "arn:aws:s3:::docassemble-example-com/*"
            ]
        }
    ]
}
{% endhighlight %}

Replace `docassemble-example-com` in the above text with the name of
your [S3] bucket.

## <a name="persistent azure"></a>Using Microsoft Azure

Using [Microsoft Azure] is very similar to using [S3].  From the
[Azure Portal] dashboard, search for "Storage accounts" in the
"Resources."  Click "Add" to create a new storage account.  Under
"Account kind," choose "BlobStorage."  Under "Access tier," you can
choose either "Cool" or "Hot," but you may have to pay more for "Hot."

Once the storage account is created, go into your "Blobs" service in
the storage account and click "+ Container" to add a new container.
Set the "Access type" to "Private."  The name of the container
corresponds with the [`AZURECONTAINER`] environment variable.  Back at
the storage account, click "Access keys."  The "Storage account name"
corresponds with the environment variable [`AZUREACCOUNTNAME`].  The
"key1" corresponds with the [`AZUREACCOUNTKEY`] environment variable.
(You can also use "key2.").  For example, you might use an `env.list`
file such as:

{% highlight text %}
DAHOSTNAME=interviews.example.com
AZUREENABLE=true
AZUREACCOUNTNAME=exampledotcom
AZUREACCOUNTKEY=98f89asdfjwew/YosdfojweafASDFErgergDFGsergagaweWRTIQgqERGs243rergE4534tERgEFBDRGferEEB==
AZURECONTAINER=interviews-example-com
TIMEZONE=America/New_York
USEHTTPS=true
USELETSENCRYPT=true
LETSENCRYPTEMAIL=dev@example.com
{% endhighlight %}

If you enable both [S3](#persistent s3) and
[Azure blob storage](#persistent azure), only [S3] will be used.

## <a name="persistent"></a>Using persistent volumes

To run **docassemble** in a [single-server arrangement] in such a way
that the [configuration], the [Playground] files, the uploaded files,
and other data persist after the [Docker] container is removed or
updated, run the image as follows:

{% highlight bash %}
docker run --env-file=env.list \
-v dabackup:/usr/share/docassemble/backup \
-d -p 80:80 -p 443:443 --stop-timeout 600 \
jhpyle/docassemble
{% endhighlight %}

where `--env-file=env.list` is an optional parameter that refers to a
file `env.list` containing environment variables for the
configuration.  A [template for the `env.list` file] is included in
distribution.

An advantage of using persistent volumes is that you can completely
replace the **docassemble** container and rebuild it from scratch, and
when you `run` the `jhpyle/docassemble` image again, docassemble will
keep running where it left off.

If you are using [HTTPS] with your own certificates (as opposed to
using [Let's Encrypt]), you can use a persistent volume to provide the
certificates to the [Docker] container.  Just add `-v
dacerts:/usr/share/docassemble/certs` to your [`docker run`] command.

To see what volumes exist on your [Docker] system, you can run:

{% highlight bash %}
docker volume ls
{% endhighlight %}

[Docker] volumes are actual directories on the file system.  To find
the path of a given volume, use [`docker volume inspect`]:

{% highlight bash %}
docker volume inspect dabackup
{% endhighlight %}

For example, if you are using [HTTPS] with your own certificates, and
you need to update the certificates your server should use, you can
find the path where the `dacerts` volume lives (`docker volume inspect
dacerts`), copy your certificates to that path (`cp mycertificate.key
/var/lib/docker/volumes/dacerts/data/docassemble.key`), and then stop
the container (`docker stop -t 600 <containerid>`) and start it again
(`docker start <containerid>`).

To delete all of the volumes, do:

{% highlight bash %}
docker volume rm $(docker volume ls -qf dangling=true)
{% endhighlight %}

Ultimately, the better [data storage] solution is to use cloud storage
([S3](#persistent s3), [Azure blob storage](#persistent azure)) because:

1. [S3](#persistent s3) and [Azure blob storage](#persistent azure)
   make scaling easier.  They are the "cloud" way of storing
   persistent data, at least until cloud-based network file systems
   become more robust.
2. It is easier to upgrade your virtual machines to the latest
   software and operating system if you can just destroy them and
   recreate them, rather than running update scripts.  If your
   persistent data is stored in the cloud, you can destroy and
   recreate virtual machines at will, without ever having to worry
   about copying your data on and off the machines.

However, you can get around the second problem by using [`docker
volume create`] to put your Docker volume on a separate drive.  That
way, you could remove the virtual machine that runs the application,
along with its primary drive, without affecting the drive with the
**docassemble** data.

## <a name="recovery"></a>Recovery from backup files

When you are using [data storage], you can do `docker stop -t 600` on
a container, followed by `docker rm`, and then re-run your original
`docker run` command, and when the system starts again, it will be in
the same place it was before, with the same uploaded files, the same
SQL database.

During `docker stop -t 600`, files are saved in the [data storage]
area.  During `docker run` (and `docker start` as well), files are
restored from the [data storage] area.

However, if your server has an unsafe shutdown, the files in the [data
storage] area might be corrupted.  They might also be missing or very
old (perhaps dating from the last time there was a safe shutdown).
If this happens, not all is lost, because you can restore from a backup.

If you are using [S3](#persistent s3) or [Azure blob
storage](#persistent azure), the files and directories that are saved
during the shutdown process are:

* `postgres` - a folder containing a "dump" of each database hosted by
  the [PostgreSQL] server.  Usually the operative file is called
  `docassemble`, for the database called `docassemble`.  If you point
  your server to an external database using the [`db` section] of your
  [Configuration], this is not applicable.  The backup file will
  exist, but it will be an empty database.
* `redis.rdb` - a file containing a backup of the [Redis] database.
  If you point your server to an external [Redis] database using a
  [`redis`] directive in your [Configuration], this is not
  applicable.  The `redis.rdb` file will exist, but it will be an
  empty database.
* `log` - a folder containing **docassemble** log files.
* `nginxlogs` - a folder containing the logs for [NGINX].  If you are
  using Apache, the relevant folder is `apachelogs`.  This is not
  applicable unless the `CONTAINERROLE` is `all`.

The `files` folder, the `config.yml` file, and the
`letsencrypt.tar.gz` (if Let's Encrypt is used) are important for
restoring the system on startup, but they are always up-to-date; they
are not copied from the server during the shutdown process.

If you are using [persistent volumes], the files and directories that
are saved during the shutdown process are:

* `/usr/share/docassemble/backup/postgres` - a folder containing a
  "dump" of each database hosted by the [PostgreSQL] server.  Usually
  the operative file is called `docassemble`, for the database called
  `docassemble`.  If you point your server to an external database
  using the [`db` section] of your [Configuration], this is not
  applicable.  The backup file will exist, but it will be an empty
  database.
* `/usr/share/docassemble/backup/redis.rdb` - a file containing a
  backup of the [Redis] database.  If you point your server to an
  external [Redis] database using a [`redis`] directive in your
  [Configuration], this is not applicable.  The `redis.rdb` file will
  exist, but it will be an empty database.
* `/usr/share/docassemble/backup/files` - a directory containing all
  of the stored files in your system (document uploads, assembled
  documents, ZIP files for installed packages, etc.).
* `/usr/share/docassemble/backup/log` - a folder containing
  **docassemble** log files.
* `/usr/share/docassemble/backup/nginxlogs` - a folder containing the
  logs for [NGINX].  If you are using Apache, the relevant folder is
  `/usr/share/docassemble/backup/apachelogs`.  This is not applicable
  unless the `CONTAINERROLE` is `all`.
* `/usr/share/docassemble/backup/config/config.yml` - a file
  containing the Configuration of your system.

The file `/usr/share/docassemble/backup/letsencrypt.tar` is important
for restoring the system (if Let's Encrypt is used), but it is always
up-to-date; it is not copied from the server during the shutdown
process.

Whenever a **docassemble** container starts up, the [PostgreSQL]
database in `postgres/docassemble` is used to restore
**docassemble**'s SQL database.  The `redis.rdb` file is used to
restore the Redis database.  These files are created during the
shutdown process.  It is important that the shutdown process happens
gracefully, because otherwise these files will not be complete.

As protection against the risk of an unsafe shutdown (as well as the
risk of the accidental deletion of data), **docassemble** maintains a
daily rotating backup.  The daily backup is created whenever the daily
cron job runs (which is typically around 6:00 in the morning).

If you are using [S3](#persistent s3) or [Azure blob
storage](#persistent azure), these backups are in the `backup` folder
in the cloud storage.  There is a subfolder in the `backup` folder for
each container that has used the cloud storage area.  The subfolder
names come from the internal hostnames of containers.  In a
[multi-server arrangement], you will see several subfolders.  You may
also see several subfolders if you have called `docker run` multiple
times.  Within a subfolder for a container, there are subfolders for
each day for which there is a backup.  The folders are in the format
`MM-DD` where `MM` is the month and `DD` is the day of the month.  If
you want to restore your system to a snapshot of where it was when a
daily backup was made, you will need to shut down your server(s) with
`docker stop -t 600` if it is still running.  Then you will need to
copy files from the daily backup location to the places where they
will be used when the system starts up again.  In particular, you will
copy the following out of the daily backup folder:

* `config/config.yml to `config.yml` in the root of the cloud storage.
* `files` to `files` in the root of the cloud storage.
* `postgres` to `postgres` in the root of the cloud storage.
* `redis.rdb` to `redis.rdb` in the root of the cloud storage.
* `log` to `log` in the root of the cloud storage.

Copying `log` is optional.  The contents of log files are not critical
to the functionality of the systems.

If you are using persistent volumes, the disaster recovery backup
files are in folders named `/usr/share/docassemble/backup/MM-DD` where
`MM` is the month and `DD` is the day the backup was made.  If you
want to restore your system to a snapshot of where it was when a daily
backup was made, you will first need to shut down your server with
`docker stop -t 600` if it is still running.  Then you will need to
copy files from the daily backup location to the places where they
will be used when the system starts up again.  In particular, you will
copy the following out of the daily backup folder:

* `config/config.yml` to `/usr/share/docassemble/backup/config.yml`
* `files` to `/usr/share/docassemble/backup/files`
* `postgres` `/usr/share/docassemble/backup/postgres`
* `redis.rdb` to `/usr/share/docassemble/backup/redis.rdb`
* `log` to `/usr/share/docassemble/backup/log`

Copying `log` is optional.  The contents of log files are not critical
to the functionality of the systems.

After copying these files into place, you can start your server(s)
with `docker run` (using the same parameters you originally used) or
`docker start`.

# <a name="multi server arrangement"></a>Multi-server arrangement

## Services on different machines

The **docassemble** application consists of several services, some of
which are singular and some of which can be plural.

The singular services include:

* SQL
* [Redis]
* [RabbitMQ] for coordinating [background processes]
* The **docassemble** log message aggregator
* A [cron] service that runs [scheduled tasks] and housekeeping functions

The (potentially) plural services include:

* Web servers
* [Celery] nodes

The **docassemble** [Docker] container will run any subset of these
six services, depending on the value of the environment variable
[`CONTAINERROLE`], which is passed to the container at startup.  In a
single-server arrangement ([`CONTAINERROLE`] = `all`, or left
undefined), the container runs all of the services (except the log
message aggregator, which is not necessary in the case of a
single-server arrangement).

You can run **docassemble** in a [multi-server arrangement] using
[Docker] by running the **docassemble** image on different hosts using
different [configuration options].

In a multi-server arrangement, you can have one machine run SQL,
another machine run [Redis] and [RabbitMQ], and any number of machines
run web servers and [Celery] nodes.  You can decide how to allocate
services to different machines.  For example, you might want to run
central tasks on a powerful server, while running many web servers on
less powerful machines.

Since the SQL, [Redis], and [RabbitMQ] services are standard services,
they do not have to be run from **docassemble** [Docker] containers.
For example, if you are already running a SQL server, a [Redis]
server, and a [RabbitMQ] server, you could just point **docassemble**
to those resources.

To change the SQL server that **docassemble** uses, edit the
[`DBHOST`], [`DBNAME`], [`DBUSER`], [`DBPASSWORD`], [`DBPREFIX`],
[`DBPORT`], and [`DBTABLEPREFIX`]<span></span> [configuration options].

To change the [Redis] server that **docassemble** uses, edit the
[`REDIS`]<span></span> [configuration option].

To change the [RabbitMQ] server that **docassemble** uses, edit the
[`RABBITMQ`]<span></span> [configuration option].

## <a name="ports"></a>Port opening

Note that for every service that a [Docker] container provides,
appropriate ports need to be forwarded from the [Docker] host machine
to the container.

* Regardless of the [`CONTAINERROLE`], port 9001 needs to be forwarded
  so that the container can be controlled via [supervisor].
* If [`CONTAINERROLE`] includes `sql`: forward port 5432 ([PostgreSQL])
* If [`CONTAINERROLE`] includes `web`: forward ports 80 (HTTP) and 443 (HTTPS)
* If [`CONTAINERROLE`] includes `log`: forward ports 514 ([Syslog-ng])
  and 8080 (custom web server)
* If [`CONTAINERROLE`] includes `redis`: forward port 6379 ([Redis])
* If [`CONTAINERROLE`] includes `rabbitmq`: forward ports 4369, 5671,
  5672, and 25672 ([RabbitMQ]).
* If [`CONTAINERROLE`] includes `mail`: forward port 25 ([e-mails]).

For example:

{% highlight bash %}
docker run \
--env CONTAINERROLE=sql:redis \
...
-d -p 5432:5432 -p 6379:6379 -p 9001:9001 \
--stop-timeout 600 jhpyle/docassemble
{% endhighlight %}

{% highlight bash %}
docker run \
--env CONTAINERROLE=web:celery \
...
-d -p 80:80 -p 443:443 -p 9001:9001 \
--stop-timeout 600 jhpyle/docassemble
{% endhighlight %}

Note that [Docker] will fail if any of these ports is already in use.
For example, many Linux distributions run a mail transport agent on
port 25 by default; you will have to stop that service in order to
start [Docker] with `-p 25:25`.  For example, on [Amazon Linux] you
may need to run:

{% highlight bash %}
sudo /etc/init.d/sendmail stop
{% endhighlight %}

## <a name="file sharing"></a>File sharing

If you run multiple **docassemble** [Docker] containers on different
machines, the containers will need to have a way to share files with
one another.

One way to share files among containers is to make
`/usr/share/docassemble/` a [persistent volume] on a network file
system.  This directory contains the configuration, SSL certificates,
[Python virtual environment], and uploaded files.  However, network
file systems present problems.

A preferable way to share files is with [Amazon S3] or
[Azure blob storage], which **docassemble** supports.  See the
[using S3] and [using Azure blob storage] sections for instructions on
setting this up.

## <a name="config file"></a>Configuration file

Note that when you use the cloud ([S3](#persistent s3) or
[Azure blob storage](#persistent azure)) for [data storage],
**docassemble** will copy the `config.yml` file out of the cloud on
startup, and save `config.yml` to the cloud whenever the configuration
is modified.

This means that as long as there is a `config.yml` file in the cloud
with the configuration you want, you can start **docassemble**
containers without specifying a lot of [configuration options]; you
simply have to refer to your cloud storage bucket/container, and
**docassemble** will take it from there.  For example, to run a
central server, you can do:

{% highlight bash %}
docker run \
--env CONTAINERROLE=sql:redis:rabbitmq:log:cron:mail \
--env S3BUCKET=docassemble-example-com \
--env S3ACCESSKEY=FWIEJFIJIDGISEJFWOEF \
--env S3SECRETACCESSKEY=RGERG34eeeg3agwetTR0+wewWAWEFererNRERERG \
-d -p 80:8080 -p 25:25 -p 5432:5432 -p 514:514 \
-p 6379:6379 -p 4369:4369 -p 5671:5671 \
-p 5672:5672 -p 25672:25672 -p 9001:9001 \
--stop-timeout 600 jhpyle/docassemble
{% endhighlight %}

To run an application server, you can do:

{% highlight bash %}
docker run \
--env CONTAINERROLE=web:celery \
--env S3BUCKET=docassemble-example-com \
--env S3ACCESSKEY=FWIEJFIJIDGISEJFWOEF \
--env S3SECRETACCESSKEY=RGERG34eeeg3agwetTR0+wewWAWEFererNRERERG \
-d -p 80:80 -p 443:443 -p 9001:9001 \
--stop-timeout 600 jhpyle/docassemble
{% endhighlight %}

# <a name="Encryption"></a>Encrypting communications

## <a name="https"></a>Using HTTPS

If you are running **docassemble** on [EC2], the easiest way to enable
HTTPS support is to set up an [Application Load Balancer] that accepts
connections in HTTPS format and forwards them to the web servers in
HTTP format.  In this configuration [Amazon] takes care of creating
and hosting the necessary SSL certificates.

If you are not using a [load balancer], you can use HTTPS either by
setting up [Let's Encrypt] or by providing your own certificates.

### <a name="letsencrypt"></a>With Let's Encrypt

If you are running **docassemble** in a [single-server arrangement],
or in a [multi-server arrangement] with only one web server, you can
use [Let's Encrypt] to enable [HTTPS].  If you have more than one web
server, you can enable encryption [without Let's Encrypt] by
installing your own certificates.

To use [Let's Encrypt], set the following environment variables in
your task definition or `env.list` file:

* `USELETSENCRYPT`: set this to `true`.
* `LETSENCRYPTEMAIL`: [Let's Encrypt] requires an e-mail address, which
  it will use to get in touch with you about renewing the SSL certificates.
* `DAHOSTNAME`: set this to the hostname that users will use to get to
  the web application.  [Let's Encrypt] needs this in order to verify
  that you have access to the host.
* `USEHTTPS`: set this to `true`.

For example, your `env.list` may look like:

{% highlight text %}
CONTAINERROLE=all
USEHTTPS=true
DAHOSTNAME=docassemble.example.com
USELETSENCRYPT=true
LETSENCRYPTEMAIL=admin@example.com
TIMEZONE=America/New_York
{% endhighlight %}

The first time the server is started, the `letsencrypt` utility will
be run, which will change the [NGINX] configuration in order to use the
appropriate SSL certificates.  When the server is later restarted,
the `letsencrypt renew` command will be run, which will refresh the
certificates if they are within 30 days of expiring.

In addition, a [script] will run on a weekly basis to attempt to renew
the certificates.

If you are using a [multi-server arrangement] with a single web
server, you need to run the `cron` role on the same server that runs
the `web` role.  If you use the [e-mail receiving] feature with
[TLS encryption], the `mail` role also has to share the server with
the `web` and `cron` roles.

### <a name="without letsencrypt"></a>Without Let's Encrypt

Using your own SSL certificates with [Docker] requires that your SSL
certificates reside within each container.  There are several ways to
accomplish this:

* Use [S3](#persistent s3) or [Azure blob storage](#persistent azure)
  and upload the certificates to your bucket/container.
* [Build your own private image] in which your SSL certificates are
  placed in `Docker/ssl/nginx.key`, `Docker/ssl/nginx.crt`, and
  `Docker/ssl/nginx.ca.pem`.  During the build process, these files
  will be copied into `/usr/share/docassemble/certs`.
* Use [persistent volumes] and copy the SSL certificate files
  (`nginx.key`, `nginx.crt`, and `nginx.ca.pem`)
  into the volume for `/usr/share/docassemble/certs` before starting
  the container.

The default [NGINX] configuration file expects SSL certificates to be
located in the following files:

{% highlight text %}
ssl_certificate /etc/ssl/docassemble/nginx.crt;
ssl_certificate_key /etc/ssl/docassemble/nginx.key;
{% endhighlight %}

The meaning of these files is as follows:

* `nginx.crt`: this file is generated by your certificate
  authority when you submit a certificate signing request.
* `nginx.key`: this file is generated at the time you create
  your certificate signing request.

In order to make sure that these files are replicated on every web
server, the [supervisor] will run the
`docassemble.webapp.install_certs` module before starting the web
server.

If you are using [S3](#persistent s3) or [Azure blob storage](#persistent azure),
this module will copy the files from the `certs/` prefix in your
bucket/container to `/etc/ssl/docassemble`.  You can use the [S3
Console] or the [Azure Portal] to create a folder called `certs` and
upload your certificate files into that folder.

If you are not using [S3](#persistent s3) or [Azure blob storage](#persistent azure), the
`docassemble.webapp.install_certs` module will copy the files from
`/usr/share/docassemble/certs` to `/etc/ssl/docassemble`.

There are two ways that you can put your own certificate files into
the `/usr/share/docassemble/certs` directory.  The first way is to
[create your own Docker image] of **docassemble** and put your
certificates into the `Docker/ssl` directory.  The contents of this
directory are copied into `/usr/share/docassemble/certs` during the
build process.

The second way is to use [persistent volumes].  If you have a
persistent volume called `certs` for the directory
`/usr/share/docassemble/certs`, then you can run `docker volume
inspect certs` to get the directory on the [Docker] host corresponding
to this directory, and you can copy the SSL certificate files into
that directory before starting the container.

Note that the files need to be called `nginx.crt` and `nginx.key`,
because this is what the standard web server configuration expects.

If you want to use different filesystem or cloud locations, the
`docassemble.webapp.install_certs` can be configured to use different
locations.  See the [configuration] variables [`certs`] and
[`cert install directory`].

## <a name="tls"></a>Using TLS for incoming e-mail

If you use the [e-mail receiving] feature, you can use [TLS] to
encrypt incoming e-mail communications.  By default, **docassemble**
will install self-signed certificates into the [Exim] configuration,
but for best results you should use certificates that match your
[`incoming mail domain`].

If you are using [Let's Encrypt] to obtain your [HTTPS] certificates
in a [single-server arrangement], then **docassemble** will use your
[Let's Encrypt] certificates for [Exim].

However, if you are running your `mail` server as part of a dedicated
backend server that does not include `web`, you will need to create
and install your own certificates.  In addition, if your
[`incoming mail domain`] is different from your [`external hostname`]
([`DAHOSTNAME`]), then you will also need to install your own
certificates.

The process of installing your own [Exim] certificates is very similar to
the process of installing [HTTPS] certificates.

If you are using [S3](#persistent s3) or
[Azure blob storage](#persistent azure), copy your certificate and
private key to the `certs` folder of your [S3] bucket or
[Azure blob storage] container, using the filenames `exim.crt` and
`exim.key`, respectively.

If you are not using [S3](#persistent s3) or
[Azure blob storage](#persistent azure), save these files as:

* `/usr/share/docassemble/certs/exim.crt` (certificate)
* `/usr/share/docassemble/certs/exim.key` (private key)

On startup, `docassemble.webapp.install_certs` will copy these files
into the appropriate location (`/etc/exim4`) with the appropriate
ownership and permissions.

# <a name="build"></a>Creating your own Docker image

To create your own [Docker] image, first make sure [git] is installed:

{% highlight bash %}
sudo apt-get -y install git
{% endhighlight %}

or

{% highlight bash %}
sudo yum -y install git
{% endhighlight %}

Then download docassemble:

{% highlight bash %}
git clone {{ site.github.repository_url }}
{% endhighlight %}

To make changes to the configuration of the **docassemble**
application that will be installed in the image, edit the following
files:

* <span></span>[`docassemble/Dockerfile`]: you may want to change the
  locale and the Debian mirror; the standard "httpredir" mirror can
  lead to random packages not being downloaded, depending on which
  mirrors it chooses to use.
* <span></span>[`docassemble/Docker/config/config.yml.dist`]: you
  probably do not need to change this; it is a template that is
  updated based on the contents of the environment variables passed to
  [`docker run`].  Once your server is up and running you can change
  the rest of the configuration in the web application.
* <span></span>[`docassemble/Docker/initialize.sh`]: this script
  updates `config.yml` based on the environment variables; retrieves a
  new version of `config.yml` from [S3]/[Azure blob storage], if
  available; if [`CONTAINERROLE`] is not set to `web`, starts the
  [PostgreSQL] server and initializes the database if it does not
  exist; creates the tables in the database if they do not already
  exist; copies SSL certificates from [S3]/[Azure blob storage] or
  `/usr/share/docassemble/certs` if [S3]/[Azure blob storage] is not
  enabled; runs the [Let's Encrypt] utility if `USELETSENCRYPT` is
  `true` and the utility has not been run yet; and starts [NGINX] and
  other background tasks.
* <span></span>[`docassemble/Docker/config/nginx-http.dist`]:
  [NGINX] configuration file for handling HTTP requests.
* <span></span>[`docassemble/Docker/config/nginx-ssl.dist`]:
  [NGINX] configuration file for handling HTTPS requests.
* <span></span>[`docassemble/Docker/config/nginx-log.dist`]:
  [NGINX] configuration file for handling requests on port 8080.
  This is enabled if the [`CONTAINERROLE`] includes `log`.
* <span></span>[`docassemble/Docker/ssl/nginx.crt.orig`]: default SSL certificate for [NGINX].
* <span></span>[`docassemble/Docker/ssl/nginx.key.orig`]: default SSL certificate for [NGINX].
* <span></span>[`docassemble/Docker/ssl/exim.crt.orig`]: default SSL certificate for [Exim].
* <span></span>[`docassemble/Docker/ssl/exim.key.orig`]: default SSL certificate for [Exim].
* <span></span>[`docassemble/Docker/docassemble-supervisor.conf`]: [supervisor]
  configuration file.
* <span></span>[`docassemble/Docker/docassemble-syslog-ng.conf`]: [Syslog-ng]
  configuration file used when [`CONTAINERROLE`] does not include `log`.
* <span></span>[`docassemble/Docker/syslog-ng.conf`]: [Syslog-ng]
  configuration file used when [`CONTAINERROLE`] includes `log`.
* <span></span>[`docassemble/Docker/rabbitmq.config`]: [RabbitMQ]
  configuration file.
* <span></span>[`docassemble/Docker/docassemble.logrotate`]: This file will be copied
  into `/etc/logrotate.d` and will control the rotation of the
  **docassemble** log file in `/usr/share/docassemble/log`.
* <span></span>[`docassemble/Docker/nginx.logrotate`]: This replaces the standard
  nginx logrotate configuration.  It does not compress old log files,
  so that it is easier to view them in the web application.
* <span></span>[`docassemble/Docker/process-email.sh`]: This is a
  script that is run when an e-mail is received, if the
  [e-mail receiving] feature is configured.
* <span></span>[`docassemble/Docker/run-nginx.sh`]: This is a script
  that is run by [supervisor] to start the [NGINX] server.
* <span></span>[`docassemble/Docker/run-uwsgi.sh`]: This is a script
  that is run by [supervisor] to start the [uWSGI] server.
* <span></span>[`docassemble/Docker/run-celery.sh`]: This is a script
  that is run by [supervisor] to start the [Celery] server.
* <span></span>[`docassemble/Docker/run-cron.sh`]: This is a script
  that is run by [supervisor] to start the [cron] daemon.
* <span></span>[`docassemble/Docker/run-postgresql.sh`]: This is a script that is
  run by [supervisor] to start the [PostgreSQL] server.
* <span></span>[`docassemble/Docker/run-rabbitmq.sh`]: This is a script that is
  run by [supervisor] to start the [RabbitMQ] server.
* <span></span>[`docassemble/Docker/run-redis.sh`]: This is a script that is
  run by [supervisor] to start the [Redis] server.
* <span></span>[`docassemble/Docker/run-syslogng.sh`]: This is a script that is
  run by [supervisor] to start the [Syslog-ng] daemon.
* <span></span>[`docassemble/Docker/run-websockets.sh`]: This is a script that is
  run by [supervisor] to start the [WebSocket] server.
* <span></span>[`docassemble/Docker/reset.sh`]: This is a script that is
  run by [supervisor] to restart the web server, the [Celery] server,
  and the [WebSocket] server on a signal from a peer server.
* <span></span>[`docassemble/Docker/sync.sh`]: This is a script that is
  run by [supervisor] to synchronize log files.
* <span></span>[`docassemble/Docker/update.sh`]: This is a script that is
  run by [supervisor] to update the software on the container.

To build the image, run:

{% highlight bash %}
cd docassemble
docker build -t yourdockerhubusername/mydocassemble .
{% endhighlight %}

You can then run your image:

{% highlight bash %}
docker run -d -p 80:80 -p 443:443 --stop-timeout 600 yourdockerhubusername/mydocassemble
{% endhighlight %}

Or push it to [Docker Hub]:

{% highlight bash %}
docker push yourdockerhubusername/mydocassemble
{% endhighlight %}

# <a name="upgrading"></a>Upgrading docassemble when using Docker

New versions of the **docassemble** software are published frequently.
Most changes only affect the [Python] code.  You can upgrade the
**docassemble** [Python] packages by going to "Package Management"
from the menu and clicking the "Upgrade" button.

However, sometimes a "system upgrade" is necessary.  This can happen
when changes are made to **docassemble**'s underlying operating system
files.  When it is time for a "system upgrade," you will see a message
on the [Configuration] screen that says "A new docassemble system
version is available.  If you are using Docker, install a new Docker
image."  Performing a "system upgrade" requires retrieving a new
**docassemble** [Docker] image and running `docker run` to start a new
container.

The first time you use [`docker run`] to start a container, [Docker]
will download the image from [Docker Hub], store it on your system,
and then create a new container from that image.  However, subsequent
[`docker run`] commands will always use the version of the image that
is stored on your system, even if a new version is available on
[Docker Hub].

You can download the latest version of **docassemble** to your system
by running:

{% highlight bash %}
docker pull jhpyle/docassemble-os
docker pull jhpyle/docassemble
{% endhighlight %}

Then, subsequent [`docker run`] commands will use the latest
**docassemble** image.  This means that when you are using [Docker],
you can upgrade **docassemble** to the newest version by running
[`docker stop -t 600`] on your existing **docassemble** container,
followed by [`docker rm`], followed by `docker pull
jhpyle/docassemble-os`, followed by `docker pull jhpyle/docassemble`,
and then running whatever [`docker run`] command you use to start a
new container.

Note, however, that [`docker rm`] will delete all of the data on the
server.  This is not a problem if your [`docker run`] command
instructs **docassemble** to use a [data storage] system; in that case,
when your new container starts up, it will use the SQL server, files,
and other information that were backed up when you did [`docker stop`].

Note also that [`docker pull`] may use up a lot of disk space.  This
is because [Docker] does not automatically delete old versions of
images, and **docassemble** images are very large.  So if your disk
space is limited, you probably don't want to run [`docker pull`] until
you get rid of the old images.  (See the [next section](#cleanup).)

Thus, so long as you are using [data storage], and you aren't running
any applications other than **docassemble** using Docker, it is
recommended that you perform a system upgrade by running:

{% highlight bash %}
docker stop -t 600 $(docker ps -a -q)
docker rm $(docker ps -a -q)
docker pull jhpyle/docassemble-os
docker pull jhpyle/docassemble
docker rmi $(docker images -f "dangling=true" -q)
{% endhighlight %}

Then, run whatever `docker run` command you use to launch
**docassemble**.

# <a name="downgrading"></a>Installing an earlier version of **docassemble** when using Docker

When you do `docker run` or `docker pull`, the only image available on
[Docker Hub] is the "latest" image.  To install a version based on an
earlier version of **docassemble**, you can make your own image using
[git].

{% highlight bash %}
git clone {{ site.github.repository_url }}
cd docassemble
git checkout v0.3.21
docker build -t yourname/mydocassemble .
cd ..
docker run -d -p 80:80 -p 443:443 --stop-timeout 600 yourname/mydocassemble
{% endhighlight %}

The [`docker run`] command that you use may have other options; this
is simply an illustration of creating an image called
`yourname/mydocassemble` and then creating a container from it using
[`docker run`].

Starting with version 0.5, the **docassemble** image is split into two
parts.  The `jhpyle/docassemble` image uses `jhpyle/docassemble-os` as
a base image.  The `jhpyle/docassemble-os` image consists of the
underlying [Debian] operating system with required [Debian] packages
installed.  The `jhpyle/docassemble-os` image is updated much less
frequently than the `jhpyle/docassemble` image.  If you want to build
your own version of `jhpyle/docassemble-os`, you can do so by running:

{% highlight bash %}
git clone https://github.com/jhpyle/docassemble-os
cd docassemble-os
docker build -t jhpyle/docassemble-os .
cd ..
{% endhighlight %}

The [docassemble-os repository] consists of a [Dockerfile] only.  Note
that the first line of the [Dockerfile] in the [docassemble
repository] is:

{% highlight text %}
FROM jhpyle/docassemble-os
{% endhighlight %}

Thus, the `jhpyle/docassemble` image incorporates by reference the
`jhpyle/docassemble-os` base image.  The [`docker build`] command
above overwrites the `jhpyle/docassemble-os` image that is stored on
your local machine.  If you want, you can edit the [Dockerfile] before
building your custom `jhpyle/docassemble` version so that it
references a different base image.

The versioning of the [docassemble-os repository] on [GitHub] follows
that of the [docassemble repository].  The two repositories are
maintained together.  However, the latest version of the
[docassemble-os repository] is usually several versions behind that of
the [docassemble repository].

# <a name="cleanup"></a>Cleaning up after Docker

If you run [`docker pull`] to retrieve new versions of **docassemble**,
or you build your own **docassemble** images more than once, you may
find your disk space being used up.  The full **docassemble** image is
about 4GB in size, and whenever you run [`docker pull`] or build a new
image, a new image is created -- the old image is not overwritten.

The following three lines will stop all containers, remove all
containers, and then remove all of the images that [Docker] created
during the build process.

{% highlight bash %}
docker stop -t 600 $(docker ps -a -q)
docker rm $(docker ps -a -q)
docker rmi $(docker images | grep "^<none>" | awk "{print $3}")
{% endhighlight %}

The last line, which deletes images, frees up the most disk space.  It
is usually necessary to remove the containers first (the [`docker rm`]
line), as the containers depend on the images.

[Redis]: http://redis.io/
[Docker installation instructions for Windows]: https://docs.docker.com/engine/installation/windows/
[Docker installation instructions for OS X]: https://docs.docker.com/engine/installation/mac/
[Docker]: https://www.docker.com/
[Amazon AWS]: http://aws.amazon.com
[automated build]: https://docs.docker.com/docker-hub/builds/
[scalability of docassemble]: {{ site.baseurl }}/docs/scalability.html
[Amazon Linux]: https://aws.amazon.com/amazon-linux-ami/
[EC2]: https://aws.amazon.com/ec2/
[single-server arrangement]: #single server arrangement
[multi-server arrangement]: #multi server arrangement
[EC2 Container Service]: https://aws.amazon.com/ecs/
[S3]: https://aws.amazon.com/s3/
[supervisor]: http://supervisord.org/
[hosted on Docker Hub]: https://hub.docker.com/r/jhpyle/docassemble/
[Docker Hub]: https://hub.docker.com/
[scalability]: {{ site.baseurl }}/docs/scalability.html
[Amazon S3]: https://aws.amazon.com/s3/
[using HTTPS]: #https
[docassemble repository]: {{ site.github.repository_url }}
[`docassemble/Dockerfile`]: {{ site.github.repository_url }}/blob/master/Dockerfile
[`docassemble/Docker/config/config.yml.dist`]: {{ site.github.repository_url }}/blob/master/Docker/config/config.yml.dist
[`docassemble/Docker/initialize.sh`]: {{ site.github.repository_url }}/blob/master/Docker/initialize.sh
[`docassemble/Docker/config/docassemble-http.conf.dist`]: {{ site.github.repository_url }}/blob/master/Docker/config/docassemble-http.conf.dist
[`docassemble/Docker/config/docassemble-ssl.conf.dist`]: {{ site.github.repository_url }}/blob/master/Docker/config/docassemble-ssl.conf.dist
[`docassemble/Docker/config/docassemble-log.conf.dist`]: {{ site.github.repository_url }}/blob/master/Docker/config/docassemble-log.conf.dist
[`docassemble/Docker/config/nginx-http.dist`]: {{ site.github.repository_url }}/blob/master/Docker/config/nginx-http.dist
[`docassemble/Docker/config/nginx-ssl.dist`]: {{ site.github.repository_url }}/blob/master/Docker/config/nginx-ssl.dist
[`docassemble/Docker/config/nginx-log.dist`]: {{ site.github.repository_url }}/blob/master/Docker/config/nginx-log.dist
[`docassemble/Docker/ssl/apache.crt.orig`]: {{ site.github.repository_url }}/blob/master/Docker/ssl/apache.crt.orig
[`docassemble/Docker/ssl/apache.key.orig`]: {{ site.github.repository_url }}/blob/master/Docker/ssl/apache.key.orig
[`docassemble/Docker/ssl/apache.ca.pem.orig`]: {{ site.github.repository_url }}/blob/master/Docker/ssl/apache.ca.pem.orig
[`docassemble/Docker/ssl/nginx.crt.orig`]: {{ site.github.repository_url }}/blob/master/Docker/ssl/nginx.crt.orig
[`docassemble/Docker/ssl/nginx.key.orig`]: {{ site.github.repository_url }}/blob/master/Docker/ssl/nginx.key.orig
[`docassemble/Docker/ssl/exim.crt.orig`]: {{ site.github.repository_url }}/blob/master/Docker/ssl/exim.crt.orig
[`docassemble/Docker/ssl/exim.key.orig`]: {{ site.github.repository_url }}/blob/master/Docker/ssl/exim.key.orig
[`docassemble/Docker/docassemble.conf`]: {{ site.github.repository_url }}/blob/master/Docker/docassemble.conf
[`docassemble/Docker/docassemble-supervisor.conf`]: {{ site.github.repository_url }}/blob/master/Docker/docassemble-supervisor.conf
[`docassemble/Docker/docassemble-syslog-ng.conf`]: {{ site.github.repository_url }}/blob/master/Docker/docassemble-syslog-ng.conf
[`docassemble/Docker/syslog-ng.conf`]: {{ site.github.repository_url }}/blob/master/Docker/syslog-ng.conf
[`docassemble/Docker/rabbitmq.config`]: {{ site.github.repository_url }}/blob/master/Docker/rabbitmq.config
[`docassemble/Docker/nginx.logrotate`]: {{ site.github.repository_url }}/blob/master/Docker/nginx.logrotate
[`docassemble/Docker/docassemble.logrotate`]: {{ site.github.repository_url }}/blob/master/Docker/docassemble.logrotate
[`docassemble/Docker/apache.logrotate`]: {{ site.github.repository_url }}/blob/master/Docker/apache.logrotate
[`docassemble/Docker/process-email.sh`]: {{ site.github.repository_url }}/blob/master/Docker/process-email.sh
[`docassemble/Docker/run-postgresql.sh`]: {{ site.github.repository_url }}/blob/master/Docker/run-postgresql.sh
[`docassemble/Docker/run-nginx.sh`]: {{ site.github.repository_url }}/blob/master/Docker/run-nginx.sh
[`docassemble/Docker/run-uwsgi.sh`]: {{ site.github.repository_url }}/blob/master/Docker/run-uwsgi.sh
[`docassemble/Docker/run-apache.sh`]: {{ site.github.repository_url }}/blob/master/Docker/run-apache.sh
[`docassemble/Docker/run-celery.sh`]: {{ site.github.repository_url }}/blob/master/Docker/run-celery.sh
[`docassemble/Docker/run-cron.sh`]: {{ site.github.repository_url }}/blob/master/Docker/run-cron.sh
[`docassemble/Docker/run-rabbitmq.sh`]: {{ site.github.repository_url }}/blob/master/Docker/run-rabbitmq.sh
[`docassemble/Docker/run-redis.sh`]: {{ site.github.repository_url }}/blob/master/Docker/run-redis.sh
[`docassemble/Docker/run-syslogng.sh`]: {{ site.github.repository_url }}/blob/master/Docker/run-syslogng.sh
[`docassemble/Docker/run-websockets.sh`]: {{ site.github.repository_url }}/blob/master/Docker/run-websockets.sh
[`docassemble/Docker/reset.sh`]: {{ site.github.repository_url }}/blob/master/Docker/reset.sh
[`docassemble/Docker/sync.sh`]: {{ site.github.repository_url }}/blob/master/Docker/sync.sh
[`docassemble/Docker/update.sh`]: {{ site.github.repository_url }}/blob/master/Docker/update.sh
[template for the `env.list` file]: {{ site.github.repository_url }}/blob/master/Docker/env.list
[ECS]: https://aws.amazon.com/ecs/
[JSON]: https://en.wikipedia.org/wiki/JSON
[PostgreSQL]: http://www.postgresql.org/
[HTTPS]: #https
[Let's Encrypt]: https://letsencrypt.org/
[using Let's Encrypt]: #letsencrypt
[Apache]: https://en.wikipedia.org/wiki/Apache_HTTP_Server
[Python virtualenv]: {{ site.baseurl }}/docs/installation.html#virtualenv
[Playground]: {{ site.baseurl }}/docs/playground.html
[Syslog-ng]: https://en.wikipedia.org/wiki/Syslog-ng
[persistent volumes]: #persistent
[persistent volume]: #persistent
[configuration]: {{ site.baseurl }}/docs/config.html
[`db`]: {{ site.baseurl }}/docs/config.html#db
[`db` section]: {{ site.baseurl }}/docs/config.html#db
[`s3` section]: {{ site.baseurl }}/docs/config.html#s3
[`azure` section]: {{ site.baseurl }}/docs/config.html#azure
[Build your own private image]: #build
[Redis]: http://redis.io/
[RabbitMQ]: https://www.rabbitmq.com/
[Application Load Balancer]: https://aws.amazon.com/elasticloadbalancing/applicationloadbalancer/
[IAM]: https://aws.amazon.com/iam/
[Amazon]: https://amazon.com
[load balancer]: https://en.wikipedia.org/wiki/Load_balancing_(computing)
[S3 Console]: https://console.aws.amazon.com/s3/home
[IAM Console]: https://console.aws.amazon.com/iam
[create your own Docker image]: #build
[`certs`]: {{ site.baseurl }}/docs/config.html#certs
[`cert install directory`]: {{ site.baseurl }}/docs/config.html#cert install directory
[cron]: https://en.wikipedia.org/wiki/Cron
[Python virtual environment]: http://docs.python-guide.org/en/latest/dev/virtualenvs/
[configuration options]: #configuration options
[configuration option]: #configuration options
[`DBHOST`]: #DBHOST
[`DBNAME`]: #DBNAME
[`DBUSER`]: #DBUSER
[`DBPASSWORD`]: #DBPASSWORD
[`DBPREFIX`]: #DBPREFIX
[`DBPORT`]: #DBPORT
[`DBTABLEPREFIX`]: #DBTABLEPREFIX
[`REDIS`]: #REDIS
[`RABBITMQ`]: #RABBITMQ
[`LOGSERVER`]: #LOGSERVER
[`S3BUCKET`]: #S3BUCKET
[`S3ACCESSKEY`]: #S3ACCESSKEY
[`S3REGION`]: #S3REGION
[`S3SECRETACCESSKEY`]: #S3SECRETACCESSKEY
[`AZURECONTAINER`]: #AZURECONTAINER
[`AZUREACCOUNTNAME`]: #AZUREACCOUNTNAME
[`AZUREACCOUNTKEY`]: #AZUREACCOUNTKEY
[`CONTAINERROLE`]: #CONTAINERROLE
[`SERVERHOSTNAME`]: #SERVERHOSTNAME
[`DAHOSTNAME`]: #DAHOSTNAME
[`USEHTTPS`]: #USEHTTPS
[`USELETSENCRYPT`]: #USELETSENCRYPT
[`DAEXPOSEWEBSOCKETS`]: #DAEXPOSEWEBSOCKETS
[`DAWEBSOCKETSIP`]: #DAWEBSOCKETSIP
[`DAWEBSOCKETSPORT`]: #DAWEBSOCKETSPORT
[`DAUPDATEONSTART`]: #DAUPDATEONSTART
[Celery]: http://www.celeryproject.org/
[background processes]: {{ site.baseurl }}/docs/background.html#background
[Windows PowerShell]: https://en.wikipedia.org/wiki/PowerShell
[Terminal]: https://en.wikipedia.org/wiki/Terminal_(macOS)
[`timezone`]: {{ site.baseurl }}/docs/config.html#timezone
[`os locale`]: {{ site.baseurl }}/docs/config.html#os locale
[`other os locales`]: {{ site.baseurl }}/docs/config.html#other os locales
[`behind https load balancer`]: {{ site.baseurl }}/docs/config.html#behind https load balancer
[`rabbitmq`]: {{ site.baseurl }}/docs/config.html#rabbitmq
[`redis`]: {{ site.baseurl }}/docs/config.html#redis
[`log server`]: {{ site.baseurl }}/docs/config.html#log server
[`lets encrypt email`]: {{ site.baseurl }}/docs/config.html#lets encrypt email
[`use lets encrypt`]: {{ site.baseurl }}/docs/config.html#use lets encrypt
[`external hostname`]: {{ site.baseurl }}/docs/config.html#external hostname
[`use https`]: {{ site.baseurl }}/docs/config.html#use https
[`ec2`]: {{ site.baseurl }}/docs/config.html#ec2
[`kubernetes`]: {{ site.baseurl }}/docs/config.html#kubernetes
[`debian packages`]: {{ site.baseurl }}/docs/config.html#debian packages
[`url root`]: {{ site.baseurl }}/docs/config.html#url root
[`root`]: {{ site.baseurl }}/docs/config.html#root
[Debian]: https://www.debian.org/
[using S3]: #persistent s3
[using Azure blob storage]: #persistent azure
[environment variables]: #configuration options
[GitHub]: https://github.com
[data storage]: #data storage
[`docker stop`]: https://docs.docker.com/engine/reference/commandline/stop/
[`docker rm`]: https://docs.docker.com/engine/reference/commandline/rm/
[`docker rmi`]: https://docs.docker.com/engine/reference/commandline/rmi/
[`docker exec`]: https://docs.docker.com/engine/reference/commandline/exec/
[`docker start`]: https://docs.docker.com/engine/reference/commandline/start/
[`docker run`]: https://docs.docker.com/engine/reference/commandline/run/
[`docker build`]: https://docs.docker.com/engine/reference/commandline/build/
[`docker ps`]: https://docs.docker.com/engine/reference/commandline/ps/
[`docker pull`]: https://docs.docker.com/engine/reference/commandline/pull/
[`docker volume`]: https://docs.docker.com/engine/reference/commandline/volume/
[`docker volume inspect`]: https://docs.docker.com/engine/reference/commandline/volume_inspect/
[`docker volume create`]: https://docs.docker.com/engine/reference/commandline/volume_create/
[Amazon Web Services]: https://aws.amazon.com
[AWS]: https://aws.amazon.com
[S3 bucket]: http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingBucket.html
[scheduled tasks]: {{ site.baseurl }}/docs/background.html#scheduled
[WebSocket]: https://en.wikipedia.org/wiki/WebSocket
[e-mails]: {{ site.baseurl }}/docs/background.html#email
[e-mail receiving]: {{ site.baseurl }}/docs/background.html#email
[Exim]: https://en.wikipedia.org/wiki/Exim
[TLS]: https://en.wikipedia.org/wiki/Transport_Layer_Security
[`incoming mail domain`]: {{ site.baseurl }}/docs/config.html#incoming mail domain
[git]: https://en.wikipedia.org/wiki/Git
[without Let's Encrypt]: #without letsencrypt
[script]: {{ site.github.repository_url }}/blob/master/Docker/cron/docassemble-cron-weekly.sh
[TLS encryption]: #tls
[Azure Portal]: https://portal.azure.com/
[Azure blob storage]: https://azure.microsoft.com/en-us/services/storage/blobs/
[Microsoft Azure]: https://azure.microsoft.com/
[blob storage container]: https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-how-to-use-blobs#create-a-container
[certbot]: https://certbot.eff.org/
[sudo]: https://en.wikipedia.org/wiki/Sudo
[ssh]: https://en.wikipedia.org/wiki/Secure_Shell
[CNAME record]: https://en.wikipedia.org/wiki/CNAME_record
[A record]: https://en.wikipedia.org/wiki/List_of_DNS_record_types#A
[Security Group]: http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html
[HTTP]: https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol
[region]: http://docs.aws.amazon.com/general/latest/gr/rande.html
[`secretkey`]: {{ site.baseurl }}/docs/config.html#secretkey
[Cross-Origin Resource Sharing]: https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS
[`cross site domains`]: {{ site.baseurl }}/docs/config.html#cross site domains
[`server administrator email`]: {{ site.baseurl }}/docs/config.html#server administrator email
[e-mail setup]: {{ site.baseurl }}/docs/installation.html#setup_email
[installation]: {{ site.baseurl }}/docs/installation.html
[e-mailing the interview]: {{ site.baseurl }}/docs/background.html#email
[Python]: https://en.wikipedia.org/wiki/Python_%28programming_language%29
[virtual machine]: https://en.wikipedia.org/wiki/Virtual_machine
[upgrading]: #upgrading
[`Dockerfile`]: {{ site.github.repository_url }}/blob/master/Dockerfile
[Logs]: {{ site.baseurl }}/docs/admin.html#logs
[Supervisor]: http://supervisord.org/
[syslog-ng]: https://en.wikipedia.org/wiki/Syslog-ng
[database corruption]: #shutdown
[debugging subsection]: {{ site.baseurl }}/docs/installation.html#debug
[Traefik]: https://traefik.io/
[Flask]: http://flask.pocoo.org/
[third party providers]: {{ site.baseurl }}/deploy.html
[troubleshooting]: #troubleshooting
[`backup days`]: {{ site.baseurl }}/docs/config.html#backup days
[`python packages`]: {{ site.baseurl }}/docs/config.html#python packages
[`xsendfile`]: {{ site.baseurl }}/docs/config.html#xsendfile
[`ls`]: https://kb.iu.edu/d/afsk#ls
[`cd`]: https://kb.iu.edu/d/afsk#cd
[`less`]: https://kb.iu.edu/d/afsk#less
[`nano`]: https://wiki.gentoo.org/wiki/Nano/Basics_Guide
[`vi`]: https://www.engadget.com/2012/07/10/vim-how-to/
[WSGI]: http://en.wikipedia.org/wiki/Web_Server_Gateway_Interface
[pip]: https://en.wikipedia.org/wiki/Pip_%28package_manager%29
[Configuration]: {{ site.baseurl }}/docs/config.html
[`expose websockets`]: {{ site.baseurl }}/docs/config.html#expose websockets
[`websockets ip`]: {{ site.baseurl }}/docs/config.html#websockets ip
[`websockets port`]: {{ site.baseurl }}/docs/config.html#websockets port
[Logs]: {{ site.baseurl }}/docs/admin.html#logs
[web interface]: {{ site.baseurl }}/docs/admin.html#configuration
[behind a reverse proxy]: #forwarding
[live help]: {{ site.baseurl }}/docs/livehelp.html
[`DARedis`]: {{ site.baseurl }}/docs/objects.html#DARedis
[NGINX]: https://www.nginx.com/
[uWSGI]: https://uwsgi-docs.readthedocs.io/en/latest/
[docassemble-os repository]: https://github.com/jhpyle/docassemble-os
[Dockerfile]: https://docs.docker.com/engine/reference/builder/
[`web server`]: {{ site.baseurl }}/docs/config.html#web server
[`docker stop -t 600`]: https://docs.docker.com/engine/reference/commandline/stop/
[MinIO]: https://min.io/
[`use minio`]: {{ site.baseurl }}/docs/config.html#use minio
[Kubernetes]: https://kubernetes.io/
[`collect statistics`]: {{ site.baseurl }}/docs/config.html#collect statistics
[Kubernetes]: https://kubernetes.io/
[`update on start`]: {{ site.baseurl }}/docs/config.html#update on start
[`allow updates`]: {{ site.baseurl }}/docs/config.html#allow updates
[`stable version`]: {{ site.baseurl }}/docs/config.html#stable version
[`ssl_protocols`]: http://nginx.org/en/docs/http/configuring_https_servers.html
[`sql ping`]: {{ site.baseurl }}/docs/config.html#sql ping
[with Kubernetes]: https://github.com/jhpyle/charts
[Rook]: https://rook.io/docs/rook/v0.8/object.html
[Vultr]: https://www.vultr.com/docs/vultr-object-storage
[Google Cloud]: https://cloud.google.com/storage/docs/interoperability
[Wasabi]: https://wasabi.com/s3-compatible-cloud-storage/
[Linode]: https://www.linode.com/docs/platform/object-storage/how-to-use-object-storage/
[Digital Ocean]: https://developers.digitalocean.com/documentation/spaces/#aws-s3-compatibility
[IBM Cloud]: https://cloud.ibm.com/apidocs/cos/cos-compatibility
[Oracle Cloud]: https://docs.cloud.oracle.com/en-us/iaas/Content/Object/Tasks/s3compatibleapi.htm
[Scaleway]: https://www.scaleway.com/en/object-storage/
[Exoscale]: https://www.exoscale.com/object-storage/
[`S3ENDPOINTURL`]: #S3ENDPOINTURL
[`use cloud urls`]: {{ site.baseurl }}/docs/config.html#use cloud urls
[YAML]: https://en.wikipedia.org/wiki/YAML
[`nginx ssl protocols`]: {{ site.baseurl }}/docs/config.html#nginx ssl protocols
[Helm]: https://helm.sh/
