# infrastructure

Infrastructure for IskPrinter

## Instructions

1. Deploy a Kubernetes cluster. The current Kubernetes cluster was set up manually, but if it ever needs to be recreated, then I will use Terraform.

1. Deploy an Nginx ingress controller. Refer to the instructions for your cloud provider on the [Installation Guide](https://kubernetes.github.io/ingress-nginx/deploy)
    * Example for local deployment:
    ```
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.41.2/deploy/static/provider/cloud/deploy.yaml \
        --context '<context>' \
        -n ingress-nginx
    ```
    * Example for GCP:
    ```
    kubectl create clusterrolebinding cluster-admin-binding \
        --context '<context>'
        -n ingress-nginx \
        --clusterrole cluster-admin \
        --user $(gcloud config get-value account)

    kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.41.2/deploy/static/provider/cloud/deploy.yaml \
        --context '<context>' \
        -n ingress-nginx
    ```

1. Deploy the ingress resource.
    * For local deployment, set the `host` to `localhost`.
        ```
        helm install ingress-iskprinter-master ./helm \
            --kube-context '<context>' \
            -n ingress-nginx \
            --set host='localhost'
        ```
    * For cloud deployment, also set the TLS crt and key files.
        ```
        helm install ingress-iskprinter-master ./helm \
            --kube-context '<context>' \
            -n ingress-nginx \
            --set host='iskprinter.com' \
            --set-file crtChainFile='<path-to-cert-chain-file>' \
            --set-file keyFile='<path-to-key-file>'
        ```
