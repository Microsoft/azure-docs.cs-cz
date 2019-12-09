---
title: Vytvoření základního kontroleru příchozího přenosu dat ve službě Azure Kubernetes Service (AKS)
description: Přečtěte si, jak nainstalovat a nakonfigurovat základní kontroler NGINX příchozího přenosu v clusteru Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 7b1fb26adc49067c35745011414ada7b33d7e55e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74913582"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Vytvoření kontroleru příchozího přenosu dat ve službě Azure Kubernetes Service (AKS)

Kontroler příchozího přenosu dat je softwarový software, který poskytuje reverzní proxy, konfigurovatelné směrování provozu a ukončení protokolu TLS pro služby Kubernetes Services. Kubernetes příchozí prostředky se používají ke konfiguraci pravidel příchozího přenosu dat a tras pro jednotlivé služby Kubernetes. Pomocí řadiče příchozího přenosu dat a pravidel příchozího přenosu dat se dá jedna IP adresa používat ke směrování provozu do několika služeb v clusteru Kubernetes.

V tomto článku se dozvíte, jak nasadit [řadič Nginx][nginx-ingress] příchozího přenosu do clusteru Azure Kubernetes Service (AKS). Dvě aplikace se pak spustí v clusteru AKS, z nichž každá je přístupná přes jednu IP adresu.

Můžete také:

- [Povolit doplněk směrování aplikace HTTP][aks-http-app-routing]
- [Vytvoření kontroleru příchozího přenosu dat, který používá interní privátní síť a IP adresu][aks-ingress-internal]
- [Vytvoření kontroleru příchozího přenosu dat, který používá vaše vlastní certifikáty TLS][aks-ingress-own-tls]
- Vytvořte kontroler příchozího přenosu dat, který pomocí šifry umožňuje automatické generování certifikátů TLS [s dynamickou veřejnou IP adresou][aks-ingress-tls] nebo [statickou veřejnou IP adresou][aks-ingress-static-tls] .

## <a name="before-you-begin"></a>Než začnete

Tento článek používá Helm k instalaci řadiče NGINX příchozího přenosu dat, správce certifikátů a ukázkovou webovou aplikaci. Musíte mít Helm inicializovaný v rámci vašeho clusteru AKS a používat účet služby pro pokladnu. Další informace o konfiguraci a použití Helm najdete v tématu [install Applications with Helm in Azure Kubernetes Service (AKS)][use-helm].

Tento článek také vyžaduje, abyste spustili Azure CLI verze 2.0.64 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Vytvoření kontroleru příchozího přenosu dat

K vytvoření kontroleru příchozího přenosu dat použijte `Helm` k instalaci *Nginx-* příchozího přenosu dat. Pro přidání redundance se nasadí dvě repliky řadičů příchozího přenosu NGINX s parametrem `--set controller.replicaCount`. Pokud chcete mít v clusteru AKS k dispozici více než jeden uzel, zajistěte, aby bylo možné plně využít více uzlů.

Řadič příchozího přenosu dat musí být také naplánován na uzel Linux. Uzly Windows serveru (v současné době ve verzi Preview v AKS) by neměli spustit kontroler příchozího přenosu dat. Selektor uzlů je určený pomocí parametru `--set nodeSelector`, který umožňuje, aby Plánovač Kubernetes spouštěl NGINX řadič příchozího přenosu v uzlu založeném na systému Linux.

> [!TIP]
> Následující příklad vytvoří obor názvů Kubernetes pro prostředky příchozího přenosu dat s názvem příchozí *– Basic*. Podle potřeby zadejte obor názvů pro vlastní prostředí. Pokud váš cluster AKS není RBAC povolený, přidejte `--set rbac.create=false` do příkazů Helm.

> [!TIP]
> Pokud chcete povolit [zachování IP adresy zdrojového klienta][client-source-ip] pro požadavky na kontejnery v clusteru, přidejte `--set controller.service.externalTrafficPolicy=Local` do příkazu pro instalaci Helm. Zdrojová IP adresa klienta je uložená v hlavičce žádosti v části *předané X-pro*. Při použití kontroleru příchozího přenosu dat s povoleným zachováním IP adresy klienta nebude předávat SSL fungovat.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Když se pro kontroler příchozího přenosu NGINX vytvoří služba Vyrovnávání zatížení Kubernetes, přiřadí se dynamická veřejná IP adresa, jak je znázorněno v následujícím příkladu výstupu:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                                 TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
aspiring-labradoodle-nginx-ingress-controller        LoadBalancer   10.0.61.144    40.117.74.8   80:30386/TCP,443:32276/TCP   6m2s
aspiring-labradoodle-nginx-ingress-default-backend   ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
```

Zatím se nevytvořila žádná pravidla příchozího přenosu dat, takže pokud přejdete na interní IP adresu, zobrazí se 404 výchozí stránka NGINX adaptéru pro příjem dat. Pravidla příchozího přenosu dat jsou nakonfigurovaná v následujících krocích.

## <a name="run-demo-applications"></a>Spustit ukázkové aplikace

Pokud chcete zobrazit kontroler příchozího přenosu v akci, spusťte v clusteru AKS dvě ukázkové aplikace. V tomto příkladu se Helm používá k nasazení dvou instancí jednoduché aplikace Hello World.

Než budete moct nainstalovat ukázkové grafy Helm, přidejte do prostředí Helm úložiště ukázek Azure následujícím způsobem:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Vytvořte první ukázkovou aplikaci z grafu Helm pomocí následujícího příkazu:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Nyní nainstalujte druhou instanci ukázkové aplikace. Pro druhou instanci zadáte nový název, aby byly tyto dvě aplikace vizuálně jedinečné. Zadejte také jedinečný název služby:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Vytvoření trasy příchozího přenosu dat

Obě aplikace jsou teď spuštěné v clusteru Kubernetes. Pokud chcete směrovat provoz do každé aplikace, vytvořte Kubernetes prostředek příchozího přenosu dat. Prostředek příchozího přenosu dat konfiguruje pravidla, která směrují provoz do jedné z těchto dvou aplikací.

V následujícím příkladu je provoz na adresu `http://40.117.74.8/` směrován do služby s názvem `aks-helloworld`. Provoz na adresu `http://40.117.74.8/hello-world-two` je směrován do služby `ingress-demo`.

Vytvořte soubor s názvem `hello-world-ingress.yaml` a zkopírujte následující příklad YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

Pomocí příkazu `kubectl apply -f hello-world-ingress.yaml` vytvořte prostředek příchozího přenosu dat.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Test řadiče pro příchozí přenosy

Pokud chcete testovat trasy pro kontroler příchozího přenosu dat, vyhledejte tyto dvě aplikace. Otevřete webový prohlížeč na IP adresu vašeho kontroleru NGINX příchozího přenosu, například *http://40.117.74.8* . První ukázková aplikace se zobrazí ve webovém prohlížeči, jak je znázorněno v následujícím příkladu:

![První aplikace běžící za kontrolou příchozího přenosu dat](media/ingress-basic/app-one.png)

Nyní přidejte cestu */Hello-World-Two* k IP adrese, například *http://40.117.74.8/hello-world-two* . Zobrazí se druhá ukázková aplikace s vlastním nadpisem:

![Druhá aplikace spuštěná za kontrolou příchozího přenosu dat](media/ingress-basic/app-two.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento článek používá Helm k instalaci komponent příchozího přenosu dat a ukázkových aplikací. Když nasadíte graf Helm, vytvoří se několik prostředků Kubernetes. Tyto prostředky zahrnují lusky, nasazení a služby. Chcete-li vyčistit tyto prostředky, můžete buď odstranit celý vzorový obor názvů, nebo jednotlivé prostředky.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Odstranění ukázkového oboru názvů a všech prostředků

Chcete-li odstranit celý ukázkový obor názvů, použijte příkaz `kubectl delete` a zadejte název oboru názvů. Všechny prostředky v oboru názvů jsou odstraněny.

```console
kubectl delete namespace ingress-basic
```

Pak odeberte úložiště Helm pro aplikaci AKS Hello World:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Odstranit prostředky jednotlivě

Další možností je podrobnější přístup k odstranění jednotlivých vytvořených prostředků. Seznam vydaných verzí Helm pomocí příkazu `helm list`. Vyhledejte grafy s názvem *Nginx-* příchozí a *AKS-HelloWorld*, jak je znázorněno v následujícím příkladu výstupu:

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
aspiring-labradoodle    1           Wed Mar 27 19:55:37 2019    DEPLOYED    nginx-ingress-1.3.1     0.22.0      ingress-basic
esteemed-koala          1           Wed Mar 27 19:59:18 2019    DEPLOYED    aks-helloworld-0.1.0                ingress-basic
wonderful-puma          1           Wed Mar 27 19:59:07 2019    DEPLOYED    aks-helloworld-0.1.0                ingress-basic
```

Odstraňte vydané verze pomocí příkazu `helm delete`. Následující příklad odstraní nasazení NGINX příchozího přenosu dat a dvě ukázkové aplikace Hello World AKS.

```
$ helm delete aspiring-labradoodle esteemed-koala wonderful-puma

release "aspiring-labradoodle" deleted
release "esteemed-koala" deleted
release "wonderful-puma" deleted
```

Pak odeberte úložiště Helm pro aplikaci AKS Hello World:

```console
helm repo remove azure-samples
```

Odebrat trasu příchozího přenosu dat směrovaného do ukázkových aplikací:

```console
kubectl delete -f hello-world-ingress.yaml
```

Nakonec můžete odstranit samotný obor názvů. Použijte příkaz `kubectl delete` a zadejte název vašeho oboru názvů:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Další kroky

Tento článek obsahuje některé externí komponenty, které se AKS. Další informace o těchto komponentách naleznete na následujících stránkách projektu:

- [Helm CLI][helm-cli]
- [Kontroler NGINX pro příchozí přenosy][nginx-ingress]

Můžete také:

- [Povolit doplněk směrování aplikace HTTP][aks-http-app-routing]
- [Vytvoření kontroleru příchozího přenosu dat, který používá interní privátní síť a IP adresu][aks-ingress-internal]
- [Vytvoření kontroleru příchozího přenosu dat, který používá vaše vlastní certifikáty TLS][aks-ingress-own-tls]
- Vytvořte kontroler příchozího přenosu dat, který pomocí šifry umožňuje automatické generování certifikátů TLS [s dynamickou veřejnou IP adresou][aks-ingress-tls] nebo [statickou veřejnou IP adresou][aks-ingress-static-tls] .

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
