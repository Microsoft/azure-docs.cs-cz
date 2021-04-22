---
title: Migrace do služby Azure Kubernetes (AKS)
description: Migrujte do služby Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 123f4e5c2442b913a53288602d1c56f199b131a6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872780"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migrace do služby Azure Kubernetes (AKS)

Tato příručka vám pomůže s plánováním a spuštěním úspěšné migrace do služby Azure Kubernetes Service (AKS), která poskytuje podrobné informace o aktuálně Doporučené konfiguraci AKS. I když tento článek nepokrývá každý scénář, obsahuje odkazy na podrobnější informace pro plánování úspěšné migrace.

Tento dokument přispívá k podpoře následujících scénářů:

* Uzavření určité aplikace a migrujte je na AKS pomocí [Azure Migrate](../migrate/migrate-services-overview.md).
* Migrace clusteru AKS s využitím [skupin dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md) na [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md).
* Migrace clusteru AKS na použití [standardního nástroje pro vyrovnávání zatížení SKU](./load-balancer-standard.md)
* Migrace z [Azure Container Service (ACS) – vyřazení z 31. ledna 2020](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) až do AKS.
* Migrace z [AKS Engine](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) na AKS.
* Migrace z clusterů Kubernetes založených na jiných než Azure na AKS.
* Přesun existujících prostředků do jiné oblasti.

Při migraci zajistěte, aby vaše cílová verze Kubernetes byla v podporovaném okně pro AKS. Starší verze nemusí být v podporovaném rozsahu a bude vyžadovat, aby upgrade verze podporoval AKS. Další informace najdete v tématu [AKS podporované verze Kubernetes](./supported-kubernetes-versions.md).

Pokud migrujete na novější verzi Kubernetes, zkontrolujte [zásady podpory pro verzi Kubernetes a zkosit](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions).

Vaše migrace může pomáhat několik open-source nástrojů v závislosti na vašem scénáři:

* [Velero](https://velero.io/) (vyžaduje Kubernetes 1.7 +)
* [Rozšíření CLI Azure Kube](https://github.com/yaron2/azure-kube-cli)
* [Posunutí](https://github.com/mhausenblas/reshifter)

V tomto článku shrnujeme podrobnosti o migraci pro:

> [!div class="checklist"]
> * Uzavření aplikací prostřednictvím Azure Migrate 
> * AKS s Standard Load Balancer a Virtual Machine Scale Sets
> * Existující připojené služby Azure
> * Zajištění platných kvót
> * Vysoká dostupnost a provozní kontinuita
> * Požadavky na bezstavové aplikace
> * Požadavky na stavové aplikace
> * Nasazení konfigurace clusteru

## <a name="use-azure-migrate-to-migrate-your-applications-to-aks"></a>Použití Azure Migrate k migraci aplikací na AKS

Azure Migrate nabízí sjednocenou platformu pro vyhodnocení a migraci na místní servery, infrastrukturu, aplikace a data Azure. Pro AKS můžete použít Azure Migrate pro následující úlohy:

* [Kontejnerizace ASP.NET aplikace a migrujte na AKS](../migrate/tutorial-containerize-aspnet-kubernetes.md)
* [Kontejnerizace webové aplikace Java a migrovat na AKS](../migrate/tutorial-containerize-java-kubernetes.md)

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS s Standard Load Balancer a Virtual Machine Scale Sets

AKS je spravovaná služba, která nabízí jedinečné možnosti s nižšími nároky na správu. Vzhledem k tomu, že AKS je spravovaná služba, musíte vybrat ze sady [oblastí](./quotas-skus-regions.md) , které AKS podporuje. Je možné, že budete muset upravit stávající aplikace, aby byly v dobrém stavu v pořádku na rovině ovládacího prvku spravované AKS během přechodu ze stávajícího clusteru do AKS.

Doporučujeme používat AKS clustery, které jsou zajištěné pomocí [Virtual Machine Scale Sets](../virtual-machine-scale-sets/index.yml) a [Azure Standard Load Balancer](./load-balancer-standard.md) , abyste zajistili, že získáte funkce, jako například:
* [Více fondů uzlů](./use-multiple-node-pools.md),
* [Zóny dostupnosti](../availability-zones/az-overview.md),
* [Schválené rozsahy IP adres](./api-server-authorized-ip-ranges.md),
* [Automatické škálování clusteru](./cluster-autoscaler.md),
* [Azure Policy pro AKS](../governance/policy/concepts/policy-for-kubernetes.md)a
* Další nové funkce, které jsou vydány.

AKS clustery zajištěné [sadami dostupnosti virtuálních počítačů](../virtual-machines/availability.md#availability-sets) neobsahují podporu pro mnohé z těchto funkcí.

Následující příklad vytvoří cluster AKS s jediným fondem uzlů, který je zálohovaný sadou škálování virtuálního počítače (VM). Cluster:
* Používá standardní nástroj pro vyrovnávání zatížení. 
* Povolí automatické škálování clusteru ve fondu uzlů pro cluster.
* Nastaví minimálně *1* a maximálně *3* uzlů.

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

## <a name="existing-attached-azure-services"></a>Existující připojené služby Azure

Při migraci clusterů máte pravděpodobně připojené externí služby Azure. I když následující služby nevyžadují opětovné vytvoření prostředků, budou vyžadovat aktualizaci připojení z předchozích na nové clustery, aby bylo možné zachovat funkčnost.

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* Účet úložiště
* Externí databáze

## <a name="ensure-valid-quotas"></a>Zajištění platných kvót

Vzhledem k tomu, že během migrace budou do svého předplatného nasazené jiné virtuální počítače, měli byste ověřit, že vaše kvóty a omezení jsou pro tyto prostředky dostatečné. V případě potřeby si vyžádejte zvýšení [kvóty vCPU](../azure-portal/supportability/per-vm-quota-requests.md).

Možná budete muset požádat o zvýšení [kvót sítě](../azure-portal/supportability/networking-quota-requests.md) , abyste se ujistili, že nevyčerpáte IP adresy. Další informace najdete v tématu [sítě a rozsahy IP adres pro AKS](./configure-kubenet.md).

Další informace najdete v tématu [omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md). Pokud chcete zjistit aktuální kvóty, v Azure Portal v okně pro [předplatné](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)vyberte své předplatné a pak vyberte **využití + kvóty**.

## <a name="high-availability-and-business-continuity"></a>Vysoká dostupnost a provozní kontinuita

Pokud vaše aplikace nemůže zvládnout výpadky, budete muset dodržovat osvědčené postupy pro scénáře migrace s vysokou dostupností. Přečtěte si další informace o [osvědčených postupech pro komplexní plánování kontinuity podnikových procesů, zotavení po havárii a maximalizaci provozuschopnosti ve službě Azure Kubernetes Service (AKS)](./operator-best-practices-multi-region.md).

U složitých aplikací se obvykle provádí migrace v čase, nikoli jenom jednou, což znamená, že stará a nová prostředí můžou potřebovat komunikovat přes síť. Aplikace, které dříve používaly `ClusterIP` služby ke komunikaci, můžou být nutné zveřejnit jako typ `LoadBalancer` a správně zabezpečit.

K dokončení migrace budete chtít klienty nasměrovat na nové služby, které běží na AKS. Doporučujeme, abyste přesměrovali provoz tak, že aktualizujete DNS tak, aby odkazoval na Load Balancer, která se nachází před vaším clusterem AKS.

Služba [Azure Traffic Manager](../traffic-manager/index.yml) může směrovat zákazníky na požadovaný cluster Kubernetes a instanci aplikace. Traffic Manager je nástroj pro vyrovnávání zatížení pro provoz založený na DNS, který může distribuovat síťový provoz napříč oblastmi. Abyste dosáhli nejlepšího výkonu a redundance, přesměrujte veškerý provoz aplikací prostřednictvím Traffic Manager před tím, než přejdete do clusteru AKS. 

V nasazení s více clustery by se zákazníci měli připojit k Traffic Manager názvu DNS, který odkazuje na služby na jednotlivých clusterech AKS. Tyto služby definujte pomocí koncových bodů Traffic Manager. Každý koncový bod je *IP adresa nástroje pro vyrovnávání zatížení služby*. Pomocí této konfigurace můžete směrovat síťový provoz z Traffic Manager koncového bodu v jedné oblasti do koncového bodu v jiné oblasti.

![AKS s Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Služba front-dveří Azure](../frontdoor/front-door-overview.md) je další možností pro směrování provozu pro clustery AKS. Pomocí služby Azure front-dveří můžete definovat, spravovat a monitorovat globální směrování pro webový provoz optimalizací pro nejlepší výkon a rychlé globální převzetí služeb při selhání pro zajištění vysoké dostupnosti. 

### <a name="considerations-for-stateless-applications"></a>Požadavky na bezstavové aplikace

Nestavová migrace aplikace je nejjednodušším případem:
1. Použijte definice prostředků (YAML nebo Helm) pro nový cluster.
1. Ujistěte se, že vše funguje podle očekávání.
1. Přesměrujte provoz tak, aby se aktivoval nový cluster.

### <a name="considerations-for-stateful-applications"></a>Požadavky na stavové aplikace

Pečlivě naplánujte migraci stavových aplikací, abyste se vyhnuli ztrátě dat nebo neočekávanému výpadku.

* Pokud používáte soubory Azure, můžete sdílenou složku připojit jako svazek do nového clusteru. Viz [připojení statických souborů Azure jako svazku](./azure-files-volume.md#mount-file-share-as-an-persistent-volume).
* Pokud používáte Azure Managed Disks, můžete disk připojit pouze v případě, že je nepřipojen k žádnému virtuálnímu počítači. Viz [připojit statický disk Azure jako svazek](./azure-disk-volume.md#mount-disk-as-volume).
* Pokud ani jeden z těchto přístupů nefunguje, můžete použít možnosti zálohování a obnovení. Viz [Velero v Azure](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md).

#### <a name="azure-files"></a>Azure Files

Na rozdíl od disků je možné soubory Azure připojit k více hostitelům současně. V clusteru AKS vám Azure a Kubernetes nebrání v vytváření pod tím, že váš cluster AKS dál používá. Aby nedošlo ke ztrátě dat a neočekávanému chování, zajistěte, aby clustery nepsaly současně do stejných souborů.

Pokud vaše aplikace může hostovat několik replik, které odkazují na stejnou sdílenou složku, postupujte podle kroků migrace bez stavů a nasaďte definice YAML do nového clusteru. 

Pokud ne, jeden z možných způsobů migrace zahrnuje následující kroky:

1. Ověřte, že aplikace funguje správně.
1. Nasměrujte svůj živý provoz do nového clusteru AKS.
1. Odpojte původní cluster.

Pokud chcete začít s prázdnou sdílenou složkou a vytvořit kopii zdrojových dat, můžete [`az storage file copy`](/cli/azure/storage/file/copy) k migraci dat použít příkazy.


#### <a name="migrating-persistent-volumes"></a>Migrace trvalých svazků

Pokud migrujete existující trvalé svazky na AKS, obecně postupujte podle těchto kroků:

1. Neuvést do stavu zápisy do aplikace. 
    * Tento krok je nepovinný a vyžaduje výpadek.
1. Pořizovat snímky disků.
1. Vytvořte nové spravované disky ze snímků.
1. Vytvořte v AKS trvalé svazky.
1. V případě, že chcete [použít existující svazky](./azure-disk-volume.md) místo PersistentVolumeClaims (statické zřizování), aktualizujte specifikace pod.
1. Nasaďte aplikaci do AKS.
1. Ověřte, že aplikace funguje správně.
1. Nasměrujte svůj živý provoz do nového clusteru AKS.

> [!IMPORTANT]
> Pokud se rozhodnete neuvést zápisy do nečinnosti, bude nutné replikovat data do nového nasazení. V opačném případě nebudete mít data, která byla napsána po provedení snímků disku.

Některé open source nástroje vám pomůžou vytvořit spravované disky a migrovat svazky mezi Kubernetes clustery:

* [Rozšíření kopírování disku Azure CLI](https://github.com/noelbundick/azure-cli-disk-copy-extension) : zkopíruje a převede disky mezi skupinami prostředků a oblastmi Azure.
* [Rozšíření Azure Kube CLI](https://github.com/yaron2/azure-kube-cli) vyčísluje svazky Kubernetes ACS a migruje je do clusteru AKS.


### <a name="deployment-of-your-cluster-configuration"></a>Nasazení konfigurace clusteru

Doporučujeme použít stávající kanál průběžné integrace (CI) a průběžné doručování (CD) k nasazení známé konfigurace do AKS. K [sestavování a nasazování aplikací do AKS](/azure/devops/pipelines/ecosystems/kubernetes/aks-template)můžete použít Azure Pipelines. Naklonujte stávající úlohy nasazení a zajistěte, aby `kubeconfig` odkazovaly na nový cluster AKS.

Pokud to není možné, exportujte definice prostředků ze stávajícího clusteru Kubernetes a pak je použijte na AKS. Můžete použít `kubectl` k exportu objektů.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Přesun existujících prostředků do jiné oblasti

Cluster AKS můžete chtít přesunout do jiné oblasti, kterou [podporuje AKS][region-availability]. Doporučujeme vytvořit nový cluster v jiné oblasti a potom nasadit prostředky a aplikace do nového clusteru. 

Navíc pokud máte v clusteru AKS spuštěné nějaké služby, jako je třeba [Azure dev Spaces][azure-dev-spaces] , budete muset tyto služby nainstalovat a nakonfigurovat v rámci svého clusteru v nové oblasti.


V tomto článku jsme shrnuti podrobnosti o migraci pro:

> [!div class="checklist"]
> * AKS s Standard Load Balancer a Virtual Machine Scale Sets
> * Existující připojené služby Azure
> * Zajištění platných kvót
> * Vysoká dostupnost a provozní kontinuita
> * Požadavky na bezstavové aplikace
> * Požadavky na stavové aplikace
> * Nasazení konfigurace clusteru


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: ../dev-spaces/index.yml
