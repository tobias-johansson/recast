# Recast

Convert between textual data formats

## Example

### Original
```
~/P/p/recast $ cat example.yml
---
web:
  build: web
  command: python app.py
  ports:
  - "5000:5000"
  volumes:
  - web:/code
  links:
  - redis
redis:
  image: redis
datadog:
  build: datadog
  links:
  - redis
  environment:
  - API_KEY=__your_datadog_api_key_here__
  volumes:
  - /var/run/docker.sock:/var/run/docker.sock
  - /proc/mounts:/host/proc/mounts:ro
  - /sys/fs/cgroup:/host/sys/fs/cgroup:ro
  command: >
    env
      foo=/etc/init.d/cron
    bash -exc "
      ls $$foo
      bar() { sed -i s/$${1}=.*/$${1}=$${2}/g $$foo; }
      bar something another
    "
```

### yaml2json
```
~/P/p/recast $ cat example.yml | ./yaml2json > example.json
~/P/p/recast $ cat example.json
{
    "web": {
        "volumes": [
            "web:/code"
        ],
        "command": "python app.py",
        "build": "web",
        "links": [
            "redis"
        ],
        "ports": [
            "5000:5000"
        ]
    },
    "redis": {
        "image": "redis"
    },
    "datadog": {
        "environment": [
            "API_KEY=__your_datadog_api_key_here__"
        ],
        "links": [
            "redis"
        ],
        "command": "env\n  foo=/etc/init.d/cron\nbash -exc \"\n  ls $$foo\n  bar() { sed -i s/$${1}=.*/$${1}=$${2}/g $$foo; }\n  bar something another\n\"\n",
        "build": "datadog",
        "volumes": [
            "/var/run/docker.sock:/var/run/docker.sock",
            "/proc/mounts:/host/proc/mounts:ro",
            "/sys/fs/cgroup:/host/sys/fs/cgroup:ro"
        ]
    }
}
```

### json2yaml
```
~/P/p/recast $ cat example.json | ./json2yaml
---
datadog:
  build: datadog
  command: >
    env
      foo=/etc/init.d/cron
    bash -exc "
      ls $$foo
      bar() { sed -i s/$${1}=.*/$${1}=$${2}/g $$foo; }
      bar something another
    "
  environment:
  - API_KEY=__your_datadog_api_key_here__
  links:
  - redis
  volumes:
  - /var/run/docker.sock:/var/run/docker.sock
  - /proc/mounts:/host/proc/mounts:ro
  - /sys/fs/cgroup:/host/sys/fs/cgroup:ro
redis:
  image: redis
web:
  build: web
  command: python app.py
  links:
  - redis
  ports:
  - 5000:5000
  volumes:
  - web:/code
```
