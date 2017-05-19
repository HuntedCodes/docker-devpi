This Docker project creates a devpi-server instance, proxied behind Nginx, to
serve as a network PyPI cache.

devpi-server indexes PyPI and will retain a local copy of any packages
obtained.

Nginx protects devpi-server by blocking access to authentication and
replication URLs. The proxy can be customized by modifying
`nginx/sites-enabled/devpi`.

For more information on this setup, read the related blog post,
"[Python Package Caching with devpi-server](https://hunted.codes/devpi-server-python-cache.html)."


## Usage

### Server

The only thing to do on the server side is run `docker-compose up`. The
container listens for requests on port 80.


### Client

To have pip use the cache, add this to your `~/.pip/pip.conf` or
`/etc/pip.conf` file:

```
# /etc/pip.conf

[global]
index-url = http://<container IP>:<container port>/root/pypi/+simple/
trusted-host = <container IP>


[search]
index = https://pypi.python.org/pypi

# Uncomment this if you're behind a restrictive proxy.
#index = http://<container IP>:<container port>
```

For good measure, configure easy_install to use the cache too. This needs to be
done for each version of Python on the system.

- `/usr/lib/python2.7/distutils/distutils.cfg`
- `/usr/lib/python3.4/distutils/distutils.cfg`

```
# /usr/lib/python*.*/distutils/distutils.cfg

[easy_install]
index_url = http://<container IP>:<container port>/root/pypi/+simple/
```
