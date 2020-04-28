---
title: ZASTARALÉ Nasazení kontejnerů pomocí Helm v Azure Kubernetes
description: Nasazení kontejnerů v clusteru Kubernetes v Azure Container Service pomocí nástroje pro vytváření balíčků Helm
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: a32c9fab3877a693d2df26571b9fae4aa7b4380c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76271087"
---
# <a name="deprecated-use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>ZASTARALÉ Nasazení kontejnerů v clusteru Kubernetes pomocí Helm

> [!TIP]
> Aktualizovanou verzi tohoto článku, který používá službu Azure Kubernetes, najdete v tématu [instalace aplikací pomocí Helm ve službě Azure Kubernetes Service (AKS)](../../aks/kubernetes-helm.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Helm](https://github.com/kubernetes/helm/) je open source nástroj pro balení, který vám pomůže s instalací a správou životního cyklu aplikací Kubernetes. Podobně jako správci balíčků pro Linux, jako jsou apt-get a Yumu, se Helm používá ke správě Kubernetes grafů, což jsou balíčky předkonfigurovaných prostředků Kubernetes. V tomto článku se dozvíte, jak pracovat s Helm v clusteru Kubernetes nasazeném v Azure Container Service.

Helm má dvě komponenty: 
* **Helm CLI** je klient, který běží na vašem počítači místně nebo v cloudu.  

* **Je server, který běží v** clusteru Kubernetes a spravuje životní cyklus vašich aplikací Kubernetes. 
 
## <a name="prerequisites"></a>Požadavky

* [Vytvoření clusteru Kubernetes](container-service-kubernetes-walkthrough.md) v Azure Container Service

* [Instalace a konfigurace `kubectl` ](../container-service-connect.md) v místním počítači

* [Instalace Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) na místní počítač

## <a name="helm-basics"></a>Základy Helm 

Chcete-li zobrazit informace o clusteru Kubernetes, do kterého instalujete, a nasazovat aplikace do, zadejte následující příkaz:

```bash
kubectl cluster-info 
```
![kubectl cluster – informace](./media/container-service-kubernetes-helm/clusterinfo.png)
 
Po instalaci Helm nainstalujte do clusteru Kubernetes zadáním následujícího příkazu:

```bash
helm init --upgrade
```
Po úspěšném dokončení se zobrazí výstup podobný následujícímu:

![Instalace do pokladny](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
Pokud chcete zobrazit všechny Helm grafy, které jsou k dispozici v úložišti, zadejte následující příkaz:

```bash 
helm search 
```

Zobrazí se výstup podobný následujícímu:

![Helm vyhledávání](./media/container-service-kubernetes-helm/helm-search.png)
 
Pokud chcete grafy aktualizovat tak, aby získaly nejnovější verze, zadejte:

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Nasazení grafu kontroleru Nginx příchozího přenosu dat 
 
Pokud chcete nasadit graf Nginx příchozího adaptéru, zadejte jeden příkaz:

```bash
helm install stable/nginx-ingress 
```
![Nasazení řadiče pro příchozí přenosy](./media/container-service-kubernetes-helm/nginx-ingress.png)

Pokud zadáte `kubectl get svc` zobrazení všech služeb, které běží na clusteru, uvidíte, že je IP adresa přiřazená k řadiči příchozího přenosu dat. (Zatímco probíhá přiřazení, vidíte `<pending>`. Dokončení trvá několik minut.) 

Po přiřazení IP adresy přejděte k hodnotě externí IP adresa a podívejte se na běžící Nginx back-end. 
 
![IP adresa příchozího přenosu dat](./media/container-service-kubernetes-helm/ingress-ip-address.png)


Pokud chcete zobrazit seznam grafů nainstalovaných v clusteru, zadejte:

```bash
helm list 
```

Příkaz lze zkrátit na `helm ls`.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>Nasazení grafu MariaDB a klienta

Teď nasaďte graf MariaDB a klienta MariaDB pro připojení k databázi.

K nasazení grafu MariaDB zadejte následující příkaz:

```bash
helm install --name v1 stable/mariadb
```

kde `--name` je značka použitá pro vydání.

> [!TIP]
> Pokud se nasazení nepovede, `helm repo update` spusťte operaci a zkuste to znovu.
>
 
 
Chcete-li zobrazit všechny grafy nasazené v clusteru, zadejte:

```bash 
helm list
```
 
Pokud chcete zobrazit všechna nasazení spuštěná v clusteru, zadejte:

```bash
kubectl get deployments 
``` 
 
 
Nakonec, pokud chcete pro přístup k klientovi spustit pod, typ:

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
Pokud se chcete připojit ke klientovi, zadejte následující příkaz a nahraďte `v1-mariadb` názvem vašeho nasazení:

```bash
sudo mysql –h v1-mariadb
```
 
 
Nyní můžete použít standardní příkazy SQL pro vytváření databází, tabulek atd. Například `Create DATABASE testdb1;` vytvoří prázdnou databázi. 
 
 
 
## <a name="next-steps"></a>Další kroky

* Další informace o správě Kubernetes grafů najdete v [dokumentaci k Helm](https://github.com/kubernetes/helm/blob/master/docs/index.md). 

