---
title: (ZASTARALÉ) Nasazení kontejnerů s helmou v Azure Kubernetes
description: Použití nástroje pro balení Helmu k nasazení kontejnerů v clusteru Kubernetes ve službě Azure Container Service
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: a32c9fab3877a693d2df26571b9fae4aa7b4380c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76271087"
---
# <a name="deprecated-use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>(ZASTARALÉ) Nasazení kontejnerů v clusteru Kubernetes pomocí helmu

> [!TIP]
> Aktualizovanou verzi tohoto článku, který používá službu Azure Kubernetes, najdete v článku [Instalace aplikací s helmou ve službě Azure Kubernetes Service (AKS).](../../aks/kubernetes-helm.md)

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Helm](https://github.com/kubernetes/helm/) je open source obalový nástroj, který vám pomůže nainstalovat a spravovat životní cyklus aplikací Kubernetes. Podobně jako linuxoví manažeři balíčků, jako jsou Apt-get a Yum, helm se používá ke správě Kubernetesových grafů, což jsou balíčky předkonfigurovaných zdrojů Kubernetes. Tento článek ukazuje, jak pracovat s Helmem v clusteru Kubernetes nasazeném ve službě Azure Container Service.

Kormidlo má dvě složky: 
* **Helm CLI** je klient, který běží na vašem počítači místně nebo v cloudu  

* **Tiller** je server, který běží v clusteru Kubernetes a spravuje životní cyklus vašich aplikací Kubernetes 
 
## <a name="prerequisites"></a>Požadavky

* [Vytvoření clusteru Kubernetes](container-service-kubernetes-walkthrough.md) ve službě Azure Container Service

* [Instalace a `kubectl` konfigurace](../container-service-connect.md) v místním počítači

* [Instalace helmu](https://github.com/kubernetes/helm/blob/master/docs/install.md) do místního počítače

## <a name="helm-basics"></a>Základy kormidla 

Chcete-li zobrazit informace o clusteru Kubernetes, do kterého instalujete tiller a nasazujete aplikace, zadejte následující příkaz:

```bash
kubectl cluster-info 
```
![kubectl cluster-info](./media/container-service-kubernetes-helm/clusterinfo.png)
 
Po instalaci helmu nainstalujte kormidlo do clusteru Kubernetes zadáním následujícího příkazu:

```bash
helm init --upgrade
```
Po úspěšném dokončení se zobrazí výstup jako následující:

![Instalace kultivátoru](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
Chcete-li zobrazit všechny grafy helmu, které jsou k dispozici v úložišti, zadejte následující příkaz:

```bash 
helm search 
```

Výstup se zobrazí jako následující:

![Vyhledávání helmu](./media/container-service-kubernetes-helm/helm-search.png)
 
Chcete-li grafy aktualizovat, abyste získali nejnovější verze, zadejte:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Nasazení grafu řadiče příchozího přenosu dat Nginx 
 
Chcete-li nasadit graf řadiče příchozího přenosu dat Nginx, zadejte jeden příkaz:

```bash
helm install stable/nginx-ingress 
```
![Nasazení řadiče příchozího přenosu dat](./media/container-service-kubernetes-helm/nginx-ingress.png)

Pokud zadáte `kubectl get svc` zobrazit všechny služby, které jsou spuštěny v clusteru, uvidíte, že IP adresa je přiřazena k řadiči příchozího přenosu dat. (Během probíhá ní, zobrazí `<pending>`se . Dokončení trvá několik minut.) 

Po přiřazení ip adresy přejděte na hodnotu externí IP adresy a zjisti, že je spuštěn back-end Nginx. 
 
![Ip adresa příchozího přenosu dat](./media/container-service-kubernetes-helm/ingress-ip-address.png)


Chcete-li zobrazit seznam grafů nainstalovaných v clusteru, zadejte:

```bash
helm list 
```

Příkaz můžete zkrátit na `helm ls`.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>Nasazení grafu MariaDB a klienta

Nyní nasaďte graf MariaDB a klienta MariaDB pro připojení k databázi.

Chcete-li nasadit tabulku MariaDB, zadejte následující příkaz:

```bash
helm install --name v1 stable/mariadb
```

kde `--name` je značka používaná pro vydání.

> [!TIP]
> Pokud se nasazení `helm repo update` nezdaří, spusťte a akci opakujte.
>
 
 
Chcete-li zobrazit všechny grafy nasazené v clusteru, zadejte:

```bash 
helm list
```
 
Chcete-li zobrazit všechna nasazení spuštěná v clusteru, zadejte:

```bash
kubectl get deployments 
``` 
 
 
Nakonec chcete-li spustit pod pro přístup ke klientovi, zadejte:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
Chcete-li se připojit ke klientovi, `v1-mariadb` zadejte následující příkaz, který nahradí názvem nasazení:

```bash
sudo mysql –h v1-mariadb
```
 
 
Nyní můžete použít standardní příkazy SQL k vytváření databází, tabulek atd. Například `Create DATABASE testdb1;` vytvoří prázdnou databázi. 
 
 
 
## <a name="next-steps"></a>Další kroky

* Další informace o správě Kubernetesových grafů naleznete v [dokumentaci k helmu](https://github.com/kubernetes/helm/blob/master/docs/index.md). 

