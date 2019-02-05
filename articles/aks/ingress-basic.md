---
title: Vytvoření kontroleru základního příchozího přenosu dat ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak nainstalovat a nakonfigurovat základní kontroler příchozího přenosu dat NGINX v clusteru služby Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/30/2018
ms.author: iainfou
ms.openlocfilehash: 99f97f7d796fecf1ac77cb3752a9ba7019edfbbc
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55729860"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Vytvoření řadiče příchozího přenosu dat ve službě Azure Kubernetes Service (AKS)

Řadič služby příchozího přenosu dat je část softwaru, která poskytuje reverzní proxy server, směrování provozu konfigurovatelné a ukončení protokolu TLS pro služby Kubernetes. Prostředky Kubernetesu příchozího přenosu dat se používají ke konfiguraci pravidla příchozího přenosu dat a trasy pro jednotlivé služby Kubernetes. Použití kontroler příchozího přenosu dat a pravidla příchozího přenosu dat, jednu IP adresu je možné směrovat provoz do více služeb v clusteru Kubernetes.

V tomto článku se dozvíte, jak nasadit [kontroler příchozího přenosu dat NGINX] [ nginx-ingress] v clusteru služby Azure Kubernetes Service (AKS). Pak dvě aplikace běží v clusteru AKS, z nichž každý je přístupný přes jednu IP adresu.

Můžete také:

- [Povolit doplněk směrování aplikace HTTP][aks-http-app-routing]
- [Vytvoření řadiče příchozího přenosu dat, který se používá soukromé, interní sítě a IP adresy][aks-ingress-internal]
- [Vytvoření řadiče příchozího přenosu dat, která používá vlastní certifikáty TLS][aks-ingress-own-tls]
- Vytvoření řadiče příchozího přenosu dat, která používá umožňuje šifrovat automaticky generovat certifikáty TLS [s dynamické veřejné IP adresy] [ aks-ingress-tls] nebo [se statickou veřejnou IP adresu][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Před zahájením

Tento článek používá Helm k instalaci serveru NGINX kontroler příchozího přenosu dat, správce certifikátů a ukázkovou webovou aplikaci. Musíte mít Helm inicializován v rámci clusteru AKS a pomocí účtu služby pro Tiller. Další informace o konfiguraci a použití Helm, naleznete v tématu [instalace aplikací s nástrojem Helm ve službě Azure Kubernetes Service (AKS)][use-helm].

Tento článek také vyžaduje, že používáte Azure CLI verze 2.0.41 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Vytvoření řadiče příchozího přenosu dat

Chcete-li vytvořit kontroler příchozího přenosu dat, použijte `Helm` instalace *nginx příchozího přenosu dat*. Pro přidání redundance dvě repliky řadiče příchozího přenosu dat NGINX nasazení se používají `--set controller.replicaCount` parametru. Pokud chcete naplno využívat s replikami kontroler příchozího přenosu dat, ujistěte se, že existuje více než jeden uzel v clusteru AKS.

> [!TIP]
> Následující příklad nainstaluje kontroler příchozího přenosu dat v `kube-system` oboru názvů. V případě potřeby, můžete určit jiný obor názvů pro vlastní prostředí. Pokud váš cluster AKS není povoleno RBAC, přidejte `--set rbac.create=false` příkazy.

```console
helm install stable/nginx-ingress --namespace kube-system --set controller.replicaCount=2
```

Po vytvoření služby Vyrovnávání zatížení Kubernetes pro kontroler příchozího přenosu dat NGINX dynamická veřejná IP adresa je přiřazen, jak je znázorněno v následujícím příkladu výstupu:

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                         TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
masked-otter-nginx-ingress-controller        LoadBalancer   10.0.92.99    40.117.74.8   80:31077/TCP,443:32592/TCP   7m
masked-otter-nginx-ingress-default-backend   ClusterIP      10.0.46.106   <none>        80/TCP                       7m
```

Žádná pravidla příchozího přenosu dat dosud nebyly vytvořeny, takže pokud přejdete na interní IP adresa, zobrazí se stránka 404 výchozí kontroler příchozího přenosu dat serveru NGINX. Pravidla příchozího přenosu dat se konfigurují v následujících krocích.

## <a name="run-demo-applications"></a>Spuštění ukázkové aplikace

Pokud chcete zobrazit kontroler příchozího přenosu dat v akci, můžeme spustit dvě ukázkové aplikace ve vašem clusteru AKS. V tomto příkladu Helm umožňuje nasadit dvě instance aplikace jednoduchý "Hello world".

Před instalací ukázkové grafy Helm, přidejte úložiště ukázky v Azure následujícím způsobem pro vaše prostředí helmu:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Vytvoření první ukázkové aplikace z grafu helmu pomocí následujícího příkazu:

```console
helm install azure-samples/aks-helloworld
```

Nyní instalaci druhé instance ukázkové aplikace. Pro druhou instanci zadejte nový název tak, že dvě aplikace jsou vizuálně distinct. Můžete také zadat jedinečný název služby:

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Vytvoření trasy příchozího přenosu dat

Obě aplikace běží v clusteru Kubernetes. Pokud chcete směrovat provoz na každou aplikaci, vytvořte prostředek příchozího přenosu dat Kubernetes. Prostředek příchozího přenosu dat nakonfiguruje pravidla, která směrovat provoz mezi těmito dvěma aplikacemi.

V následujícím příkladu, provoz na adresu `http://40.117.74.8/` se směruje na službu s názvem `aks-helloworld`. Provoz na adresu `http://40.117.74.8/hello-world-two` směrován `ingress-demo` služby.

Vytvořte soubor s názvem `hello-world-ingress.yaml` a kopie v následujícím příkladu YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

Vytvoření prostředků pomocí příchozího přenosu dat `kubectl apply -f hello-world-ingress.yaml` příkazu.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Testovací kontroler příchozího přenosu dat

Pokud chcete otestovat trasy pro kontroler příchozího přenosu dat, přejděte na dvě aplikace. Otevřete webový prohlížeč na IP adresu řadiče NGINX příchozího přenosu dat, jako například *http://40.117.74.8*. Jak ukazuje následující příklad, zobrazí se první ukázkové aplikace ve webovém prohlížeči:

![První aplikaci spuštěnou za kontroler příchozího přenosu dat](media/ingress-basic/app-one.png)

Nyní přidejte */hello-world-two* cestu k IP Adrese, jako adresa *http://40.117.74.8/hello-world-two*. Zobrazí se druhé ukázkové aplikace s vlastní název:

![Druhá aplikace spuštěná za kontroler příchozího přenosu dat](media/ingress-basic/app-two.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento článek používá Helm k instalaci součásti příchozího přenosu dat a ukázkové aplikace. Při nasazování grafu helmu vytvoří řada prostředky Kubernetesu. Tyto prostředky zahrnují podů, nasazení a služby. K uvolnění těchto prostředků, nejprve seznamu Helm vydané verze s `helm list` příkazu. Vyhledejte grafy s názvem *nginx příchozího přenosu dat* a *aks-helloworld*, jak je znázorněno v následujícím příkladu výstupu:

```
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
gilded-duck         1           Tue Oct 16 16:52:25 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
righteous-numbat    1           Tue Oct 16 16:53:53 2018    DEPLOYED    aks-helloworld-0.1.0                default
looming-moth        1           Tue Oct 16 16:53:59 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Odstranit vydané verze s `helm delete` příkazu. Následující příklad odstraní nasazení serveru NGINX příchozího přenosu dat a dvě ukázkové AKS hello world aplikace.

```
$ helm delete gilded-duck righteous-numbat looming-moth

release "gilded-duck" deleted
release "righteous-numbat" deleted
release "looming-moth" deleted
```

V dalším kroku odeberte Helm úložiště pro aplikace hello world AKS:

```console
helm repo remove azure-samples
```

Nakonec odeberte trasu příchozího přenosu dat, která přesměruje přenosy do ukázkové aplikace:

```console
kubectl delete -f hello-world-ingress.yaml
```

## <a name="next-steps"></a>Další postup

Tento článek zahrnuty některé externí součásti pro AKS. Další informace o těchto komponent, naleznete na následujících stránkách projektu:

- [Helm CLI][helm-cli]
- [Kontroler příchozího přenosu dat serveru NGINX][nginx-ingress]

Můžete také:

- [Povolit doplněk směrování aplikace HTTP][aks-http-app-routing]
- [Vytvoření řadiče příchozího přenosu dat, který se používá soukromé, interní sítě a IP adresy][aks-ingress-internal]
- [Vytvoření řadiče příchozího přenosu dat, která používá vlastní certifikáty TLS][aks-ingress-own-tls]
- Vytvoření řadiče příchozího přenosu dat, která používá umožňuje šifrovat automaticky generovat certifikáty TLS [s dynamické veřejné IP adresy] [ aks-ingress-tls] nebo [se statickou veřejnou IP adresu][aks-ingress-static-tls]

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
