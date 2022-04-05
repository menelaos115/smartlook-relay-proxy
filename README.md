# Smartlook Relay Proxy

This service relays HTTP requests from your subdomain to
Smartlook's recording domains, allowing you to effectively bypass Ad blockers used
by your visitors.
> **Only web recording is currently supported.**  
> Support for Mobile SDKs will be added soon.

## Environment variables

| Name                      | Type     | Default value                             | Description                                                         |
| ------------------------- | -------- | ----------------------------------------- | ------------------------------------------------------------------- |
| `LOGGER_LEVEL`            | `string` | `'info'`                                  | One of `trace`, `debug`, `info`, `warn`, `error`, `fatal`, `silent` |
| `MANAGER_HOST`            | `string` | `'https://manager.eu.smartlook.cloud'`    | Smartlook Manager host                                              |
| `WEB_SDK_WRITER_HOST`     | `string` | `'https://web-writer.eu.smartlook.cloud'` | Smartlook Web Writer host                                           |
| `ASSETS_PROXY_HOST`       | `string` | `'https://assets-proxy.smartlook.cloud'`  | Smartlook Assets Proxy host                                         |

## Installation and deployment

Image is available on [Docker Hub](https://hub.docker.com/r/smartlook/relay-proxy).

1. Run the image and configure environment variables according to your needs.  
**All variables are optional, default values are listed in the table above**.

```sh
docker run --name="smartlook-relay-proxy" -d \
  # can be also set with "--env-file"
  -e LOGGER_LEVEL=<level> \
  -e MANAGER_HOST=<host> \
  -e WEB_SDK_WRITER_HOST=<host> \
  -e ASSETS_PROXY_HOST=<host> \
  -p <port>:8000 \  # mapping is optional, internally runs on port 8000
  smartlook/relay-proxy:latest
```

2. Create a new subdomain (e.g. *sl.yourdomain.com*) and point it to the container.

3. Replace URLs in Smartlook script (**only next-gen tracking code is supported**).

```html
<script>
  window.smartlook ||
    (function (d) {
    var o = (smartlook = function () {
      o.api.push(arguments)
    }), h = d.getElementsByTagName('head')[0];
    var c = d.createElement('script');
    o.api = new Array();
    c.async = true;
    c.crossOrigin = 'anonymous';
    c.type = 'text/javascript';
    c.charset = 'utf-8';
    c.src = 'https://<your-relay-proxy-host>/recorder.js';
    h.appendChild(c);
  })(document);
  smartlook('init', <project-key>, { host: '<your-relay-proxy-host>' });
</script>
```

4. You're done! Visit your site and check the network tab to make sure the requests are sent through your subdomain.
![network tab screenshot](https://raw.githubusercontent.com/smartlook/smartlook-relay-proxy/main/network.png)

## Development

```sh
# See all available commands
make help

# Build and start development server (port 80 by default)
# Do not forget to configure environment variables in docker-compose.dev.yml
make dev # add `build=yes` for rebuild
```

## License

See [LICENSE](LICENSE).
