---
title: "Integrace se službami spravovanými Azure s využitím OSBA (Open Service Broker for Azure)"
description: "Integrace se službami spravovanými Azure s využitím OSBA (Open Service Broker for Azure)"
services: container-service
author: sozercan
manager: timlt
ms.service: container-service
ms.topic: overview
ms.date: 12/05/2017
ms.author: seozerca
ms.openlocfilehash: 594cb0afbdb0a44e9f092b9afc5af13b21e763a4
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2018
---
# <a name="integrate-with-azure-managed-services-using-open-service-broker-for-azure-osba"></a>Integrace se službami spravovanými Azure s využitím OSBA (Open Service Broker for Azure)

Open Service Broker for Azure (OSBA) společně s modulem [Kubernetes Service Catalog][kubernetes-service-catalog] umožňuje využívat služby spravované Azure v Kubernetes. Tato příručka se soustředí na nasazení modulu Service Catalog, Open Service Broker for Azure (OSBA) a aplikací, které používají služby spravované Azure s využitím Kubernetes.

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure

* Azure CLI 2.0: Můžete ho [nainstalovat místně][azure-cli-install], nebo použít ve službě [Azure Cloud Shell][azure-cloud-shell].

* Helm CLI 2.7+: Můžete ho [nainstalovat místně][helm-cli-install], nebo použít ve službě [Azure Cloud Shell][azure-cloud-shell].

* Oprávnění pro vytvoření instančního objektu s rolí přispěvatele v předplatném Azure

* Existující cluster Azure Container Service (AKS). Pokud potřebujete cluster AKS, postupujte podle článku [Vytvoření clusteru AKS][create-aks-cluster].

## <a name="install-service-catalog"></a>Instalace modulu Service Catalog

Prvním krokem je instalace modulu Service Catalog v clusteru Kubernetes s využitím diagramu Helmu. Upgraduje instalaci Tilleru (server Helmu) ve vašem clusteru pomocí příkazu:

```azurecli-interactive
helm init --upgrade
```

Teď do úložiště Helmu přidejte diagram modulu Service Catalog:

```azurecli-interactive
helm repo add svc-cat https://svc-catalog-charts.storage.googleapis.com
```

Nakonec nainstalujte Service Catalog s diagramem Helmu:

```azurecli-interactive
helm install svc-cat/catalog --name catalog --namespace catalog --set rbacEnable=false
```

Po spuštění diagramu Helmu ověřte, že se ve výstupu následujícího příkazu zobrazuje `servicecatalog`:

```azurecli-interactive
kubectl get apiservice
```

Výstup by měl vypadat například nějak takhle (tady je oříznutý):

```
NAME                                 AGE
v1.                                  10m
v1.authentication.k8s.io             10m
...
v1beta1.servicecatalog.k8s.io        34s
v1beta1.storage.k8s.io               10
```

## <a name="install-open-service-broker-for-azure"></a>Instalace technologie Open Service Broker for Azure

Dalším krokem je instalace zprostředkovatele [Open Service Broker for Azure][open-service-broker-azure], která zahrnuje katalog pro služby spravované Azure. Příkladem dostupných služeb Azure jsou Azure Database for PostgreSQL, Azure Redis Cache, Azure Database for MySQL, Azure Cosmos DB, Azure SQL Database a další.

Začněte přidáním zprostředkovatele Open Service Broker for Azure do úložiště Helmu:

```azurecli-interactive
helm repo add azure https://kubernetescharts.blob.core.windows.net/azure
```

Dále pomocí následujícího skriptu vytvořte [Instanční objekt][create-service-principal] a naplňte několik proměnných. Tyto proměnné se použijí při spuštění grafu Helmu k instalaci zprostředkovatele služby.

```azurecli-interactive
SERVICE_PRINCIPAL=$(az ad sp create-for-rbac)
AZURE_CLIENT_ID=$(echo $SERVICE_PRINCIPAL | cut -d '"' -f 4)
AZURE_CLIENT_SECRET=$(echo $SERVICE_PRINCIPAL | cut -d '"' -f 16)
AZURE_TENANT_ID=$(echo $SERVICE_PRINCIPAL | cut -d '"' -f 20)
AZURE_SUBSCRIPTION_ID=$(az account show --query id --output tsv)
```

Teď máte tyto proměnné prostředí naplněné a můžete spustit následující příkaz pro instalaci zprostředkovatele služby.

```azurecli-interactive
helm install azure/open-service-broker-azure --name osba --namespace osba \
    --set azure.subscriptionId=$AZURE_SUBSCRIPTION_ID \
    --set azure.tenantId=$AZURE_TENANT_ID \
    --set azure.clientId=$AZURE_CLIENT_ID \
    --set azure.clientSecret=$AZURE_CLIENT_SECRET
```

Jakmile je nasazení OSBA kompletní, nainstalujte [Service Catalog CLI][service-catalog-cli]. Toto snadno použitelné rozhraní příkazového řádku umožňuje dotazování na zprostředkovatele, třídy a plány služeb a další.

Spuštěním následujícího příkazu nainstalujte binární soubor Service Catalog CLI:

```azurecli-interactive
curl -sLO https://servicecatalogcli.blob.core.windows.net/cli/latest/$(uname -s)/$(uname -m)/svcat
chmod +x ./svcat
```

Teď zobrazte seznam nainstalovaných zprostředkovatelů služeb:

```azurecli-interactive
./svcat get brokers
```

Zobrazený výstup by měl vypadat přibližně takto:

```
  NAME                               URL                                STATUS
+------+--------------------------------------------------------------+--------+
  osba   http://osba-open-service-broker-azure.osba.svc.cluster.local   Ready
```

Potom zobrazte seznam dostupných tříd služeb. Zobrazené třídy služeb jsou dostupné služby spravované Azure, které se dají zřídit prostřednictvím zprostředkovatele Open Service Broker for Azure.

```azurecli-interactive
./svcat get classes
```

Nakonec zobrazte seznam všech dostupných plánů služeb. Plány služeb jsou úrovně pro služby spravované Azure. Například plány pro Azure Database for MySQL mají rozsah od `basic50` pro úroveň Basic s 50 jednotkami přenosu dat (DTU) až po `standard800` pro úroveň Standard s 800 DTU.

```azurecli-interactive
./svcat get plans
```

## <a name="install-wordpress-from-helm-chart-using-azure-database-for-mysql"></a>Instalace WordPressu z grafu Helmu pomocí Azure Database for MySQL

V tomto kroku použijete Helm k instalaci aktualizovaného grafu Helm pro WordPress. Tento graf zřídí externí instanci Azure Database for MySQL, kterou může použít WordPress. Tento proces může trvat několik minut.

```azurecli-interactive
helm install azure/wordpress --name wordpress --namespace wordpress --set resources.requests.cpu=0
```

K ověření, že instalace zřídila správné prostředky, zobrazte seznam nainstalovaných instancí služeb a vazeb:

```azurecli-interactive
./svcat get instances -n wordpress
./svcat get bindings -n wordpress
```

Zobrazte seznam nainstalovaných tajných kódů:

```azurecli-interactive
kubectl get secrets -n wordpress -o yaml
```

## <a name="next-steps"></a>Další kroky

Podle tohoto článku jste nasadili Service Catalog do clusteru Azure Container Service (AKS). Použili jste Open Service Broker for Azure k nasazení instalace WordPressu, která využívá služby spravované Azure, v tomto případě Azure Database for MySQL.

Pokud chcete získat přístup k dalším aktualizovaným grafům Helmu založeným na OSBA, použijte úložiště [Azure/helm-charts][helm-charts]. Pokud vás zajímá vytvoření vlastních grafů, které fungují pro OSBA, přečtěte si téma věnované [vytvoření nového grafu][helm-create-new-chart].

<!-- LINKS - external -->
[helm-charts]: https://github.com/Azure/helm-charts
[helm-cli-install]: kubernetes-helm.md#install-helm-cli
[helm-create-new-chart]: https://github.com/Azure/helm-charts#creating-a-new-chart
[kubernetes-service-catalog]: https://github.com/kubernetes-incubator/service-catalog
[open-service-broker-azure]: https://github.com/Azure/open-service-broker-azure
[service-catalog-cli]: https://github.com/Azure/service-catalog-cli

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[create-aks-cluster]: ./kubernetes-walkthrough.md
[create-service-principal]: ./kubernetes-service-principal.md
