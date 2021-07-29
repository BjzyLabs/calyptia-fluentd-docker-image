Calyptia Fluentd Docker Image
=============================

## What is Fluentd?

Fluentd is an open source data collector, which lets you unify the data
collection and consumption for a better use and understanding of data.

> [www.fluentd.org](https://www.fluentd.org/)

![Fluentd Logo](https://www.fluentd.org/assets/img/miscellany/fluentd-logo.png)

## Settings for Calyptia Cloud

This docker image is working well with [cmetrics Fluentd extension for metrics plugin](https://github.com/calyptia/fluent-plugin-metrics-cmetrics).

And enabling RPC and configDump endpoint is required if sending Fluentd configuration for now:

### Example settings for Calyptia Cloud

```aconf
<system>
# If users want to use multi workers feature which corresponds to logical number of CPUs, please comment out this line.
#  workers "#{require 'etc'; Etc.nprocessors}"
  enable_input_metrics true
  # This record size measuring settings might impact for performance.
  # Please be careful for high loaded environment to turn on.
  enable_size_metrics true
  <metrics>
    @type cmetrics
  </metrics>
  rpc_endpoint 127.0.0.1:24444
  enable_get_dump true
</system>
# And other configurations....

## Fill YOUR_API_KEY with your Calyptia API KEY
<source>
  @type calyptia_monitoring
  @id input_caplyptia_moniroting
  <cloud_monitoring>
    # endpoint http://development-environment-or-production.fqdn:5000
    api_key YOUR_API_KEY
    rate 30
    pending_metrics_size 100 # Specify capacity for pending metrics
  </cloud_monitoring>
  <storage>
    @type local
    path /fluentd/log/agent_states
  </storage>
</source>
```

## Docker

Current images use fluentd v1 series and pushed into GitHub Container Registry(ghcr):

[Calyptia Fluentd | GHCR ](https://github.com/calyptia/calyptia-fluentd-docker-image/pkgs/container/fluentd)

## How to use this image

To create endpoint that collects logs on your host just run:

```bash
docker run -d -p 24224:24224 -p 24224:24224/udp -v /data:/fluentd/log fluent/fluentd:v1.3-debian-1
```

Default configurations are to:

- listen port `24224` for Fluentd forward protocol
- store logs with tag `docker.**` into `/fluentd/log/docker.*.log`
  (and symlink `docker.log`)
  - store all other logs into `/fluentd/log/data.*.log` (and symlink `data.log`)

### Providing your own configuration file and additional options

`fluentd` can be process to be appended command line arguments in the `docker run` line

For example, to provide a config, then:

```console
$ docker run -ti --rm -v /path/to/dir:/fluentd/etc -v /path/to/store/api_response:/fluentd/log/agent_state fluent/fluentd -c /fluentd/etc/<conf>
```

The first `-v` tells Docker to share '/path/to/dir' as a volume and mount it at `/fluentd/etc`.
The second `-v` tells Docker to persist '/path/to/anotherdir` as a volume and mount it at `/fluentd/log/agent_state`.

### References

[Docker Logging | fluentd.org][docker-logging-recipe]

[Fluentd logging driver - Docker Docs][docker-engine-docs]

## Issues

If you have any problems with or questions about this image, please contact us
through a [GitHub issue](https://github.com/calyptia/calyptia-fluentd-docker-image/issues).

[docker-logging-recipe]: https://www.fluentd.org/guides/recipes/docker-logging
[docker-engine-docs]: https://docs.docker.com/engine/reference/logging/fluentd
