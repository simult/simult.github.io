## Simult

Free and open source smart load balancer.

Simult is secure and smart application load balancer. It can distribute incoming requests to the backend servers. There
are 3 different distribution mode that you can use which are Roundrobin, Leastconn and Affinitykey.

It has designed to used with prometheus hence exports rich metrics.

### Configuration

Simult can be configured with a yaml file and the new configuration can be applied on the fly. An example configuration yaml:

```markdown
defaults:
  tlsparams:
    certpath: ssl/
    keypath: ssl/

frontends:
  main:
    timeout: 305s
    keepalivetimeout: 30s
    listeners:
      - address: 0.0.0.0:80
      - address: 0.0.0.0:443
        tls: yes
    routes:
      - host: empty.site.io
        backend: empty

      - host: simult.server.io
        path: "/category/brand/*"
        backend: shop
        backup: empty
      - host: simult.server.io
        backend: shop
        backup: empty

      - host: test.simult.io
        path: "/foo/bar/*"
        backend: test

backends:
  empty:
    timeout: 10s
    healthcheck: empty
    servers:
      - "http://<ip1>"
      - "http://<ip2>"

  shop:
    servermaxconn: 100
    servermaxidleconn: 100
    timeout: 10s
    reqheaders:
      Host: simult.server.io
    serverhashsecret: somehash
    healthcheck: shop
    mode: affinitykey
    affinitykey:
      source: "httpheader: a-custom-header"
      maxservers: 4
      threshold: 2
    servers:
      - "http://<someip1>"
      - "http://<someip2>"
      - "http://<someip3>"
      - "http://<someip4>"
      - "http://<someip5>"

  test:
    timeout: 10s
    reqheaders:
      Host: test.simult.io
    healthcheck: test
    servers:
      - "http://<testip1>"
  
healthchecks:
  empty:
    http:
      path: /health
      host: empty.site.io
      interval: 1s
      timeout: 1s
      fall: 3
      rise: 2

  shop:
    http:
      path: /foo/health
      host: simult.server.io
      interval: 1s
      timeout: 1s
      fall: 3
      rise: 2
      resp: '{"status":"UP"}'

  test:
    http:
      path: /foo/health
      host: test.simult.io
      interval: 1s
      timeout: 1s
      fall: 3
      rise: 2
      resp: '{"status":"UP"}'

```


### More Information

You can find installation and configuration details on [simult](https://github.com/simult/simult). 

