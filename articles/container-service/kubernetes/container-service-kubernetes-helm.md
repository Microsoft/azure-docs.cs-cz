---
title: Nasazení kontejnerů s nástrojem Helm. v Kubernetes v Azure
description: Nasazení kontejnerů v clusteru Kubernetes ve službě Azure Container Service pomocí nástroje Helm balení
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 882e785968f94473e80c7a14e5a68498add37735
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38634149"
---
# <a name="use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>Nasazení kontejnerů v clusteru Kubernetes pomocí Helm

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

[Příkaz Helm](https://github.com/kubernetes/helm/) je balení open source nástroj, který vám pomůže nainstalovat a spravovat životní cyklus aplikací Kubernetes. Podobně jako u správců balíčků Linux třeba Apt-get a Yumu Helm slouží ke správě grafů Kubernetes, což jsou balíčky předem prostředky Kubernetesu. V tomto článku se dozvíte, jak pracovat s nástrojem Helm v clusteru Kubernetes nasadit ve službě Azure Container Service.

Příkaz Helm má dvě součásti: 
* **Helm CLI** je klient, na kterém běží na vašem počítači, místně nebo v cloudu  

* **Tiller** je server, který běží v clusteru Kubernetes a spravuje životní cyklus vašich aplikací v Kubernetes 
 
## <a name="prerequisites"></a>Požadavky

* [Vytvoření clusteru Kubernetes](container-service-kubernetes-walkthrough.md) ve službě Azure Container Service

* [Instalace a konfigurace `kubectl` ](../container-service-connect.md) na místním počítači

* [Nainstalovat Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) na místním počítači

## <a name="helm-basics"></a>Základy Helm 

Chcete-li zobrazit informace o clusteru Kubernetes, že instalujete Tiller a nasazování vašich aplikací, zadejte následující příkaz:

```bash
kubectl cluster-info 
```
![kubectl clusteru info](./media/container-service-kubernetes-helm/clusterinfo.png)
 
Po dokončení instalace Helm nainstalujte na Kubernetes cluster Tiller tak, že zadáte následující příkaz:

```bash
helm init --upgrade
```
Po úspěšném dokončení, se zobrazí výstup podobný tomuto:

![Instalace tiller](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
Chcete-li zobrazit všechny grafům helmu k dispozici v úložišti, zadejte následující příkaz:

```bash 
helm search 
```

Zobrazí se výstup podobný tomuto:

![Příkaz Helm search](./media/container-service-kubernetes-helm/helm-search.png)
 
Chcete-li aktualizovat grafů zobrazíte nejnovější verze, zadejte:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Nasazení grafu kontroleru příchozího přenosu dat aplikace serveru Nginx 
 
Pokud chcete nasadit grafu kontroleru příchozího přenosu dat aplikace serveru Nginx, zadejte jeden příkaz:

```bash
helm install stable/nginx-ingress 
```
![Nasadit kontroler příchozího přenosu dat](./media/container-service-kubernetes-helm/nginx-ingress.png)

Pokud zadáte `kubectl get svc` Chcete-li zobrazit všechny služby, které jsou spuštěny v clusteru, uvidíte, že IP adresa je přiřazen do kontroleru příchozího přenosu. (Když probíhá přiřazení, zobrazí `<pending>`. Trvá několik minut na dokončení.) 

Po IP adresa se přiřadí, přejděte na hodnotu externí IP adresu a zobrazte spuštění back-end serveru Nginx. 
 
![IP adresu příchozího přenosu dat](./media/container-service-kubernetes-helm/ingress-ip-address.png)


Pokud chcete zobrazit seznam grafy, které jsou nainstalovány ve vašem clusteru, zadejte:

```bash
helm list 
```

Příkaz k můžete zkrátit `helm ls`.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>Nasazení klienta a graf MariaDB

Nasaďte nyní MariaDB grafu a MariaDB klienta pro připojení k databázi.

Pokud chcete nasadit grafu MariaDB, zadejte následující příkaz:

```bash
helm install --name v1 stable/mariadb
```

kde `--name` je značka použít pro vydané verze.

> [!TIP]
> Pokud se nasazení nezdaří, spusťte `helm repo update` a zkuste to znovu.
>
 
 
Chcete-li zobrazit všechny grafy nasadili v clusteru, zadejte:

```bash 
helm list
```
 
Pokud chcete zobrazit všechna nasazení, které běží na clusteru, zadejte:

```bash
kubectl get deployments 
``` 
 
 
Nakonec spusťte pod přístup klient, zadejte:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
Pokud chcete připojit ke klientovi, zadejte následující příkaz a nahraďte `v1-mariadb` s názvem vašeho nasazení:

```bash
sudo mysql –h v1-mariadb
```
 
 
Standardní příkazy SQL můžete teď použít k vytvoření databáze, tabulky, atd. Například `Create DATABASE testdb1;` vytvoří prázdnou databázi. 
 
 
 
## <a name="next-steps"></a>Další postup

* Další informace o správě grafů Kubernetes najdete v článku [Helm dokumentaci](https://github.com/kubernetes/helm/blob/master/docs/index.md). 

