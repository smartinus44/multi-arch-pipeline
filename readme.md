# Multiarch pipeline with OpenShift pipeline

> Le cluster OpensShift utilisé contient des workers en arm64 et amd64, dans la première solution on utilise que les workers en amd64 avec une émulation QEMU opéré via un daemonset.
>
> Le fichier [nodeselector.yaml](./solution1/nodeselector.yaml) contient la directive qui permet d'aiguiller vers une node dans une architecture spécifique.

## Contraintes

[] On utilise buildah
[] ...


## Solution 1: avec l'émulation QEMU.

![Schéma de la solution 1][./Solution1.jpg]

### Nécessite d'avoir les privilèges suffisants.

Vérifier au préalable les contraintes de sécurité de votre cluster:

```
oc get scc
```

Vous pouvez créer votre propre compte de service et lui appliquer les bonnes contraintes de sécurités pour faire une élévation de privilège:

```
oc adm policy add-scc-to-user privileged -z  <<your_sa>> -n <<your_project>>
```

### Creer les ressources

```
oc apply -f ./solution1/daemonset.yaml && \    
oc apply -f ./solution1/pvc.yaml && \    
oc apply -f ./solution1/multiarch-buildah.yaml && \    
oc apply -f ./solution1/pipeline.yaml    
```

### Lancer le pipeline avec les paramètres par défault.

```
tkn pipeline start buildah-multiarch \
    --namespace=test \
    --pod-template [nodeselector.yaml](./solution1/nodeselector.yaml) \
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

## Solution 2: sans l'émulation QEMU.

> Dans cette seconde solution on utilise les workers en amd64 et en arm64.
>
> WIP


```
oc apply -f ./solution2/pvc-arm64.yaml && \
oc apply -f ./solution2/pvc-amd64.yaml
```

```
tkn pipeline start multiarch-without-emulation \
    --namespace=test \
    --pod-template ./solution2/nodeselector-arm64.yaml \
    --workspace name=scratch,claimName=source-pvc-arm64 \
    --serviceaccount=pipeline \
    --use-param-defaults \
    --param platarch=linux/arm64 \
    --showlog
```

```
tkn pipeline start multiarch-without-emulation \
    --namespace=test \
    --pod-template ./solution2/nodeselector-amd64.yaml \
    --workspace name=scratch,claimName=source-pvc-amd64 \
    --serviceaccount=pipeline \
    --use-param-defaults \
    --param platarch=linux/amd64 \
    --showlog
```
