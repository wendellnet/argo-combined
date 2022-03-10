# [Automation of Everything - How To Combine Argo Events, Workflows & Pipelines, CD, and Rollouts](https://youtu.be/XNXJtxkUKeY)

1 - instalar o seale cli (local)
    https://github.com/bitnami-labs/sealed-secrets/releases

1.1 - instalar no cluster
    kubectl apply --filename https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.17.3/controller.yaml

Criando um manifesto seleado    
    kubectl --namespace default create secret generic mysecret --dry-run=client --from-literal foo=bar --output json
        echo 'YmFy' | base64 -d   (senha foi apenas convertida para base 64)

    kubectl --namespace default create secret generic mysecret --dry-run=client --from-literal foo=bar --output json | kubeseal | tee mysecret.yaml

    kubectl create --filename mysecret.yaml

Verificando a chave criada
    kubectl get secret mysecret --output yamlkube 
    kubectl get secret mysecret --output jsonpath="{.data.foo}"
    kubectl get secret mysecret --output jsonpath="{.data.foo}" | base64 -d


Artigo complementar sobre sealed-secrets(https://medium.com/codex/sealed-secrets-for-kubernetes-722d643eb658)

2 - Argo Combined Events, Workflows & Pipeline , CD, and Rollouts

    kustomize build argo-cd/overlays/production | kubectl apply --filename -

    kubectl --namespace argocd rollout status deployment argocd-server

        Pegando password do argocd, logando e trocando a senha
            export PASS = $(kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d)
            argocd login --insecure --username admin --password $PASS --grpc-web argo-cd.$BASE_HOST
            argocd account update-password --current-password $PASS --new-password admin

    publicando localmente o ambiente do argocd
        kubectl port-forward service/argocd-server 8080:80 -n argocd
    

    kubectl apply --filename project.yaml
