---
title: Migrace z Azure Red Hat OpenShiftu 3.11 na Azure Red Hat OpenShift 4
description: Migrace z Azure Red Hat OpenShiftu 3.11 na Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 08/13/2020
keywords: migrace, ARO, OpenShift, Red Hat
ms.openlocfilehash: 371672de83a6d745d7b367f8327a64e11059923e
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653274"
---
# <a name="migrate-from-azure-red-hat-openshift-311-to-azure-red-hat-openshift-4"></a>Migrace z Azure Red Hat OpenShift 3,11 na Azure Red Hat OpenShift 4

Azure Red Hat OpenShift v OpenShift 4 přináší Kubernetes 1,16 na operační systém Red Hat Core, soukromé clustery, přinesou si vlastní podporu virtuálních sítí a úplnou roli Správce clusteru. Kromě toho je k dispozici mnoho nových funkcí, jako je například podpora rozhraní operator Framework, centra operátorů a sítě služby OpenShift.

K úspěšnému přechodu z Azure Red Hat OpenShift 3,11 na Azure Red Hat OpenShift 4 nezapomeňte zkontrolovat [rozdíly v části úložiště, sítě, protokolování, zabezpečení a monitorování](https://docs.openshift.com/container-platform/4.4/migration/migrating_3_4/planning-migration-3-to-4.html).

V tomto článku ukážeme, jak migrovat z clusteru Azure Red Hat OpenShift 3,11 do clusteru Azure Red Hat 4.

> [!NOTE]
> Nástroje pro migraci Red Hat OpenShift, jako je třeba nástroj pro podporu migrace plochy pro kontrolu a nástroj pro migraci clusterových aplikací (VAČK), se nedají používat s clustery Azure Red Hat OpenShift 3,11.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster Azure Red Hat OpenShift 3,11.

## <a name="create-a-target-azure-red-hat-openshift-4-cluster"></a>Vytvoření cílového clusteru Azure Red Hat OpenShift 4

Nejdřív [vytvořte cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md) , který byste chtěli použít jako cílový cluster. Tady budeme používat základní konfiguraci. Pokud máte zájem o různá nastavení, přečtěte si [kurz vytvoření clusteru Azure Red Hat OpenShift 4](tutorial-create-cluster.md).

Vytvořte virtuální síť se dvěma prázdnými podsítěmi pro hlavní a pracovní uzly.

```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
```

Pak vytvořte cluster pomocí následujícího příkazu.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

## <a name="configure-the-target-openshift-4-cluster"></a>Konfigurace cílového clusteru OpenShift 4

### <a name="authentication"></a>Authentication

Aby uživatelé mohli pracovat s Azure Red Hat OpenShift, musí nejdřív ověřit cluster. Vrstva ověřování identifikuje uživatele přidruženého k žádostem o rozhraní Azure Red Hat OpenShift API. Vrstva autorizace pak pomocí informací o žádajícím uživateli určí, jestli je požadavek povolený.

Při vytvoření clusteru Azure Red Hat OpenShift 4 se vytvoří dočasný uživatel s právy pro správu. [Připojte se ke svému clusteru](tutorial-connect-cluster.md), přidejte uživatele a skupiny a [nakonfigurujte příslušná oprávnění](https://docs.openshift.com/container-platform/4.6/authentication/understanding-authentication.html) pro obojí.

### <a name="networking"></a>Sítě

Azure Red Hat OpenShift 4 používá několik různých operátorů k nastavení sítě ve vašem clusteru: [operátor sítě s clustery](https://docs.openshift.com/container-platform/4.6/networking/cluster-network-operator.html#nw-cluster-network-operator_cluster-network-operator), [operátor DNS](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)a [operátor](https://docs.openshift.com/container-platform/4.6/networking/ingress-operator.html)příchozího přenosu dat. Další informace o nastavení sítě v clusteru Azure Red Hat OpenShift 4 najdete v [diagramu sítě](concepts-networking.md) a [principech sítě](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html).

### <a name="storage"></a>Storage
Azure Red Hat OpenShift 4 podporuje následující moduly plug-in PersistentVolume:

- AWS elastického blokového úložiště (EBS)
- Disk Azure
- Soubor Azure
- GCE trvalý disk
- HostPath
- iSCSI
- Místní svazek
- NFS
- Úložiště kontejneru Red Hat OpenShift

Informace o konfiguraci těchto typů úložišť najdete v tématu [Konfigurace trvalého úložiště](https://access.redhat.com/documentation/azure_red_hat_openshift/4/html/storage/configuring-persistent-storage).

### <a name="registry"></a>Registr

Azure Red Hat OpenShift 4 dokáže sestavovat image ze zdrojového kódu, nasazovat je a spravovat jejich životní cyklus. Pokud to chcete povolit, Azure Red Hat OpenShift poskytuje 4 [interní integrovaný registr kontejnerů imagí](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html) , který se dá nasadit do prostředí Azure Red Hat OpenShift pro místní správu imagí.

Pokud používáte externí Registry, například [Azure Container Registry](../container-registry/index.yml), [Red Hat Quay Registry](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html#registry-quay-overview_registry-options)nebo [ověřování s povoleným ověřováním Red Hat](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html#registry-authentication-enabled-registry-overview_registry-options), postupujte podle pokynů pro zadání přihlašovacích údajů do clusteru, aby cluster mohl přistupovat k úložištím.

### <a name="monitoring"></a>Sledování

Azure Red Hat OpenShift zahrnuje předem nakonfigurovanou, předem nainstalovanou a samostatnou sadu monitorování, která je založená na Prometheus open source projektu a jeho širším ekosystému. Poskytuje monitorování součástí clusteru a obsahuje sadu výstrah, které okamžitě upozorní správce clusteru na všechny problémy, ke kterým dochází, a na sadu Grafanach řídicích panelů. Zásobník monitorování clusteru se podporuje jenom pro monitorování clusterů Azure Red Hat OpenShift. Další informace najdete v tématu [monitorování clusteru pro Azure Red Hat OpenShift](https://docs.openshift.com/container-platform/4.6/monitoring/understanding-the-monitoring-stack.html).

Pokud jste pro [Azure Red Hat OpenShift 3,11 používali Azure monitor for Containers](../azure-monitor/containers/container-insights-azure-redhat-setup.md), můžete také povolit Azure monitor pro kontejnery pro [clustery Azure Red Hat OpenShift 4](../azure-monitor/containers/container-insights-azure-redhat4-setup.md) a nadále používat stejný Log Analytics pracovní prostor.

## <a name="move-your-dns-or-load-balancer-configuration-to-the-new-cluster"></a>Přesuňte konfiguraci DNS nebo nástroje pro vyrovnávání zatížení do nového clusteru.

Pokud používáte Azure Traffic Manager, přidejte koncové body, které odkazují na váš cílový cluster, a nastavte prioritu těchto koncových bodů.

## <a name="deploy-application-to-your-target-cluster"></a>Nasazení aplikace do cílového clusteru

Jakmile máte cílový cluster správně nakonfigurovaný pro vaše zatížení, [Připojte se ke svému clusteru](tutorial-connect-cluster.md) a vytvořte potřebné aplikace, komponenty nebo služby pro vaše projekty. Azure Red Hat OpenShift vám umožňuje vytvořit tyto položky z Gitu, image kontejnerů, katalog pro vývoj Red Hat, souboru Dockerfile, definici YAML/JSON nebo vybrat databázovou službu z katalogu.

## <a name="delete-your-source-cluster"></a>Odstranění zdrojového clusteru
Jakmile ověříte, že je cluster Azure Red Hat OpenShift 4 správně nastavený, odstraňte cluster Azure Red Hat OpenShift 3,11.

```
az openshift delete --name $CLUSTER_NAME
                    --resource-group $RESOURCE_GROUP
                    [--no-wait]
                    [--subscription]
                    [--yes]
```
## <a name="next-steps"></a>Další kroky
Podívejte se [na dokumentaci k](https://docs.openshift.com/container-platform/4.6/welcome/index.html)Red Hat OpenShift.
