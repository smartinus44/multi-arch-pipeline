# Multiarch pipeline with OpenShift pipeline

> Le cluster OpensShift utilisé contient des workers en arm64 et amd64, dans la première solution on utilise que les workers en amd64 avec une émulation QEMU opéré via un daemonset.
>
> Le fichier [nodeselector.yaml](./nodeselector.yaml) contient la directive qui permet d'aiguiller vers une node dans une architecture spécifique.

## Solution 1: avec la émulation QEMU.

### Creer les ressources

```
oc apply -f daemonset.yaml && \    
oc apply -f pvc.yaml && \    
oc apply -f multiarch-buildah.yaml && \    
oc apply -f pipeline.yaml    
```

### Lancer le pipeline avec les paramètres par défault.

```
tkn pipeline start buildah-multiarch \
    --namespace=test \
    --pod-template [nodeselector.yaml](./nodeselector.yaml) \
    --serviceaccount=pipeline \
    --use-param-defaults \
    --workspace name=scratch,claimName=source-pvc,subPath=src \
    --showlog
```

### Lancer le pipeline avec des paramètres spécifiques.

```
tkn pipeline start buildah-multiarch \
    --namespace=test \
    --pod-template nodeselector.yaml \
    --serviceaccount=pipeline \
    --param buildahPlatforms=linux/x86_64,linux/arm64/v8 \
    --param gitRepositoryURL=https://github.com/smartinus44/buildah-multiarchitecture-build.git \
    --param outputContainerImage=quay.io/rh_ee_symartin/multiarch_test/code-with-quarkus \
    --param containerfilepath=Containerfile \
    --workspace name=scratch,claimName=source-pvc,subPath=src \
    --showlog
```

## Solution 2: sans la émulation QEMU.

> Dans cette seconde solution on utilise les workers en amd64 et en arm64.
>
> WIP

