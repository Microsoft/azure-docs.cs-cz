---
title: Migrace do služby Azure Kubernetes (AKS)
description: Migrujte do služby Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 8315560c679f9807715af14dc315fa3000be0472
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624810"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migrace do služby Azure Kubernetes (AKS)

Tento článek vám pomůže s plánováním a spuštěním úspěšné migrace do služby Azure Kubernetes Service (AKS). V této příručce najdete podrobné informace o aktuálně Doporučené konfiguraci pro AKS, která vám pomůže při rozhodování. Tento článek se nezabývá každým scénářem a v případě potřeby obsahuje odkazy na podrobnější informace pro plánování úspěšné migrace.

Tento dokument se dá použít k podpoře následujících scénářů:

* Migrace clusteru AKS s využitím [skupin dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) na [Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)
* Migrace clusteru AKS na použití [standardního nástroje pro vyrovnávání zatížení SKU](https://docs.microsoft.com/azure/aks/load-balancer-standard)
* Migrace z [Azure Container Service (ACS) – vyřazení z 31. ledna 2020](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) do AKS
* Migrace z [modulu AKS](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) na AKS
* Migrace z clusterů Kubernetes založených na jiných než Azure na AKS

Při migraci zajistěte, aby vaše cílová verze Kubernetes byla v podporovaném okně pro AKS. Pokud používáte starší verzi, nemusí být v podporovaném rozsahu a vyžadovat, aby byly upgradované verze AKS podporovány. Další informace najdete v tématu [podporované verze Kubernetes pro AKS](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions) .

Pokud migrujete na novější verzi Kubernetes, zkontrolujte [zásady podpory pro verzi Kubernetes a zkosit](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions).

Vaše migrace může pomáhat několik open-source nástrojů v závislosti na vašem scénáři:

* [Velero](https://velero.io/) (vyžaduje Kubernetes 1.7 +)
* [Rozšíření CLI Azure Kube](https://github.com/yaron2/azure-kube-cli)
* [Posunutí](https://github.com/mhausenblas/reshifter)

V tomto článku shrnujeme podrobnosti o migraci pro:

> [!div class="checklist"]
> * AKS s Standard Load Balancer a Virtual Machine Scale Sets
> * Existující připojené služby Azure
> * Zajištění platných kvót
> * Vysoká dostupnost a provozní kontinuita
> * Požadavky na bezstavové aplikace
> * Požadavky na stavové aplikace
> * Nasazení konfigurace clusteru

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS s Standard Load Balancer a Virtual Machine Scale Sets

AKS je spravovaná služba, která nabízí jedinečné možnosti s nižšími nároky na správu. V důsledku toho, že se jedná o spravovanou službu, musíte vybrat ze sady [oblastí](https://docs.microsoft.com/azure/aks/quotas-skus-regions) , které podporuje AKS. Přechod ze stávajícího clusteru do AKS může vyžadovat úpravu stávajících aplikací, aby zůstaly v dobrém stavu na rovině spravovaného řízení AKS.

Pro zajištění funkcí, jako je [více fondů uzlů](https://docs.microsoft.com/azure/aks/use-multiple-node-pools), [zóny dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview), [autorizovaných rozsahů IP adres](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges), [automatického škálování clusteru](https://docs.microsoft.com/azure/aks/cluster-autoscaler), [Azure Policy pro AKS](https://docs.microsoft.com/azure/governance/policy/concepts/rego-for-aks)a dalších nových funkcí, doporučujeme používat clustery AKS, které jsou založené na [Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets) a [Azure Standard Load Balancer](https://docs.microsoft.com/azure/aks/load-balancer-standard) .

AKS clustery zajištěné [sadami dostupnosti virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) neobsahují podporu pro mnohé z těchto funkcí.

Následující příklad vytvoří cluster AKS s jediným fondem uzlů zálohovaným sadou škálování virtuálního počítače. Používá standardní nástroj pro vyrovnávání zatížení. Povoluje taky automatické škálování clusteru ve fondu uzlů pro cluster a nastavuje minimálně *1* a maximálně *3* uzly:

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

Při migraci clusterů máte pravděpodobně připojené externí služby Azure. Ty nevyžadují opětovné vytvoření prostředků, ale budou vyžadovat aktualizaci připojení z předchozích na nové clustery, aby bylo možné zachovat funkčnost.

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* Účet úložiště
* Externí databáze

## <a name="ensure-valid-quotas"></a>Zajištění platných kvót

Vzhledem k tomu, že během migrace budou do svého předplatného nasazené další virtuální počítače, měli byste ověřit, že vaše kvóty a limity jsou pro tyto prostředky dostatečné. Možná budete muset požádat o zvýšení [kvóty vCPU](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).

Možná budete muset požádat o zvýšení [kvót sítě](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) , abyste se ujistili, že nevyčerpáte IP adresy. Další informace najdete v tématu [sítě a rozsahy IP adres pro AKS](https://docs.microsoft.com/azure/aks/configure-kubenet) .

Další informace najdete v tématu [omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Pokud chcete zjistit aktuální kvóty, v Azure Portal v okně pro [předplatné](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)vyberte své předplatné a pak vyberte **využití + kvóty**.

## <a name="high-availability-and-business-continuity"></a>Vysoká dostupnost a provozní kontinuita

Pokud vaše aplikace nemůže zvládnout výpadky, budete muset dodržovat osvědčené postupy pro scénáře migrace s vysokou dostupností.  Osvědčené postupy pro komplexní plánování kontinuity podnikových procesů, zotavení po havárii a maximalizaci doby provozu jsou nad rámec tohoto dokumentu.  Další informace o [osvědčených postupech pro zajištění kontinuity podnikových procesů a zotavení po havárii ve službě Azure Kubernetes Service (AKS) získáte v článku o osvědčených postupech](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) .

U složitých aplikací se obvykle provádí migrace v čase, nikoli najednou. To znamená, že stará a nová prostředí můžou potřebovat komunikovat přes síť. Aplikace, které dřív používaly `ClusterIP` služby ke komunikaci, můžou být nutné vystavit jako `LoadBalancer` typu a správně zabezpečit.

K dokončení migrace budete chtít klienty nasměrovat na nové služby, které běží na AKS. Pro přesměrování provozu doporučujeme, abyste provedli aktualizaci DNS tak, aby odkazovaly na Load Balancer, která je umístěná před clusterem AKS.

Služba [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) může směrovat zákazníky na požadovaný cluster Kubernetes a instanci aplikace.  Traffic Manager je nástroj pro vyrovnávání zatížení pro provoz založený na DNS, který může distribuovat síťový provoz napříč oblastmi.  Abyste dosáhli nejlepšího výkonu a redundance, přesměrujte veškerý provoz aplikací prostřednictvím Traffic Manager před tím, než přejdete do clusteru AKS.  V nasazení s více clustery by se zákazníci měli připojit k Traffic Manager názvu DNS, který odkazuje na služby na jednotlivých clusterech AKS. Tyto služby definujte pomocí koncových bodů Traffic Manager. Každý koncový bod je *IP adresa nástroje pro vyrovnávání zatížení služby*. Pomocí této konfigurace můžete směrovat síťový provoz z Traffic Manager koncového bodu v jedné oblasti do koncového bodu v jiné oblasti.

![AKS s Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Služba front-dveří Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview) je další možností pro směrování provozu pro clustery AKS.  Služba Azure Front Door vám umožňuje definovat, spravovat a monitorovat globální směrování webového provozu tím, že provádí optimalizaci pro nejlepší výkon a poskytuje okamžité globální převzetí služeb při selhání, aby byla zajištěna vysoká dostupnost. 

### <a name="considerations-for-stateless-applications"></a>Požadavky na bezstavové aplikace

Nestavová migrace aplikace je nejjednodušším případem. Použijte definice prostředků (YAML nebo Helm) na nový cluster, ujistěte se, že vše funguje podle očekávání, a přesměrujte provoz na aktivaci nového clusteru.

### <a name="considerations-for-stateful-applications"></a>Požadavky na stavové aplikace

Pečlivě naplánujte migraci stavových aplikací, abyste se vyhnuli ztrátě dat nebo neočekávanému výpadku.

Pokud používáte soubory Azure, můžete sdílenou složku připojit jako svazek do nového clusteru:
* [Připojit statické soubory Azure jako svazek](https://docs.microsoft.com/azure/aks/azure-files-volume#mount-the-file-share-as-a-volume)

Pokud používáte Azure Managed Disks, můžete disk připojit pouze v případě, že je nepřipojen k žádnému virtuálnímu počítači:
* [Připojit statický disk Azure jako svazek](https://docs.microsoft.com/azure/aks/azure-disk-volume#mount-disk-as-volume)

Pokud ani jeden z těchto přístupů nefunguje, můžete použít možnosti zálohování a obnovení:
* [Velero v Azure](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)

#### <a name="azure-files"></a>Soubory Azure

Na rozdíl od disků je možné soubory Azure připojit k více hostitelům současně. V clusteru AKS vám Azure a Kubernetes nebrání v vytváření pod tím, že váš cluster ACS stále používá. Aby nedošlo ke ztrátě dat a neočekávanému chování, zajistěte, aby clustery nepsaly do stejných souborů současně.

Pokud vaše aplikace může hostovat několik replik, které odkazují na stejnou sdílenou složku, postupujte podle kroků migrace bez stavů a nasaďte definice YAML do nového clusteru. Pokud ne, jeden z možných způsobů migrace zahrnuje následující kroky:

* Ověřte, že aplikace funguje správně.
* Nasměrujte svůj živý provoz do nového clusteru AKS.
* Odpojte původní cluster.

Pokud chcete začít s prázdnou sdílenou složkou a vytvořit kopii zdrojových dat, můžete k migraci dat použít [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) příkazy.


#### <a name="migrating-persistent-volumes"></a>Migrace trvalých svazků

Pokud migrujete existující trvalé svazky na AKS, obecně postupujte podle těchto kroků:

* Neuvést do stavu zápisy do aplikace. (Tento krok je nepovinný a vyžaduje výpadek.)
* Pořizovat snímky disků.
* Vytvořte nové spravované disky ze snímků.
* Vytvořte v AKS trvalé svazky.
* V případě, že chcete [použít existující svazky](https://docs.microsoft.com/azure/aks/azure-disk-volume) místo PersistentVolumeClaims (statické zřizování), aktualizujte specifikace pod.
* Nasaďte aplikaci do AKS.
* Ověřte, že aplikace funguje správně.
* Nasměrujte svůj živý provoz do nového clusteru AKS.

> [!IMPORTANT]
> Pokud se rozhodnete neuvést zápisy do nečinnosti, bude nutné replikovat data do nového nasazení. V opačném případě nebudete mít data, která byla napsána po provedení snímků disku.

Některé open source nástroje vám pomůžou vytvořit spravované disky a migrovat svazky mezi Kubernetes clustery:

* [Rozšíření kopírování disku Azure CLI](https://github.com/noelbundick/azure-cli-disk-copy-extension) : zkopíruje a převede disky mezi skupinami prostředků a oblastmi Azure.
* [Rozšíření Azure Kube CLI](https://github.com/yaron2/azure-kube-cli) vyčísluje svazky Kubernetes ACS a migruje je do clusteru AKS.


### <a name="deployment-of-your-cluster-configuration"></a>Nasazení konfigurace clusteru

Doporučujeme použít stávající kanál průběžné integrace (CI) a průběžné doručování (CD) k nasazení známé konfigurace do AKS. K [sestavování a nasazování aplikací do AKS](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/kubernetes/aks-template?view=azure-devops)můžete použít Azure Pipelines. Naklonujte stávající úlohy nasazení a zajistěte, aby `kubeconfig` odkazovaly na nový cluster AKS.

Pokud to není možné, exportujte definice prostředků ze stávajícího clusteru Kubernetes a pak je použijte na AKS. K exportu objektů můžete použít `kubectl`.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Přesun existujících prostředků do jiné oblasti

Cluster AKS můžete chtít přesunout do jiné oblasti, kterou [podporuje AKS][region-availability]. Doporučujeme vytvořit nový cluster v jiné oblasti a potom nasadit prostředky a aplikace do nového clusteru. Navíc pokud máte v clusteru AKS spuštěné nějaké služby, jako je třeba [Azure dev Spaces][azure-dev-spaces] , budete muset tyto služby ve svém clusteru nainstalovat a nakonfigurovat i v nové oblasti.


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
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/