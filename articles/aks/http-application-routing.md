---
title: Doplněk pro směrování aplikace HTTP ve službě Azure Kubernetes (AKS)
description: Doplněk pro směrování aplikací HTTP použijte k přístupu k aplikacím nasazeným ve službě Azure Kubernetes (AKS).
services: container-service
author: lachie83
ms.topic: article
ms.date: 07/20/2020
ms.author: laevenso
ms.openlocfilehash: 08835bda959fb4fe261e86e4d519ab85bd2a4625
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87495144"
---
# <a name="http-application-routing"></a>Směrování aplikace HTTP

Řešení směrování aplikace HTTP usnadňuje přístup k aplikacím, které jsou nasazeny do vašeho clusteru Azure Kubernetes Service (AKS). Když je řešení povolené, nakonfiguruje v clusteru AKS [adaptér](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) příchozího přenosu dat. Při nasazení aplikací řešení také vytvoří veřejně přístupné názvy DNS pro koncové body aplikací.

Když je doplněk povolený, vytvoří v předplatném zónu DNS. Další informace o cenách služby DNS najdete v tématu [ceny služby DNS][dns-pricing].

> [!CAUTION]
> Doplněk pro směrování aplikací HTTP je navržený tak, aby vám umožnil rychle vytvořit kontroler příchozího přenosu dat a přistupovat k aplikacím. Tento doplněk není aktuálně navržený pro použití v produkčním prostředí a nedoporučuje se pro produkční použití. Pro nasazení příchozích dat do provozu, která zahrnují víc replik a podporu TLS, najdete informace v tématu [vytvoření řadiče HTTPS pro příchozí](./ingress-tls.md)přenosy.

## <a name="http-routing-solution-overview"></a>Přehled řešení směrování HTTP

Doplněk nasazuje dvě součásti: [kontroler][ingress] příchozího přenosu dat Kubernetes a externí řadič [DNS][external-dns] .

- **Adaptér**příchozího přenosu dat: kontroler příchozího přenosu dat je přístupný pro Internet pomocí služby Kubernetes typu Vyrovnávání zatížení. Kontroler příchozího přenosu dat sleduje a implementuje [Kubernetes příchozí prostředky][ingress-resource], které vytváří trasy k koncovým bodům aplikace.
- **Externí kontroler DNS**: sleduje Kubernetes prostředky příchozího přenosu dat a vytváří záznamy DNS v zóně DNS specifické pro clustery.

## <a name="deploy-http-routing-cli"></a>Nasazení směrování HTTP: rozhraní příkazového řádku

Doplněk směrování aplikace HTTP může být při nasazení clusteru AKS povolen s rozhraním příkazového řádku Azure CLI. K tomu použijte příkaz [AZ AKS Create][az-aks-create] s `--enable-addons` argumentem.

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-addons http_application_routing
```

> [!TIP]
> Pokud chcete povolit vícenásobné doplňky, poskytněte je jako seznam oddělený čárkami. Pokud chcete například povolit směrování a monitorování aplikací HTTP, použijte formát `--enable-addons http_application_routing,monitoring` .

V existujícím clusteru AKS můžete taky povolit směrování HTTP pomocí příkazu [AZ AKS Enable-addons][az-aks-enable-addons] . Pokud chcete povolit směrování protokolu HTTP v existujícím clusteru, přidejte `--addons` parametr a zadejte *http_application_routing* , jak je znázorněno v následujícím příkladu:

```azurecli
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing
```

Po nasazení nebo aktualizaci clusteru použijte příkaz [AZ AKS show][az-aks-show] , který načte název zóny DNS.

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName -o table
```

Tento název je nutný k nasazení aplikací do clusteru AKS a je zobrazený v následujícím příkladu výstupu:

```console
9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io
```

## <a name="deploy-http-routing-portal"></a>Nasazení směrování HTTP: portál

Doplněk směrování aplikace HTTP lze povolit prostřednictvím Azure Portal při nasazování clusteru AKS.

![Povolení funkce směrování protokolu HTTP](media/http-routing/create.png)

Po nasazení clusteru přejděte do automaticky vytvořené skupiny prostředků AKS a vyberte zónu DNS. Poznamenejte si název zóny DNS. Tento název je nutný k nasazení aplikací do clusteru AKS.

![Získat název zóny DNS](media/http-routing/dns.png)

## <a name="connect-to-your-aks-cluster"></a>Připojení ke clusteru AKS

Pokud se chcete připojit ke clusteru Kubernetes z místního počítače, použijte klienta příkazového řádku Kubernetes [kubectl][kubectl].

Pokud používáte Azure Cloud Shell, `kubectl` je už nainstalovaný. Můžete ho také nainstalovat místně, a to pomocí příkazu [az aks install-cli][]:

```azurecli
az aks install-cli
```

Pomocí příkazu [az aks get-credentials][] nakonfigurujte klienta `kubectl` pro připojení k vašemu clusteru Kubernetes. Následující příklad vrátí pověření pro cluster AKS s názvem *MyAKSCluster* v *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKSCluster
```

## <a name="use-http-routing"></a>Použití směrování protokolu HTTP

Řešení směrování aplikace HTTP se dá aktivovat jenom u prostředků příchozího přenosu dat, které jsou poznámy takto:

```yaml
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

Vytvořte soubor s názvem **Samples-http-Application-Routing. yaml** a ZKOPÍRUJTE následující YAML. Na řádku 43 aktualizujte `<CLUSTER_SPECIFIC_DNS_ZONE>` pomocí názvu zóny DNS shromážděného v předchozím kroku tohoto článku.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld  
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: neilpeterson/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld  
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: aks-helloworld
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: aks-helloworld.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /
```

K vytvoření prostředků použijte příkaz [kubectl Apply][kubectl-apply] .

```bash
kubectl apply -f samples-http-application-routing.yaml
```

Následující příklad ukazuje vytvořené prostředky:

```bash
$ kubectl apply -f samples-http-application-routing.yaml

deployment.apps/aks-helloworld created
service/aks-helloworld created
ingress.networking.k8s.io/aks-helloworld created
```

Otevřete webový prohlížeč a *Akste Hello. \<CLUSTER_SPECIFIC_DNS_ZONE\> *například *AKS-HelloWorld.9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.IO* a ověřte, že vidíte ukázkovou aplikaci. Zobrazení aplikace může trvat několik minut.

## <a name="remove-http-routing"></a>Odebrat směrování protokolu HTTP

Řešení směrování HTTP se dá odebrat pomocí Azure CLI. Uděláte to tak, že spustíte následující příkaz, kde nahradíte svůj cluster AKS a název skupiny prostředků.

```azurecli
az aks disable-addons --addons http_application_routing --name myAKSCluster --resource-group myResourceGroup --no-wait
```

Když je doplněk pro směrování aplikace HTTP zakázaný, některé prostředky Kubernetes můžou v clusteru zůstat. Tyto prostředky zahrnují *configMaps* a *tajné klíče*a vytvářejí se v oboru názvů *Kube-System* . Pokud chcete zachovat čistý cluster, možná budete chtít tyto prostředky odebrat.

Vyhledejte prostředky *addon-http-Application-Routing* pomocí následujících příkazů [Get kubectl][kubectl-get] :

```console
kubectl get deployments --namespace kube-system
kubectl get services --namespace kube-system
kubectl get configmaps --namespace kube-system
kubectl get secrets --namespace kube-system
```

Následující příklad výstupu ukazuje configMaps, které by se měly odstranit:

```
$ kubectl get configmaps --namespace kube-system

NAMESPACE     NAME                                                       DATA   AGE
kube-system   addon-http-application-routing-nginx-configuration         0      9m7s
kube-system   addon-http-application-routing-tcp-services                0      9m7s
kube-system   addon-http-application-routing-udp-services                0      9m7s
```

K odstranění prostředků použijte příkaz [kubectl Delete][kubectl-delete] . Zadejte typ prostředku, název prostředku a obor názvů. Následující příklad odstraní jednu z předchozích configmaps:

```console
kubectl delete configmaps addon-http-application-routing-nginx-configuration --namespace kube-system
```

Opakujte předchozí `kubectl delete` krok pro všechny prostředky funkce *addon-http-Application-Routing* , které zůstaly ve vašem clusteru.

## <a name="troubleshoot"></a>Řešení potíží

Pomocí příkazu [kubectl logs][kubectl-logs] Zobrazte protokoly aplikací pro externí aplikaci DNS. Protokoly by měly potvrdit, že byl úspěšně vytvořen záznam DNS a a TXT.

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'aks-helloworld' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'aks-helloworld' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

Tyto záznamy se taky můžou zobrazit na prostředku zóny DNS v Azure Portal.

![Získat záznamy DNS](media/http-routing/clippy.png)

Pomocí příkazu [kubectl logs][kubectl-logs] Zobrazte protokoly aplikací pro kontroler Nginx pro příchozí přenosy. Protokoly by měly potvrdit `CREATE` prostředek příchozího přenosu dat a znovu načíst řadič. Všechny aktivity HTTP jsou protokolovány.

```bash
$ kubectl logs -f deploy/addon-http-application-routing-nginx-ingress-controller -n kube-system

-------------------------------------------------------------------------------
NGINX Ingress controller
  Release:    0.13.0
  Build:      git-4bc943a
  Repository: https://github.com/kubernetes/ingress-nginx
-------------------------------------------------------------------------------

I0426 20:30:12.212936       9 flags.go:162] Watching for ingress class: addon-http-application-routing
W0426 20:30:12.213041       9 flags.go:165] only Ingress with class "addon-http-application-routing" will be processed by this ingress controller
W0426 20:30:12.213505       9 client_config.go:533] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0426 20:30:12.213752       9 main.go:181] Creating API client for https://10.0.0.1:443
I0426 20:30:12.287928       9 main.go:225] Running in Kubernetes Cluster version v1.8 (v1.8.11) - git (clean) commit 1df6a8381669a6c753f79cb31ca2e3d57ee7c8a3 - platform linux/amd64
I0426 20:30:12.290988       9 main.go:84] validated kube-system/addon-http-application-routing-default-http-backend as the default backend
I0426 20:30:12.294314       9 main.go:105] service kube-system/addon-http-application-routing-nginx-ingress validated as source of Ingress status
I0426 20:30:12.426443       9 stat_collector.go:77] starting new nginx stats collector for Ingress controller running in namespace  (class addon-http-application-routing)
I0426 20:30:12.426509       9 stat_collector.go:78] collector extracting information from port 18080
I0426 20:30:12.448779       9 nginx.go:281] starting Ingress controller
I0426 20:30:12.463585       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-nginx-configuration", UID:"2588536c-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"559", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-nginx-configuration
I0426 20:30:12.466945       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-tcp-services", UID:"258ca065-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"561", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-tcp-services
I0426 20:30:12.467053       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-udp-services", UID:"259023bc-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"562", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-udp-services
I0426 20:30:13.649195       9 nginx.go:302] starting NGINX process...
I0426 20:30:13.649347       9 leaderelection.go:175] attempting to acquire leader lease  kube-system/ingress-controller-leader-addon-http-application-routing...
I0426 20:30:13.649776       9 controller.go:170] backend reload required
I0426 20:30:13.649800       9 stat_collector.go:34] changing prometheus collector from  to default
I0426 20:30:13.662191       9 leaderelection.go:184] successfully acquired lease kube-system/ingress-controller-leader-addon-http-application-routing
I0426 20:30:13.662292       9 status.go:196] new leader elected: addon-http-application-routing-nginx-ingress-controller-5cxntd6
I0426 20:30:13.763362       9 controller.go:179] ingress backend successfully reloaded...
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"aks-helloworld", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/aks-helloworld
W0426 21:51:57.908771       9 controller.go:775] service default/aks-helloworld does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-aks-helloworld-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="clean-up"></a>Vyčištění

Odeberte přidružené objekty Kubernetes vytvořené v tomto článku pomocí `kubectl delete` .

```bash
kubectl delete -f samples-http-application-routing.yaml
```

Ukázkový výstup zobrazuje objekty Kubernetes, které byly odebrány.

```bash
$ kubectl delete -f samples-http-application-routing.yaml

deployment "aks-helloworld" deleted
service "aks-helloworld" deleted
ingress "aks-helloworld" deleted
```

## <a name="next-steps"></a>Další kroky

Informace o tom, jak nainstalovat řadič příchozího přenosu HTTPS v AKS, najdete v tématu https informing [on Azure Kubernetes Service (AKS)][ingress-https].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[ingress-https]: ./ingress-tls.md
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials

<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource
