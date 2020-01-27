# OnDemand Docker

Run [Open-OnDemand](https://osc.github.io/ood-documentation/master/index.html) inside a docker container. This will
setup a basic OnDemand setup inside a docker container, create a new user, set it's password, and start the HTTP server.
This should minimize most effort of setup for the base application.

### Setup

It's docker, so there's not much here. You do need to do a bit of configuration to customize your installation, or you
could just run it as-is. For those that need to customize, here's how.

#### Set SSH Port
If you are developing on a machine that has SSH enabled on port 22, you will not be able to expose the container SSH port
to the host on port 22. You may either forward, or just run SSH on an entirely different port.

**Forwarding**: In `docker-compose.yml`, change line `6` that currently has `221:221` to `<desired host port>:22`, where
`<desired host port>` is whichever port on the host you'd like to expose the container's port `22` on.

**Alternate SSH Port**: Currently, this is setup to run SSH on port `221`. If you want to change the port, follow these
steps:

1. In `docker/sshd_config` on line `17` that currently has `PORT 221`, change that to `PORT <your desired SSH port>`
2. In `docker-compose.yml` on line `6` that currently has `221:221`, change that to `<port>:<port>` where `<port>`
is the port you entered in step 1.

#### Set Credentials

You will set your credentials for OnDemand and your root password in `docker/Dockerfile` on lines `4` through `6`.
**You cannot run OnDemand as root**, so change your `ONDEMAND_USER` to something else that's not `root`.

#### Build

To build the container, run `docker-compose build`

#### Start OnDemand

To start the container and run OnDemand, run `docker-compose up`.

To run in _daemon mode_, run `docker-compose up -d`.

#### Access OnDemand

If you are running your docker daemon locally (which most people are), then you can access OnDemand at
`http://localhost/`.

If for some reason you are running your docker daemon remotely, or have assigned a different IP to your container, you
will access OnDemand from that IP address.

To login, your credentials will be whatever you entered in `docker/Dockerfile` for `ONDEMAND_USER`
AND `ONDEMAND_PASSWORD`.

#### SSH Access

**If you are on Linux/Unix/MacOS:** First, from your project directory, run `chmod +x bin/ssh`.

You can easily SSH into the container with `bin/ssh` on Linux/Unix/MacOS, and `bin\ssh` on Windows.

You may also use `docker exec -it ondemand-docker /bin/bash`.