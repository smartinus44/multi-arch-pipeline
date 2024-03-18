# Multiarch pipeline with OpenShift pipeline

Le fichier nodeselector contient la directive qui permet d'aiguiller vers une node dans une architecture spécifique.

### Creer les ressources

```
oc apply -f pvc.yaml && \    
oc apply -f multiarch-buildah.yaml && \    
oc apply -f pipeline.yaml && \    
oc apply -f nodeselector.yaml && \    
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
