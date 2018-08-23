---
title: Směrování aplikace HTTP – doplněk ve službě Azure Kubernetes Service (AKS)
description: Použijte doplněk směrování aplikace HTTP ve službě Azure Kubernetes Service (AKS).
services: container-service
author: lachie83
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/25/2018
ms.author: laevenso
ms.openlocfilehash: 93b9075831db356607438288a7c153dde2f8b210
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42061308"
---
# <a name="http-application-routing"></a>Směrování aplikace HTTP

Přístup k aplikacím, které jsou nasazené na clusteru Azure Kubernetes Service (AKS) usnadňuje řešení směrování aplikace HTTP. Když toto řešení povolené, nakonfiguruje kontroler příchozího přenosu dat ve vašem clusteru AKS. Jak se aplikace nasazují, řešení také vytváří veřejně dostupné názvy DNS pro koncové body aplikace.

Pokud doplněk je povolená, vytvoří zónu DNS ve vašem předplatném. Další informace o nákladech DNS najdete v tématu [DNS ceny][dns-pricing].

## <a name="http-routing-solution-overview"></a>Přehled řešení směrování protokolu HTTP

Doplněk nasadí dvě součásti: [kontroler příchozího přenosu dat Kubernetes] [ ingress] a [externí DNS] [ external-dns] kontroleru.

- **Kontroler příchozího přenosu dat**: kontroler příchozího přenosu dat je přístupný z Internetu pomocí služby Kubernetes typu nástroj pro vyrovnávání zatížení. Kontroler příchozího přenosu dat sleduje a implementuje [příchozího přenosu dat Kubernetes prostředky][ingress-resource], který vytvoří trasy pro koncové body aplikace.
- **Externí DNS řadiče**: sleduje pro Kubernetes prostředky příchozího přenosu dat a vytvoří záznamy DNS A v zóně DNS specifické pro cluster.

## <a name="deploy-http-routing-cli"></a>Nasazení, směrování protokolu HTTP: rozhraní příkazového řádku

Pomocí Azure CLI je možné povolit doplněk směrování aplikace HTTP, při nasazování clusteru AKS. Chcete-li tak učinit, použijte [az aks vytvořit] [ az-aks-create] příkazů `--enable-addons` argument.

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-addons http_application_routing
```

Můžete také povolit směrování na existující cluster AKS pomocí protokolu HTTP [az aks enable-addons] [ az-aks-enable-addons] příkazu. Pokud chcete povolit směrování protokolu HTTP v existujícím clusteru, přidejte `--addons` parametr a zadat *http_application_routing* jak je znázorněno v následujícím příkladu:

```azurecli
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing
```

Poté, co clusteru je nasazený nebo aktualizovat, použijte [az aks zobrazit] [ az-aks-show] příkaz, který načte název zóny DNS. Tento název je potřeba k nasazení aplikací do clusteru AKS.

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName -o table

Result
-----------------------------------------------------
9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io
```

## <a name="deploy-http-routing-portal"></a>Nasazení, směrování protokolu HTTP: portál

Na webu Azure portal je možné povolit doplněk směrování aplikace HTTP, při nasazování clusteru AKS.

![Povolit funkci směrování protokolu HTTP](media/http-routing/create.png)

Po nasazení clusteru, přejděte do skupiny automaticky vytvořený prostředek AKS a vyberte zónu DNS. Poznamenejte si název zóny DNS. Tento název je potřeba k nasazení aplikací do clusteru AKS.

![Získejte název zóny DNS](media/http-routing/dns.png)

## <a name="use-http-routing"></a>Použití směrování protokolu HTTP

Řešení směrování aplikace HTTP můžou být vyvolány pouze na prostředky příchozího přenosu dat, které jsou opatřeny poznámkami následujícím způsobem:

```yaml
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

Vytvořte soubor s názvem **ukázky http aplikace routing.yaml** a zkopírujte do následující kód YAML. Na řádku 43 aktualizovat `<CLUSTER_SPECIFIC_DNS_ZONE>` se zóna DNS shromážděných v předchozím kroku tohoto článku.


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

Použití [použití kubectl] [ kubectl-apply] příkaz pro vytvoření prostředků.

```bash
$ kubectl apply -f samples-http-application-routing.yaml

deployment "party-clippy" created
service "party-clippy" created
ingress "party-clippy" created
```

Pomocí cURL nebo v prohlížeči přejděte na název hostitele definováno v sekci hostitele ukázky http aplikace routing.yaml souboru. Aplikace může trvat až jednu minutu, než je k dispozici prostřednictvím Internetu.

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

## <a name="remove-http-routing"></a>Odebrat směrování protokolu HTTP

Řešení směrování protokolu HTTP lze odebrat pomocí příkazového řádku Azure. Provedete to spuštěním následujícího příkazu, nahraďte vaše AKS clusterů a prostředků název skupiny.

```azurecli
az aks disable-addons --addons http_application_routing --name myAKSCluster --resource-group myResourceGroup --no-wait
```

## <a name="troubleshoot"></a>Řešení potíží

Použití [kubectl protokoly] [ kubectl-logs] příkazu zobrazte protokoly aplikací pro aplikaci externí DNS. Protokoly by měl potvrďte, že záznam TXT DNS a a byly úspěšně vytvořeny.

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'party-clippy' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'party-clippy' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

Tyto záznamy můžete zobrazit také na prostředku zóny DNS na webu Azure Portal.

![Získat záznamy DNS](media/http-routing/clippy.png)

Použití [kubectl protokoly] [ kubectl-logs] příkazu zobrazte protokoly aplikací pro kontroler příchozího přenosu dat serveru Nginx. Ověřte, zda protokoly `CREATE` prostředek příchozího přenosu dat a znovu načíst kontroleru. Všechny HTTP aktivita se protokoluje.

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

Odeberte přidružené Kubernetes objekty vytvořené v tomto článku.

```bash
$ kubectl delete -f samples-http-application-routing.yaml

deployment "party-clippy" deleted
service "party-clippy" deleted
ingress "party-clippy" deleted
```

## <a name="next-steps"></a>Další postup

Informace o tom, jak nainstalovat řadič protokol HTTPS zabezpečená příchozího přenosu dat ve službě AKS najdete v tématu [HTTPS příchozího přenosu dat ve službě Azure Kubernetes Service (AKS)][ingress-https].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[ingress-https]: ./ingress.md
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons


<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource
