# Vagrant Docker Starter

This project is intended to serve as a template for your projects
where you want to use [Vagrant](http://vagrantup.com) and
[Docker](http://docker.com) to ensure consistency
in your deployments, as well as your development environment.

The templates within provide a standard (but opinionated) way to have a
`docker-host` Vagrant machine which can run, orchestrate, and link
various Docker containers, including your app, and any other services
(PostgreSQL, Redis, etc.) it may depend on. A `docker-host` machine is used
to guarantee consistency (often certain things will need to be provisioned on
the host, and it's best to keep this isolated and off your local OS), and also
to ensure that it works across platforms (notably OSX) that may not support
docker natively.

# Quick Start

The following examples help you see how `vagrant-docker-starter` "works":

```bash
# Run these from this project root

# See the docker-host + all configured containers
vagrant status

# Boot the docker-host VM
vagrant up docker-host

# Start the postgres container
vagrant up postgres

# Start the redis container
vagrant up redis

# Reload the redis container
vagrant reload redis

# Stop the redis + postgres containers
vagrant halt redis postgres

# Shut down the docker-host VM
vagrant halt docker-host

# Start all containers (in order)
# If your containers use links, you'll need to use --no-parallel
# To ensure they are started sequentially
vagrant up --no-parallel
```

# Structure

By default, the `Vagrantfile` and it's config `YAML` file are placed in the
`ops/` directory, to keep the root uncluttered.  These files can be moved
anywhere, but if you move the `Vagrantfile`, you may have to update the path
to the `vagrant-config.yml`.  Additionally, moving where the `Vagrantfile` is
will change what directory you have to run your `vagrant` commands from.

The `ops/` directory is also the place where you can add directories and files
needed to provision your `docker-host`, and for docker config files or scripts
needed to build images for your particular project.  We find it's best to
keep these out of your project root, and the `ops/` directory seems suited
for this.

You should ideally not have to modify the `Vagrantfile`, as most of the
configuration for both the `docker-host` and all of your containers can be
kept in the `vagrant-config.yml` file, which, by default, resides in the
`ops/` directory.

## vagrant-config.yml

The `vagrant-config.yml` contains 2 main sections:
[Docker_host](#docker_host) and [Containers](#containers).

### docker\_host

The `docker\_host` section allows you to customize the memory, cpus, and 
ports forwarded to the host machine.

### containers

You specify your docker containers the `vagrant-config.yml` file under the
`containers` section.  The general format inside the `containers` section
matches the [docker-compose](https://docs.docker.com/compose/compose-file/)
file format as closely as possible.

# Using Vagrant Docker Starter in your project

You can create a new project directory from the templates here pretty easily
with `git-archive` using the following:

```bash
git archive master | tar -x -C /new/project/
```

# How is this different from vagrant-docker-compose

[Vagrant-docker-compose](https://github.com/leighmcculloch/vagrant-docker-compose)
is a great little Vagrant plugin, but it does not show you all of your
containers when running `vagrant status`.  It will only show the docker host.
This makes it more cumbersom to see which containers your project contains,
which containers are running, and to start/stop/reload specifc containers at
will.

With `vagrant-docker-compose` I find that it is not easy to use the
`vagrant docker-run` command, which can come in handy for running one-off
commands from within a container, for testing, debugging, or similar.

Additionally, `vagrant-docker-compose` does not make it easy to have special
containers for tasks such as building `golang` projects, or other one-off
pre-process tasks.

## docker-compose.yml compatible

The good news is that I have structured the `vagrant-config.yml` file
to be mostly `docker-compose` compatible.  This means that the `containers`
section of the `vagrant-config.yml` file used by this project, will also
work with `docker-compose` if broken out into it's own file.  And, thus,
this section will also work with `vagrant-docker-compose`.

If you give these templates a try, and they're not for you, it's easy to
switch your work over!

# TODO

* Ordering of how containers come up would be nice
* Make it easy to provision the `docker-host` with `:shell`, `:chef\_solo`,
  `:ansible`, etc.

# LICENSE

Public Domain, of course.
