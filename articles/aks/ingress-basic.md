---
title: Vytvoření základního řadiče příchozího přenosu dat ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak nainstalovat a nakonfigurovat základní řadič příchozího přenosu dat NGINX v clusteru služby Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 12/20/2019
ms.openlocfilehash: e37f7aa677be129aa9fe568880c53cc860947e30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595633"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Vytvoření řadiče příchozího přenosu dat ve službě Azure Kubernetes Service (AKS)

Řadič příchozího přenosu dat je software, který poskytuje reverzní proxy server, konfigurovatelné směrování provozu a ukončení TLS pro služby Kubernetes. Prostředky příchozího přenosu dat kubernetes se používají ke konfiguraci pravidel příchozího přenosu dat a tras pro jednotlivé služby Kubernetes. Pomocí řadiče příchozího přenosu dat a pravidel příchozího přenosu dat lze jednu adresu IP použít k směrování provozu do více služeb v clusteru Kubernetes.

Tento článek ukazuje, jak nasadit [řadič příchozího přenosu dat NGINX][nginx-ingress] v clusteru služby Azure Kubernetes Service (AKS). Dvě aplikace jsou pak spuštěny v clusteru AKS, z nichž každá je přístupná přes jednu IP adresu.

Můžete také:

- [Povolení doplňku směrování aplikací HTTP][aks-http-app-routing]
- [Vytvoření řadiče příchozího přenosu dat, který používá interní, privátní síť a adresu IP][aks-ingress-internal]
- [Vytvoření řadiče příchozího přenosu dat, který používá vlastní certifikáty TLS][aks-ingress-own-tls]
- Vytvoření řadiče příchozího přenosu dat, který používá let's Encrypt k automatickému generování certifikátů TLS [s dynamickou veřejnou IP adresou][aks-ingress-tls] nebo [statickou veřejnou IP adresou][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Než začnete

Tento článek používá Helm k instalaci řadiče příchozího přenosu dat NGINX a ukázkové webové aplikace.

Tento článek také vyžaduje, abyste spouštěli Azure CLI verze 2.0.64 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Vytvoření řadiče příchozího přenosu dat

Chcete-li vytvořit řadič příchozího přenosu dat, použijte helmu k instalaci *nginx-ingress*. Pro přidání redundance se nasadí dvě repliky kontrolerů příchozího přenosu dat NGINX s parametrem `--set controller.replicaCount`. Chcete-li plně využít spuštění replik řadiče příchozího přenosu dat, ujistěte se, že je více než jeden uzel v clusteru AKS.

Kontroler příchozího přenosu dat je potřeba naplánovat také v uzlu Linuxu. Uzly systému Windows Server (aktuálně ve verzi preview v AKS) by neměly spouštět řadič příchozího přenosu dat. Selektor uzlů se specifikuje pomocí parametru `--set nodeSelector`, aby plánovači Kubernetes oznámil, že má spustit kontroler příchozího přenosu dat NGINX v uzlu Linuxu.

> [!TIP]
> Následující příklad vytvoří obor názvů Kubernetes pro prostředky příchozího přenosu dat s názvem *ingress-basic*. Podle potřeby zadejte obor názvů pro vlastní prostředí.

> [!TIP]
> Pokud chcete povolit [uchování IP zdrojů klienta][client-source-ip] pro požadavky `--set controller.service.externalTrafficPolicy=Local` na kontejnery v clusteru, přidejte do příkazu Helm install. Zdrojová ADRESA klienta je uložena v hlavičce požadavku v části *X-Forwarded-For*. Při použití řadiče příchozího přenosu dat s povoleným uchováním IP zdroje klienta nebude předávací přenos SSL fungovat.

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

Při vytvoření služby Vyrovnávání zatížení Kubernetes pro řadič příchozího přenosu dat NGINX je přiřazena dynamická veřejná IP adresa, jak je znázorněno v následujícím příkladu výstupu:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                             TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-controller         LoadBalancer   10.0.61.144    EXTERNAL_IP   80:30386/TCP,443:32276/TCP   6m2s
nginx-ingress-default-backend    ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
```

Zatím nebyla vytvořena žádná pravidla příchozího přenosu dat, takže při procházení interní IP adresy se zobrazí výchozí stránka řadiče příchozího přenosu dat NGINX 404. Pravidla příchozího přenosu dat jsou konfigurována v následujících krocích.

## <a name="run-demo-applications"></a>Spuštění ukázkových aplikací

Chcete-li zobrazit řadič příchozího přenosu dat v akci, spusťme dvě ukázkové aplikace v clusteru AKS. V tomto příkladu Helm se používá k nasazení dvou instancí jednoduché aplikace *Hello svět.*

Před instalací ukázkových grafů Helm přidejte úložiště ukázek Azure do prostředí Helm následujícím způsobem:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Vytvořte první ukázkovou aplikaci z grafu Helm pomocí následujícího příkazu:

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

Nyní nainstalujte druhou instanci ukázkové aplikace. Pro druhou instanci zadáte nový název tak, aby obě aplikace jsou vizuálně odlišné. Zadáte také jedinečný název služby:

```console
helm install aks-helloworld-two azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="aks-helloworld-two"
```

## <a name="create-an-ingress-route"></a>Vytvoření postupu příchozího přenosu dat

Obě aplikace jsou nyní spuštěny v clusteru Kubernetes. Chcete-li směrovat provoz do každé aplikace, vytvořte prostředek příchozího přenosu dat Kubernetes. Prostředek příchozího přenosu dat konfiguruje pravidla, která směrují provoz do jedné ze dvou aplikací.

V následujícím příkladu je provoz *na EXTERNAL_IP* `aks-helloworld`směrován na službu s názvem . Provoz na *EXTERNAL_IP/hello-world-two* je `aks-helloworld-two` směrován do služby. Provoz na *EXTERNAL_IP/statické* je směrován `aks-helloworld` do služby pojmenované pro statické prostředky.

Vytvořte soubor `hello-world-ingress.yaml` s názvem a zkopírujte v následujícím příkladu YAML.

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

Vytvořte prostředek příchozího `kubectl apply -f hello-world-ingress.yaml` přenosu dat pomocí příkazu.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
ingress.extensions/hello-world-ingress-static created
```

## <a name="test-the-ingress-controller"></a>Otestujte řadič příchozího přenosu dat

Chcete-li otestovat trasy pro řadič příchozího přenosu dat, přejděte na dvě aplikace. Otevřete webový prohlížeč s IP adresou řadiče příchozího přenosu dat NGINX, například *EXTERNAL_IP*. První demo aplikace se zobrazí ve webovém prohlížeči, jak je znázorněno na následujícím příkladu:

![První aplikace spuštěná za řadičem příchozího přenosu dat](media/ingress-basic/app-one.png)

Nyní přidejte */hello-world-two* cestu k adrese IP, například *EXTERNAL_IP/hello-world-two*. Zobrazí se druhá demo aplikace s vlastním názvem:

![Druhá aplikace spuštěná za řadičem příchozího přenosu dat](media/ingress-basic/app-two.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento článek používá Helm k instalaci součástí příchozího přenosu dat a ukázkové aplikace. Při nasazení grafu Helm se vytvoří několik prostředků Kubernetes. Tyto prostředky zahrnují pody, nasazení a služby. Chcete-li vyčistit tyto prostředky, můžete odstranit celý ukázkový obor názvů nebo jednotlivé prostředky.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Odstranění ukázkového oboru názvů a všech prostředků

Chcete-li odstranit celý ukázkový `kubectl delete` obor názvů, použijte příkaz a zadejte název oboru názvů. Všechny prostředky v oboru názvů budou odstraněny.

```console
kubectl delete namespace ingress-basic
```

Potom odstraňte repo helmu pro aplikaci AKS hello world:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Odstranění prostředků jednotlivě

Alternativně podrobnější přístup je odstranit jednotlivé vytvořené prostředky. Seznam vydání helmu `helm list` pomocí příkazu. Podívejte se na grafy s názvem *nginx-ingress* a *aks-helloworld*, jak je znázorněno v následujícím příkladu výstupu:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-06 19:57:00.131576 -0600 CST    deployed        aks-helloworld-0.1.0               
aks-helloworld-two      ingress-basic   1               2020-01-06 19:57:10.971365 -0600 CST    deployed        aks-helloworld-0.1.0               
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Odstraňte verze pomocí `helm delete` příkazu. Následující příklad odstraní nasazení příchozího přenosu dat NGINX a dvě ukázkové aplikace AKS hello world.

```
$ helm delete aks-helloworld aks-helloworld-two nginx-ingress --namespace ingress-basic

release "aks-helloworld" uninstalled
release "aks-helloworld-two" uninstalled
release "nginx-ingress" uninstalled
```

Dále odeberte repo helmu pro aplikaci AKS hello world:

```console
helm repo remove azure-samples
```

Odeberte trasu příchozího přenosu dat, která směrovala provoz do ukázkových aplikací:

```console
kubectl delete -f hello-world-ingress.yaml
```

Nakonec můžete odstranit samotný obor názvů. Použijte `kubectl delete` příkaz a zadejte název oboru názvů:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Další kroky

Tento článek zahrnoval některé externí součásti AKS. Další informace o těchto součástech naleznete na následujících stránkách projektu:

- [Helm CLI][helm-cli]
- [Řadič příchozího přenosu dat NGINX][nginx-ingress]

Můžete také:

- [Povolení doplňku směrování aplikací HTTP][aks-http-app-routing]
- [Vytvoření řadiče příchozího přenosu dat, který používá interní, privátní síť a adresu IP][aks-ingress-internal]
- [Vytvoření řadiče příchozího přenosu dat, který používá vlastní certifikáty TLS][aks-ingress-own-tls]
- Vytvoření řadiče příchozího přenosu dat, který používá let's Encrypt k automatickému generování certifikátů TLS [s dynamickou veřejnou IP adresou][aks-ingress-tls] nebo [statickou veřejnou IP adresou][aks-ingress-static-tls]

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
