# Pushing VAF to Production

> Note that as of 30-Apr-2020 much of this document is obsolete and deprecated.

First and foremost, be aware that there are TWO versions of the VAF website stored in two different branches of the `private` project repository at `https:/github.com/McFateM/vaf`:

  - master : The public version with links to Digital Grinnell and other references.  https://vaf.grinnell.edu
  - kiosk : The kiosk version with no live links.  https://vaf-kiosk.grinnell.edu

Most of what follows assumes you are building and intend to deploy `vaf.grinnell.edu`.  If working on the kiosk version be sure to substitute `vaf-kiosk` in place of `vaf` wherever you see `[substitute]` noted below.

## To develop locally using Docksal...

```
cd ~/Docksal-Projects/sites
git clone https://github.com/McFateM/vaf.git
cd vaf
git clone https://github.com/DigitalGrinnell/hugrid.git themes/hugrid
fin up
fin develop
```

Visit http://vaf.docksal to interact with the site.  In this mode changes made within the local `vaf` directory will be automatically refreshed into http://vaf.docksal.  Be careful with caching in this mode!

## To build and test a new image locally...

```
git clone https://github.com/McFateM/vaf.git
cd vaf
docker image build -t vaf-test .     # <-- executed from my project directory, builds a new up-to-date image
http://localhost:8081
```

### To push the new image to Docker Hub...

```
docker login
docker tag vaf-test mcfatem/vaf:latest       # [substitute]
docker push mcfatem/vaf:latest
```

### To launch the image as https://vaf.grinnell.edu visit `static.grinnell.edu` and use this:

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
Note: In the above command the variables `NAME`, `HOST`, and `IMAGE` are no longer necessary as references to them have been hard-coded into the `docker container run...` command.  But if you choose to use this snippet to deploy `vaf-kiosk` you will need to make substitutions inside the `docker container run...` command, like so:

```
docker container run -d --name vaf-kiosk \
    --label traefik.backend=vaf-kiosk \
    --label traefik.docker.network=web \
    --label "traefik.frontend.rule=Host:vaf-kiosk.grinnell.edu" \
    --label traefik.port=80 \
    --label com.centurylinklabs.watchtower.enable=true \
    --network web \
    --restart always \
mcfatem/vaf-kiosk
```

### OR, to launch the image as https://static.grinnell.edu/vaf see the gist at https://gist.github.com/McFateM/a008a99f25478cd6e73e463e769c7d75.

```
NAME=vaf
HOST=static.grinnell.edu
IMAGE="mcfatem/vaf"
docker container run -d --name ${NAME} \
    --label traefik.backend=${NAME} \
    --label traefik.docker.network=traefik_webgateway \
    --label "traefik.frontend.rule=Host:${HOST};PathPrefixStrip:/vaf" \
    --label traefik.port=80 \
    --label com.centurylinklabs.watchtower.enable=true \
    --network traefik_webgateway \
    --restart always \
${IMAGE}
```

Original TIFF images are held in `static/images/originals`.  I used https://hackernoon.com/save-time-by-transforming-images-in-the-command-line-c63c83e53b17 and *Imagemagick*'s `mogrify` and `convert` commands to produce the thumbnail and full-size images found in `static/images/thumbs` and `static/images/full`, respectively.

Resizing was based on a 7x5 (horizontal x vertical) grid and assumed use of a 3rd or 4th generation Apple iPad with screen resolution of 2048x1536 (landscape).  Thumbnail size is 280x280 pixels and full-size are 1250x1250.

## Kiosk
The new specs say the block arrangement will be 5 wide x 7 tall, and that should work nicely for an iPad kiosk in a *portrait* orientation.  However, when we emulate this the thumbnails appear to be twice as wide as necessary, only 2 will fit side-by-side on the iPad screen.  So, on 7-Dec-2018 I created a new local `half-size` branch of the project and hit the `static/images/thumbs` folder with:

```
mogrify -resize 50% *.png
```

I also found that the banner image was too wide for the iPad so I hit the `static/images` directory with:

```
mogrify -resize 75% *.png
```
