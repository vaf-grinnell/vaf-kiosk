# VAF - Public Website Version

This is the **main** branch and public website version of **VAF**.  Object data in this project is held in `./data/items.toml` and in this version each of the `[[items]]` keys DOES have a `url` key/value pair.  This is largely what makes this **public-facing** version different than the corresponding **kiosk** version which can now be found in https://github.com/vaf-grinnell/vaf-kiosk.  The **kiosk** version is built to run on the iPad kiosk in the north end of the HSSC atrium on campus.

## Deployed in GitHub Pages

Pushing changes to this site should automatically deploy it at https://vaf.grinnell.edu/vaf-kiosk/.


## Obsolete - Instructions Beyond This Point Are Out-of-Date!

As of 30-Apr-2020, this site is intended to be deployed using my [docker-traefik2-host](https://github.com/McFateM/docker-traefik2-host) approach.  A `docker container run` command is no longer used to launch [the site](https://static.grinnell.edu/) on Grinnell College's `static.Grinnell.edu` server, so no more...

```
NAME=vaf                                      # [substitute]
HOST=vaf.grinnell.edu                         # [substitute]
IMAGE="mcfatem/vaf"                           # [substitute]
docker container run -d --name vaf \
    --label traefik.backend=vaf \
    --label traefik.docker.network=web \
    --label "traefik.frontend.rule=Host:vaf.grinnell.edu" \
    --label traefik.port=80 \
    --label com.centurylinklabs.watchtower.enable=true \
    --network web \
    --restart always \
  mcfatem/vaf
```

### Now Using Docker-Compose

With the introduction of [Traefik v2.x](https://traefik.io) this site now relies solely on files, most importantly `docker-compose.yml`, and a single `docker-compose up -d` command, to execute a one-time application launch. On Grinnell's `static.grinnell.edu` server, after the host has been initailized (see [README.md](https://github.com/McFateM/docker-traefik2-host)), the whole command sequence, executed as _root_, looked like this:

```
sudo su
cd /opt/containers
git clone --recursive https://github.com/McFateM/vaf
cd vaf
docker-compose up -d
```

Since the above commands have already been run once, there should be no need to do it again. However, the pertinent portions of the process can now be specified like so:

```
sudo su
cd /opt/containers/vaf
git pull  # assumes the git remote is origin -> https://github.com/McFateM/vaf
docker-compose up -d
```

## The Kiosk Version

So, it looks like my old `kiosk` branch is gone?  Hmmm, that's unfortunate. Fortunately, there should still be a working build of it in my Docker Hub at `mcfatem/vaf-kiosk`.  So, to deploy that version try this...

```
sudo su
cd /opt/containers/vaf
docker-compose -f docker-compose.kiosk.yml up -d
```

## Local Development

It is recommended that you clone (or fork and clone) this repository to an OS X workstation where [Hugo](https://gohugo.io) is installed and running an up-to-date version.

My typical workflow for local development is:

```
cd ~/GitHub/
git clone https://github.com/McFateM/vaf
cd vaf
git checkout -b <new-branch-name>
atom .
hugo server
```

The `atom .` command opens the project in my [Atom](https://atom.io) editor, and `hugo server` launches a local instance of the site and provides a link to that site if there are no errors.  This local site will respond immediately to any changes made in Atom.

# Updating the Production Server

You can use a `./push-update.sh` command to push your changes into production.  Study the `./push-update.sh` script and corresponding `push-update-Dockerfile` configuration to see all that it does.

# An Even Easier Update

Not long ago I added the _Atom Shell Commands_ package to my _Atom_ config, added a command named **Push a Static Update**, and pointed that command at the _push_update.sh_ script that is now part of this project.
