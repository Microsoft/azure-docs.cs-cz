---
title: Doplněk směrování aplikací HTTP ve službě Azure Kubernetes Service (AKS)
description: Pomocí doplňku směrování aplikací HTTP pro přístup k aplikacím nasazeným ve službě Azure Kubernetes Service (AKS).
services: container-service
author: lachie83
ms.topic: article
ms.date: 08/06/2019
ms.author: laevenso
ms.openlocfilehash: 6ffc9daaf1b87fc9fb6ebbb0f2787f07282afe5e
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632407"
---
# <a name="http-application-routing"></a>Směrování aplikace HTTP

Řešení směrování aplikací HTTP usnadňuje přístup k aplikacím, které jsou nasazené do clusteru Služby Azure Kubernetes (AKS). Pokud je řešení povoleno, konfiguruje [řadič příchozího přenosu dat](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) v clusteru AKS. Při nasazování aplikací řešení také vytváří veřejně přístupné názvy DNS pro koncové body aplikace.

Pokud je doplněk povolen, vytvoří ve vašem předplatném zónu DNS. Další informace o nákladech na službu DNS naleznete v [tématu ceny DNS][dns-pricing].

> [!CAUTION]
> Doplněk směrování aplikací HTTP je navržen tak, aby vám umožní rychle vytvořit řadič příchozího přenosu dat a přistupovat k vašim aplikacím. Tento doplněk se nedoporučuje pro produkční použití. Pro nasazení příchozích dat připravených pro produkční prostředí, která obsahují více replik a podporu TLS, najdete [v tématu Vytvoření řadiče příchozího přenosu dat https](https://docs.microsoft.com/azure/aks/ingress-tls).

## <a name="http-routing-solution-overview"></a>Přehled řešení směrování HTTP

Doplněk nasazuje dvě součásti: [řadič příchozího přenosu dat Kubernetes][ingress] a řadič [externího DNS.][external-dns]

- **Řadič příchozího přenosu dat**: Řadič příchozího přenosu dat je vystaven internetu pomocí služby Kubernetes typu LoadBalancer. Ingress řadič sleduje a implementuje [prostředky Příchozí přenos dat][ingress-resource], který vytváří trasy do koncových bodů aplikace.
- **Řadič External-DNS**: Sleduje prostředky příchozího přenosu dat Kubernetes a vytváří záznamy DNS A v zóně DNS specifické pro cluster.

## <a name="deploy-http-routing-cli"></a>Nasazení směrování HTTP: CLI

Doplněk směrování aplikací HTTP lze povolit pomocí rozhraní příkazového příkazu Azure při nasazování clusteru AKS. Chcete-li tak učinit, použijte příkaz [az aks create][az-aks-create] s argumentem. `--enable-addons`

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-addons http_application_routing
```

> [!TIP]
> Pokud chcete povolit více doplňků, zadejte je jako seznam oddělený čárkami. Chcete-li například povolit směrování a `--enable-addons http_application_routing,monitoring`monitorování aplikací HTTP, použijte formát .

Směrování HTTP můžete také povolit v existujícím clusteru AKS pomocí příkazu [az aks enable-addons.][az-aks-enable-addons] Chcete-li povolit směrování HTTP `--addons` v existujícím clusteru, přidejte parametr a zadejte *http_application_routing,* jak je znázorněno v následujícím příkladu:

```azurecli
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing
```

Po nasazení nebo aktualizaci clusteru načtěte název zóny DNS pomocí příkazu [az aks show.][az-aks-show] Tento název je nutný k nasazení aplikací do clusteru AKS.

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName -o table
```

Výsledek

9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io


## <a name="deploy-http-routing-portal"></a>Nasazení směrování HTTP: Portál

Doplněk směrování aplikací HTTP lze povolit prostřednictvím portálu Azure při nasazování clusteru AKS.

![Povolení funkce směrování HTTP](media/http-routing/create.png)

Po nasazení clusteru vyhledejte automaticky vytvořenou skupinu prostředků AKS a vyberte zónu DNS. Poznamenejte si název zóny DNS. Tento název je nutný k nasazení aplikací do clusteru AKS.

![Získání názvu zóny DNS](media/http-routing/dns.png)

## <a name="use-http-routing"></a>Použití směrování HTTP

Řešení směrování aplikace HTTP může být spuštěno pouze u prostředků příchozího přenosu dat, které jsou anotovány následujícím způsobem:

```yaml
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

Vytvořte soubor s názvem **samples-http-application-routing.yaml** a zkopírujte v následujícím yaml. Na řádku 43 `<CLUSTER_SPECIFIC_DNS_ZONE>` aktualizujte názvem zóny DNS shromážděným v předchozím kroku tohoto článku.


```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: party-clippy
spec:
  template:
    metadata:
      labels:
        app: party-clippy
    spec:
      containers:
      - image: r.j3ss.co/party-clippy
        name: party-clippy
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        tty: true
        command: ["party-clippy"]
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: party-clippy
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8080
  selector:
    app: party-clippy
  type: ClusterIP
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: party-clippy
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: party-clippy.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: party-clippy
          servicePort: 80
        path: /
```

K vytvoření prostředků použijte příkaz [kubectl apply.][kubectl-apply]

```bash
$ kubectl apply -f samples-http-application-routing.yaml

deployment "party-clippy" created
service "party-clippy" created
ingress "party-clippy" created
```

Pomocí cURL nebo prohlížeče přejděte na název hostitele zadaný v hostitelské části souboru samples-http-application-routing.yaml. Aplikace může trvat až jednu minutu, než bude k dispozici prostřednictvím internetu.

```bash
$ curl party-clippy.471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io

 _________________________________
/ It looks like you're building a \
\ microservice.                   /
 ---------------------------------
 \
  \
     __
    /  \
    |  |
    @  @
    |  |
    || |/
    || ||
    |\_/|
    \___/

```

## <a name="remove-http-routing"></a>Odebrání směrování HTTP

Řešení směrování HTTP lze odebrat pomocí příkazového příkazu k řešení Azure. Chcete-li tak učinit, spusťte následující příkaz a naponořte název clusteru AKS a skupiny prostředků.

```azurecli
az aks disable-addons --addons http_application_routing --name myAKSCluster --resource-group myResourceGroup --no-wait
```

Pokud je doplněk směrování aplikace HTTP zakázán, mohou některé prostředky Kubernetes zůstat v clusteru. Tyto prostředky zahrnují *configMaps* a *tajné klíče*a jsou vytvořeny v oboru názvů *kube systému.* Chcete-li udržovat čistý cluster, můžete tyto prostředky odebrat.

Vyhledejte *addon-http-application-routing* prostředky pomocí následujících [příkazů kubectl get:][kubectl-get]

```console
kubectl get deployments --namespace kube-system
kubectl get services --namespace kube-system
kubectl get configmaps --namespace kube-system
kubectl get secrets --namespace kube-system
```

Následující ukázkový výstup ukazuje configMaps, které by měly být odstraněny:

```
$ kubectl get configmaps --namespace kube-system

NAMESPACE     NAME                                                       DATA   AGE
kube-system   addon-http-application-routing-nginx-configuration         0      9m7s
kube-system   addon-http-application-routing-tcp-services                0      9m7s
kube-system   addon-http-application-routing-udp-services                0      9m7s
```

Chcete-li odstranit prostředky, použijte příkaz [kubectl delete.][kubectl-delete] Zadejte typ prostředku, název prostředku a obor názvů. Následující příklad odstraní jeden z předchozích configmaps:

```console
kubectl delete configmaps addon-http-application-routing-nginx-configuration --namespace kube-system
```

Opakujte `kubectl delete` předchozí krok pro všechny prostředky *směrování addon-http-application,které* zůstaly v clusteru.

## <a name="troubleshoot"></a>Řešení potíží

Pomocí příkazu [kubectl logs][kubectl-logs] můžete zobrazit protokoly aplikací pro aplikaci External-DNS. Protokoly by měly potvrdit, že záznam DNS A a TXT byl úspěšně vytvořen.

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'party-clippy' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'party-clippy' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

Tyto záznamy se taky můžou vidět na prostředku zóny DNS na webu Azure Portal.

![Získání záznamů DNS](media/http-routing/clippy.png)

Pomocí příkazu [kubectl logs][kubectl-logs] zobrazte protokoly aplikací pro řadič přenosu dat Nginx. Protokoly by měly potvrdit prostředek `CREATE` příchozího přenosu dat a opětovné načtení řadiče. Všechny aktivity PROTOKOLU HTTP je zaznamenána.

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
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"party-clippy", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/party-clippy
W0426 21:51:57.908771       9 controller.go:775] service default/party-clippy does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-party-clippy-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="clean-up"></a>Vyčištění

Odeberte přidružené objekty Kubernetes vytvořené v tomto článku.

```bash
$ kubectl delete -f samples-http-application-routing.yaml

deployment "party-clippy" deleted
service "party-clippy" deleted
ingress "party-clippy" deleted
```

## <a name="next-steps"></a>Další kroky

Informace o tom, jak nainstalovat řadič Ingress zabezpečený protokolem HTTPS v AKS, najdete [v tématu Ingress protokolu HTTPS ve službě Azure Kubernetes Service (AKS).][ingress-https]

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[ingress-https]: ./ingress-tls.md
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons


<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource
