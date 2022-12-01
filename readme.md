recreate: terminate the old version and release the new one

ramped: release a new version on a rolling update fashion, one after the other

blue/green: release a new version alongside the old version then switch traffic

canary: release a new version to a subset of users, then proceed to a full rollout

a/b testing: release a new version to a subset of users in a precise way (HTTP headers, cookie, weight, etc.). A/B testing is really a technique for making business decisions based on statistics but we will briefly describe the process. This doesn’t come out of the box with Kubernetes, it implies extra work to setup a more advanced infrastructure (Istio, Linkerd, Traefik, custom nginx/haproxy, etc).
