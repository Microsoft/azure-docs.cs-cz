---
title: Vytvoření kontroleru příchozího přenosu dat
titleSuffix: Azure Kubernetes Service
description: Přečtěte si, jak nainstalovat a nakonfigurovat základní kontroler NGINX příchozího přenosu v clusteru Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 12/20/2019
ms.openlocfilehash: f6e07dde68f2f2ce0ccfbb7858fd1d217b993a62
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101480"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Vytvoření kontroleru příchozího přenosu dat ve službě Azure Kubernetes Service (AKS)

Kontroler příchozího přenosu dat je softwarový software, který poskytuje reverzní proxy, konfigurovatelné směrování provozu a ukončení protokolu TLS pro služby Kubernetes Services. Kubernetes příchozí prostředky se používají ke konfiguraci pravidel příchozího přenosu dat a tras pro jednotlivé služby Kubernetes. Pomocí řadiče příchozího přenosu dat a pravidel příchozího přenosu dat se dá jedna IP adresa používat ke směrování provozu do několika služeb v clusteru Kubernetes.

V tomto článku se dozvíte, jak nasadit [řadič Nginx][nginx-ingress] příchozího přenosu do clusteru Azure Kubernetes Service (AKS). Dvě aplikace se pak spustí v clusteru AKS, z nichž každá je přístupná přes jednu IP adresu.

Můžete také:

- [Povolit doplněk směrování aplikace HTTP][aks-http-app-routing]
- [Vytvoření kontroleru příchozího přenosu dat, který používá interní privátní síť a IP adresu][aks-ingress-internal]
- [Vytvoření kontroleru příchozího přenosu dat, který používá vaše vlastní certifikáty TLS][aks-ingress-own-tls]
- Vytvořte kontroler příchozího přenosu dat, který pomocí šifry umožňuje automatické generování certifikátů TLS [s dynamickou veřejnou IP adresou][aks-ingress-tls] nebo [statickou veřejnou IP adresou][aks-ingress-static-tls] .

## <a name="before-you-begin"></a>Před zahájením

Tento článek používá [Helm 3][helm] k instalaci řadiče pro příchozí Nginx a ukázkovou webovou aplikaci.

Tento článek také vyžaduje, abyste spustili Azure CLI verze 2.0.64 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Vytvoření kontroleru příchozího přenosu dat

Pokud chcete vytvořit kontroler příchozího přenosu dat, použijte Helm a nainstalujte *Nginx –* příchozí přenos dat. Pro přidání redundance se nasadí dvě repliky kontrolerů příchozího přenosu dat NGINX s parametrem `--set controller.replicaCount`. Pokud chcete mít v clusteru AKS k dispozici více než jeden uzel, zajistěte, aby bylo možné plně využít více uzlů.

Kontroler příchozího přenosu dat je potřeba naplánovat také v uzlu Linuxu. Uzly Windows serveru (v současné době ve verzi Preview v AKS) by neměli spustit kontroler příchozího přenosu dat. Selektor uzlů se specifikuje pomocí parametru `--set nodeSelector`, aby plánovači Kubernetes oznámil, že má spustit kontroler příchozího přenosu dat NGINX v uzlu Linuxu.

> [!TIP]
> Následující příklad vytvoří obor názvů Kubernetes pro prostředky příchozího přenosu dat s názvem příchozí *– Basic*. Podle potřeby zadejte obor názvů pro vlastní prostředí.

> [!TIP]
> Pokud chcete povolit [zachování IP adresy zdrojového klienta][client-source-ip] pro požadavky na kontejnery v clusteru, přidejte `--set controller.service.externalTrafficPolicy=Local` do příkazu Helm Install. Zdrojová IP adresa klienta je uložená v hlavičce žádosti v části *předané X-pro*. Při použití kontroleru příchozího přenosu dat s povoleným zachováním IP adresy klienta nebude předávat SSL fungovat.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repository
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Když se pro kontroler příchozího přenosu NGINX vytvoří služba Vyrovnávání zatížení Kubernetes, přiřadí se dynamická veřejná IP adresa, jak je znázorněno v následujícím příkladu výstupu:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                             TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-controller         LoadBalancer   10.0.61.144    EXTERNAL_IP   80:30386/TCP,443:32276/TCP   6m2s
nginx-ingress-default-backend    ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
```

Zatím se nevytvořila žádná pravidla příchozího přenosu dat, takže pokud přejdete na interní IP adresu, zobrazí se 404 výchozí stránka NGINX adaptéru pro příjem dat. Pravidla příchozího přenosu dat jsou nakonfigurovaná v následujících krocích.

## <a name="run-demo-applications"></a>Spustit ukázkové aplikace

Pokud chcete zobrazit kontroler příchozího přenosu v akci, spusťte v clusteru AKS dvě ukázkové aplikace. V tomto příkladu se Helm používá k nasazení dvou instancí jednoduché aplikace *Hello World* .

Než budete moct nainstalovat ukázkové grafy Helm, přidejte do prostředí Helm úložiště ukázek Azure následujícím způsobem:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Vytvořte první ukázkovou aplikaci z grafu Helm pomocí následujícího příkazu:

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

Nyní nainstalujte druhou instanci ukázkové aplikace. Pro druhou instanci zadáte nový název, aby byly tyto dvě aplikace vizuálně jedinečné. Zadejte také jedinečný název služby:

```console
helm install aks-helloworld-two azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="aks-helloworld-two"
```

## <a name="create-an-ingress-route"></a>Vytvoření trasy příchozího přenosu dat

Obě aplikace jsou teď spuštěné v clusteru Kubernetes. Pokud chcete směrovat provoz do každé aplikace, vytvořte Kubernetes prostředek příchozího přenosu dat. Prostředek příchozího přenosu dat konfiguruje pravidla, která směrují provoz do jedné z těchto dvou aplikací.

V následujícím příkladu je přenos do *EXTERNAL_IP* směrován do služby s názvem `aks-helloworld`. Provoz do *EXTERNAL_IP/Hello-World-Two* je směrován do `aks-helloworld-two` služby. Provoz do *EXTERNAL_IP/static* je směrován do služby s názvem `aks-helloworld` pro statické prostředky.

Vytvořte soubor s názvem `hello-world-ingress.yaml` a zkopírujte ho do následujícího příkladu YAML.

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
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /static(/|$)(.*)
```

Pomocí `kubectl apply -f hello-world-ingress.yaml` příkazu vytvořte prostředek příchozího přenosu dat.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
ingress.extensions/hello-world-ingress-static created
```

## <a name="test-the-ingress-controller"></a>Test řadiče pro příchozí přenosy

Pokud chcete testovat trasy pro kontroler příchozího přenosu dat, vyhledejte tyto dvě aplikace. Otevřete webový prohlížeč na IP adresu vašeho kontroleru NGINX příchozího přenosu, například *EXTERNAL_IP*. První ukázková aplikace se zobrazí ve webovém prohlížeči, jak je znázorněno v následujícím příkladu:

![První aplikace běžící za kontrolou příchozího přenosu dat](media/ingress-basic/app-one.png)

Nyní přidejte cestu */Hello-World-Two* k IP adrese, například *EXTERNAL_IP/Hello-World-Two*. Zobrazí se druhá ukázková aplikace s vlastním nadpisem:

![Druhá aplikace spuštěná za kontrolou příchozího přenosu dat](media/ingress-basic/app-two.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento článek používá Helm k instalaci komponent příchozího přenosu dat a ukázkových aplikací. Když nasadíte graf Helm, vytvoří se několik prostředků Kubernetes. Tyto prostředky zahrnují lusky, nasazení a služby. Chcete-li vyčistit tyto prostředky, můžete buď odstranit celý vzorový obor názvů, nebo jednotlivé prostředky.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Odstranění ukázkového oboru názvů a všech prostředků

Chcete-li odstranit celý vzorový obor názvů, `kubectl delete` použijte příkaz a zadejte název oboru názvů. Všechny prostředky v oboru názvů jsou odstraněny.

```console
kubectl delete namespace ingress-basic
```

Pak odeberte úložiště Helm pro aplikaci AKS Hello World:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Odstranit prostředky jednotlivě

Další možností je podrobnější přístup k odstranění jednotlivých vytvořených prostředků. Seznam vydaných verzí Helm `helm list` pomocí příkazu. Vyhledejte grafy s názvem *Nginx-* příchozí a *AKS-HelloWorld*, jak je znázorněno v následujícím příkladu výstupu:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-06 19:57:00.131576 -0600 CST    deployed        aks-helloworld-0.1.0               
aks-helloworld-two      ingress-basic   1               2020-01-06 19:57:10.971365 -0600 CST    deployed        aks-helloworld-0.1.0               
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Odstraňte vydané verze `helm delete` příkazem. Následující příklad odstraní nasazení NGINX příchozího přenosu dat a dvě ukázkové aplikace Hello World AKS.

```
$ helm delete aks-helloworld aks-helloworld-two nginx-ingress --namespace ingress-basic

release "aks-helloworld" uninstalled
release "aks-helloworld-two" uninstalled
release "nginx-ingress" uninstalled
```

Pak odeberte úložiště Helm pro aplikaci AKS Hello World:

```console
helm repo remove azure-samples
```

Odebrat trasu příchozího přenosu dat směrovaného do ukázkových aplikací:

```console
kubectl delete -f hello-world-ingress.yaml
```

Nakonec můžete odstranit samotný obor názvů. Použijte `kubectl delete` příkaz a zadejte název vašeho oboru názvů:

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
[helm]: https://helm.sh/
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
