
wstepne przygotowanie NS, oznaczenie dla istio aby była auto-injection i dla wygody przejscie na ten NS dla nastepnych komend:


```
kubectl create namespace slawek
kubectl label namespace slawek istio-injection=enabled --overwrite
kubectl config set-context --current --namespace=slawek
```

uwaga , od ASM 1.8 obowiązuje nieco inna metoda labelowania na auto-injection:
```
kubectl label namespace slawek istio-injection- istio.io/rev=asm-182-2 --overwrite
REV bierzemy z kubectl -n istio-system get pods -l app=istiod --show-labels (np rev=asm-182-2)
```

uwaga! w przypadku private GKE mozna (a wrecz wstepnie trzeba) zmienic w Deployach image: gimboo/nginx_nonroot na image: gcr.io/sl-gke-10/nginx_nonroot
wczesniej oczywiscie je zrobic via :  
```
docker pull ; docker tag gimboo/nginx_nonroot gcr.io/sl-gke-10/nginx_nonroot docker push  gcr.io/sl-gke-10/nginx_nonroot
```

bazujemy na :
```
https://istio.io/latest/docs/tasks/security/authorization/authz-custom/
```

majac na uwadze: 

```
kk edit cm istio-asm-1106-2 -n istio-system

[...]
data:
  mesh: |-
    extensionProviders:
    - name: "sample-ext-authz-grpc"
      envoyExtAuthzGrpc:
        service: "ext-authz.foo.svc.cluster.local"
        port: "9000"
    - name: "sample-ext-authz-http"
      envoyExtAuthzHttp:
        service: "ext-authz.foo.svc.cluster.local"
        port: "8000"
        includeRequestHeadersInCheck: ["x-ext-authz"]
    defaultConfig:
[...]

```
