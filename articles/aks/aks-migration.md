---
title: Migrace do služby Azure Kubernetes (AKS)
description: Migrujte do služby Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 8315560c679f9807715af14dc315fa3000be0472
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624810"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migrace do služby Azure Kubernetes (AKS)

Tento článek vám pomůže naplánovat a provést úspěšnou migraci do služby Azure Kubernetes Service (AKS). Chcete-li pomoci při klíčových rozhodnutích, tato příručka obsahuje podrobnosti o aktuální doporučené konfiguraci pro AKS. Tento článek nezahrnuje všechny scénáře a v případě potřeby obsahuje odkazy na podrobnější informace pro plánování úspěšné migrace.

Tento dokument lze použít k podpoře následujících scénářů:

* Migrace clusteru AKS podporovaného [sadami dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) na [škálovací sady virtuálních strojů](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)
* Migrace clusteru AKS za účelem použití [standardního účetního čítače skladového úbytku](https://docs.microsoft.com/azure/aks/load-balancer-standard)
* Migrace z [Azure Container Service (ACS) – odchod do důchodu 31.](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/)
* Migrace z [motoru AKS](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) na AKS
* Migrace ze clusterů Kubernetes, které nejsou založeny na Azure, na AKS

Při migraci se ujistěte, že vaše cílová verze Kubernetes je v podporovaném okně pro AKS. Pokud používáte starší verzi, nemusí být v podporovaném rozsahu a vyžadují inovaci verzí, které mají být podporovány AKS. Další informace naleznete v [podporovaných verzích Kubernetes podporovaných AKS.](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions)

Pokud migrujete na novější verzi Kubernetes, přečtěte si [zásady podpory verze Kubernetes a verze](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions).

S migrací může v závislosti na vašem scénáři pomoci několik nástrojů s otevřeným zdrojovým kódem:

* [Velero](https://velero.io/) (Vyžaduje Kubernetes 1,7+)
* [Rozšíření příkazového příkazu KOnI Azure Kube](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

V tomto článku shrneme podrobnosti o migraci pro:

> [!div class="checklist"]
> * AKS se standardním nástrojem pro vyrovnávání zatížení a škálovacími sadami virtuálních strojů
> * Existující připojené služby Azure
> * Zajistit platné kvóty
> * Vysoká dostupnost a kontinuita provozu
> * Důležité informace pro aplikace bez stavové
> * Důležité informace o stavových aplikacích
> * Nasazení konfigurace clusteru

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS se standardním nástrojem pro vyrovnávání zatížení a škálovacími sadami virtuálních strojů

AKS je spravovaná služba nabízející jedinečné funkce s nižší režií na správu. V důsledku spravované služby je nutné vybrat ze sady [oblastí,](https://docs.microsoft.com/azure/aks/quotas-skus-regions) které Podporuje AKS. Přechod z existujícího clusteru na AKS může vyžadovat úpravu stávajících aplikací, aby zůstaly v pořádku v rovině spravovaného řídicího systému AKS.

Doporučujeme používat clustery AKS podporované [škálovacími sadami virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machine-scale-sets) a [nástroj pro vyrovnávání zatížení standardu Azure,](https://docs.microsoft.com/azure/aks/load-balancer-standard) abyste zajistili, že budete mít k dispozici funkce, jako jsou [fondy více uzlů](https://docs.microsoft.com/azure/aks/use-multiple-node-pools), zóny [dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview), [autorizované rozsahy IP adres](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges), automatické [škálování clusterů](https://docs.microsoft.com/azure/aks/cluster-autoscaler), [zásady Azure pro AKS](https://docs.microsoft.com/azure/governance/policy/concepts/rego-for-aks)a další nové funkce při jejich vydání.

Clustery AKS podporované [sadami dostupnosti virtuálních strojů](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) postrádají podporu pro mnoho z těchto funkcí.

Následující příklad vytvoří cluster AKS s fondem jednoho uzlu podporovanýškálovací sadou virtuálních strojů. Používá standardní vyrovnávání zatížení. Umožňuje také automatické škálování clusteru ve fondu uzlů pro cluster a nastavuje minimálně *1* a maximálně *3* uzly:

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

Při migraci clusterů jste možná připojili externí služby Azure. Ty nevyžadují obnovení prostředků, ale budou vyžadovat aktualizaci připojení z předchozích na nové clustery k udržení funkčnosti.

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* Účet úložiště
* Externí databáze

## <a name="ensure-valid-quotas"></a>Zajistit platné kvóty

Vzhledem k tomu, že další virtuální počítače budou nasazeny do vašeho předplatného během migrace, měli byste ověřit, zda jsou kvóty a limity pro tyto prostředky dostatečné. Možná budete muset požádat o zvýšení [kvóty virtuálního procesoru](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).

Možná budete muset požádat o zvýšení [kvót sítě,](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) abyste zajistili, že nevyčerpáte IP adresy. Další informace naleznete [v oblasti sítí a IP adres pro AKS.](https://docs.microsoft.com/azure/aks/configure-kubenet)

Další informace najdete v tématu [Limity předplatného azure a služeb](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Pokud chcete zkontrolovat aktuální kvóty, přejděte na portálu Azure do [okna předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), vyberte předplatné a pak vyberte **Využití + kvóty**.

## <a name="high-availability-and-business-continuity"></a>Vysoká dostupnost a kontinuita provozu

Pokud vaše aplikace nemůže zpracovat prostoje, budete muset dodržovat osvědčené postupy pro scénáře migrace s vysokou dostupností.  Osvědčené postupy pro komplexní plánování kontinuity provozu, zotavení po havárii a maximalizaci provozujsou nad rámec tohoto dokumentu.  Další informace najdete v [doporučených postupech pro kontinuitu podnikání a zotavení po havárii ve službě Azure Kubernetes Service (AKS).](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)

U složitých aplikací budete obvykle migrovat v průběhu času, nikoli všechny najednou. To znamená, že staré a nové prostředí může být nutné komunikovat v síti. Aplikace, které `ClusterIP` dříve používaly služby ke `LoadBalancer` komunikaci, mohou být vystaveny jako typ a odpovídajícím způsobem zabezpečeny.

Chcete-li dokončit migraci, budete chtít nasměrovat klienty na nové služby, které jsou spuštěny na AKS. Doporučujeme přesměrovat provoz aktualizací služby DNS tak, aby přeczaovala na nástroj pro vyrovnávání zatížení, který je přímo před clusterem AKS.

[Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/) může nasměrovat zákazníky na požadovaný cluster Kubernetes a instanci aplikace.  Traffic Manager je správce zatížení založený na službě DNS, který může distribuovat síťový provoz mezi oblastmi.  Chcete-li dosáhnout nejlepšího výkonu a redundance, nasměrujte veškerý provoz aplikací prostřednictvím Traffic Manageru před tím, než přejde do clusteru AKS.  V nasazení s více clustery by se zákazníci měli připojit k názvu DNS Traffic Manageru, který odkazuje na služby v každém clusteru AKS. Definujte tyto služby pomocí koncových bodů Traffic Manageru. Každý koncový bod je *IP služby pro vyrovnávání zatížení*. Tato konfigurace slouží k přímému síťovému provozu z koncového bodu Traffic Manager u jedné oblasti do koncového bodu v jiné oblasti.

![AKS s Traffic Managerem](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Služba Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) je další možností pro směrování provozu pro clustery AKS.  Služba Azure Front Door vám umožňuje definovat, spravovat a monitorovat globální směrování webového provozu tím, že provádí optimalizaci pro nejlepší výkon a poskytuje okamžité globální převzetí služeb při selhání, aby byla zajištěna vysoká dostupnost. 

### <a name="considerations-for-stateless-applications"></a>Důležité informace pro aplikace bez stavové

Bezstavová migrace aplikací je nejjednodušší případ. Použijte definice prostředků (YAML nebo Helm) na nový cluster, ujistěte se, že vše funguje podle očekávání a přesměrovat provoz pro aktivaci nového clusteru.

### <a name="considerations-for-stateful-applications"></a>Důležité informace o stavových aplikacích

Pečlivě naplánujte migraci stavových aplikací, abyste zabránili ztrátě dat nebo neočekávaným výpadkům.

Pokud používáte Soubory Azure, můžete připojit sdílenou složku jako svazek do nového clusteru:
* [Připojení statických souborů Azure jako svazku](https://docs.microsoft.com/azure/aks/azure-files-volume#mount-the-file-share-as-a-volume)

Pokud používáte spravované disky Azure, můžete disk připojit jenom v případě, že není připojený k libovolnému virtuálnímu počítači:
* [Připojení statického disku Azure jako svazku](https://docs.microsoft.com/azure/aks/azure-disk-volume#mount-disk-as-volume)

Pokud ani jeden z těchto přístupů nefunguje, můžete použít možnosti zálohování a obnovení:
* [Velero v Azure](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)

#### <a name="azure-files"></a>Soubory Azure

Na rozdíl od disků lze soubory Azure připojit k více hostitelům současně. V clusteru AKS Vám Azure a Kubernetes nezabrání ve vytváření podu, který váš cluster ACS stále používá. Chcete-li zabránit ztrátě dat a neočekávanému chování, zajistěte, aby clustery nezapisují do stejných souborů současně.

Pokud vaše aplikace může hostovat více replik, které odkazují na stejnou sdílenou složku, postupujte podle bezstavových kroků migrace a nasaďte definice YAML do nového clusteru. Pokud tomu tak není, jeden možný přístup k migraci zahrnuje následující kroky:

* Ověřte, zda aplikace pracuje správně.
* Namiřte svůj živý provoz na nový cluster AKS.
* Odpojte starý cluster.

Pokud chcete začít s prázdnou sdílenou položkou a vytvořit kopii zdrojových dat, můžete pomocí [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) příkazů migrovat data.


#### <a name="migrating-persistent-volumes"></a>Migrace trvalých svazků

Pokud migrujete existující trvalé svazky do AKS, obvykle postupujte takto:

* Quiesce zapíše do aplikace. (Tento krok je volitelný a vyžaduje prostoje.)
* Pořizují snímky disků.
* Vytvořte nové spravované disky ze snímků.
* Vytvořte trvalé svazky v AKS.
* Aktualizujte specifikace podu [použít existující svazky](https://docs.microsoft.com/azure/aks/azure-disk-volume) spíše než PersistentVolumeClaims (statické zřizování).
* Nasaďte aplikaci do AKS.
* Ověřte, zda aplikace pracuje správně.
* Namiřte svůj živý provoz na nový cluster AKS.

> [!IMPORTANT]
> Pokud se rozhodnete neutišit zápisy do nepořádku, budete muset replikovat data do nového nasazení. V opačném případě vám budou chybět data, která byla zapsána po pořízení snímků disku.

Některé nástroje s otevřeným zdrojovým kódem vám mohou pomoci vytvářet spravované disky a migrovat svazky mezi clustery Kubernetes:

* [Rozšíření Azure CLI Disk Copy](https://github.com/noelbundick/azure-cli-disk-copy-extension) kopíruje a převádí disky napříč skupinami prostředků a oblastmi Azure.
* [Rozšíření Azure Kube CLI](https://github.com/yaron2/azure-kube-cli) vyjmenovává svazky Kubernetes a migruje je do clusteru AKS.


### <a name="deployment-of-your-cluster-configuration"></a>Nasazení konfigurace clusteru

Doporučujeme použít existující kanál průběžné integrace (CI) a průběžné doručování (CD) k nasazení konfigurace považované za funkční do AKS. Azure Pipelines můžete použít k [vytváření a nasazování aplikací do AKS](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/kubernetes/aks-template?view=azure-devops). Klonujte stávající úlohy nasazení `kubeconfig` a ujistěte se, že odkazuje na nový cluster AKS.

Pokud to není možné, exportujte definice prostředků z existujícího clusteru Kubernetes a pak je použijte na AKS. Můžete použít `kubectl` k exportu objektů.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Přesunutí existujících zdrojů do jiné oblasti

Můžete chtít přesunout cluster AKS do [jiné oblasti podporované AKS][region-availability]. Doporučujeme vytvořit nový cluster v jiné oblasti a pak nasadit prostředky a aplikace do nového clusteru. Kromě toho pokud máte nějaké služby, jako je Azure [Dev Spaces spuštěné][azure-dev-spaces] v clusteru AKS, budete také muset nainstalovat a nakonfigurovat tyto služby v clusteru v nové oblasti.


V tomto článku jsme shrnuli podrobnosti o migraci pro:

> [!div class="checklist"]
> * AKS se standardním nástrojem pro vyrovnávání zatížení a škálovacími sadami virtuálních strojů
> * Existující připojené služby Azure
> * Zajistit platné kvóty
> * Vysoká dostupnost a kontinuita provozu
> * Důležité informace pro aplikace bez stavové
> * Důležité informace o stavových aplikacích
> * Nasazení konfigurace clusteru


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/