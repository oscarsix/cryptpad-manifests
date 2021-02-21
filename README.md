# cryptpad-manifests
[Cryptpad](https://cryptpad.fr/) server deployment based on kustomization manifests by using [promasu/cryptpad](https://hub.docker.com/r/promasu/cryptpad) docker image.

## Usage

Create kustomization.yaml file
```
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

configMapGenerator:
  - name: cryptpad-envs
    behavior: merge
    literals:
      - CPAD_MAIN_DOMAIN=cryptpad.tld
      - CPAD_SANDBOX_DOMAIN=cryptpad.tld

namespace: cryptpad

patchesJson6902:
  - target:
      group: networking.k8s.io
      version: v1
      kind: Ingress
      name: cryptpad
    patch: |-
      - op: replace
        path: /spec/rules/0/host
        value: cryptpad.tld
  - target:
      version: v1
      kind: PersistentVolumeClaim
      name: cryptpad-data
    patch: |-
      - op: replace
        path: /spec/storageClassName
        value: nfs-red01

resources:
  - https://github.com/oscarsix/cryptpad-manifests?ref=v4.1.0-nginx-alpine-1
```

Deploy with `kubectl apply -k .`
