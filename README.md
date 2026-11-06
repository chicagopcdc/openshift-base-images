# openshift-base-images

Base images for PCDC services running on OpenShift (non-root, random uid).
Pushed to ECR: `973342646972.dkr.ecr.us-east-1.amazonaws.com/openshift-pcdc/python-nginx`.

Derived from [uc-cdis/base-images](https://github.com/uc-cdis/base-images).

| Dir | Python | Source |
|---|---|---|
| `python-nginx/3.9` | 3.9 | `paulmurdoch19/base-images@98a499df` (`openshift-modifications`). Matches the current ECR `:latest` image (pushed 2025-12-18). Kept for the old fence/openshift branch. |
| `python-nginx/3.13` | 3.13 | `uc-cdis/base-images` `python3.13/python_nginx` plus the OpenShift changes below. |

## OpenShift changes relative to upstream
1. `cp /usr/sbin/nginx /usr/bin/nginx` - `dockerrun.bash` runs `/usr/bin/nginx` when `OPENSHIFT=true`.
2. `chmod -R g+rx /home/gen3` - pods run as a random uid with supplemental group 1000 (`gen3`) and must be able to run poetry.

In the helm values, nginx is configured by mounting `nginx.conf` from a ConfigMap over `/etc/nginx/nginx.conf`
(listen 8080, writable pid file), so the `nginx.conf` baked into the image is not what runs on OpenShift.

## Building
```
docker build --platform linux/amd64 -t python-nginx:3.13 python-nginx/3.13
```
To push, run the **Build and push python-nginx base image to ECR** workflow (manual). Use a new tag
(e.g. `3.13`); do not overwrite `latest` until the fence `openshift` branch is on the 3.13 layout.

When upstream `3.13-pythonnginx` changes, re-sync `python-nginx/3.13` from `python3.13/python_nginx` and reapply the two changes above.
