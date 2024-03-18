

Source repo:

https://github.com/smartinus44/devfile-sample-code-with-quarkus/tree/main




oc apply -f pvc.yaml 

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



tkn pipeline start buildah-multiarch \
    --namespace=test \
    --pod-template nodeselector.yaml \
    --serviceaccount=pipeline \
    --use-param-defaults \
    --workspace name=scratch,claimName=source-pvc,subPath=src \
    --showlog
