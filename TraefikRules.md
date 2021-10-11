### To launch the image as https://vaf.grinnell.edu

```
IMAGE="mcfatem/vaf"
docker container run -d --name vaf \
    --label traefik.backend=vaf \
    --label traefik.docker.network=web \
    --label traefik.port=80 \
    --label com.centurylinklabs.watchtower.enable=true \
    --network web \
    --restart always \
    --label "traefik.frontend.redirect.regex=^static.grinnell.edu/vaf$" \
    --label "traefik.frontend.redirect.replacement=static.grinnell.edu/vaf/" \
    --label "traefik.frontend.rule=Host:static.grinnell.edu;PathPrefixStrip:/vaf/" \
${IMAGE}
```

--label "traefik.frontend.rule=Host:vaf.grinnell.edu;ReplacePath:/vaf/" \
