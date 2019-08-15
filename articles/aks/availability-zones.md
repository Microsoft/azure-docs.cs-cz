---
title: Použití Zóny dostupnosti ve službě Azure Kubernetes (AKS)
description: Zjistěte, jak vytvořit cluster, který distribuuje uzly napříč zónami dostupnosti ve službě Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: iainfou
ms.openlocfilehash: 52df1438913983a80ece487b49d0d6bd7821b636
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019440"
---
# <a name="preview---create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Preview – vytvoření clusteru služby Azure Kubernetes (AKS), který používá Zóny dostupnosti

Cluster Azure Kubernetes Service (AKS) distribuuje prostředky, jako jsou uzly a úložiště napříč logickými oddíly základní výpočetní infrastruktury Azure. Tento model nasazení zajišťuje, že se uzly spouštějí napříč samostatnými doménami aktualizace a selhání v jednom datovém centru Azure. Clustery AKS nasazené s tímto výchozím chováním poskytují vysokou úroveň dostupnosti pro ochranu proti selhání hardwaru nebo plánované události údržby.

Pokud chcete zajistit vyšší úroveň dostupnosti pro vaše aplikace, clustery AKS se dají distribuovat napříč zónami dostupnosti. Tyto zóny jsou fyzicky oddělená datacentry v dané oblasti. Pokud jsou komponenty clusteru distribuované napříč několika zónami, může cluster AKS tolerovat selhání v jedné z těchto zón. Vaše aplikace a operace správy jsou dál dostupné i v případě, že je v jednom datovém centru nějaký problém.

V tomto článku se dozvíte, jak vytvořit cluster AKS a distribuovat součásti uzlu napříč zónami dostupnosti. Tato funkce je aktuálně ve verzi Preview.

> [!IMPORTANT]
> Funkce služby AKS ve verzi Preview jsou samoobslužné a výslovný souhlas. Jsou k dispozici za účelem shromažďování názorů a chyb od naší komunity. Ve verzi Preview nejsou tyto funkce určeny pro použití v produkčním prostředí. Funkce ve verzi Public Preview spadají pod podporu nejlepšího úsilí. Pomoc od týmů technické podpory AKS je k dispozici pouze během pracovní doby tichomořského časového pásma (PST). Další informace najdete v následujících článcích podpory:
>
> * [Zásady podpory AKS][aks-support-policies]
> * [Nejčastější dotazy k podpoře Azure][aks-faq]

## <a name="before-you-begin"></a>Před zahájením

Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.0.66 nebo novější. Verzi `az --version` zjistíte spuštěním. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Nainstalovat rozšíření CLI AKS-Preview

K vytváření clusterů AKS, které používají zóny dostupnosti, potřebujete rozšíření *AKS-Preview* CLI 0.4.1 nebo vyšší verze. Nainstalujte rozšíření Azure CLI *AKS-Preview* pomocí příkazu [AZ Extension Add][az-extension-add] a potom zkontrolujte všechny dostupné aktualizace pomocí příkazu [AZ Extension Update][az-extension-update] ::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flags-for-your-subscription"></a>Registrace příznaků funkcí pro vaše předplatné

Pokud chcete vytvořit cluster AKS, který má zóny dostupnosti, nejdřív u vašeho předplatného povolte některé příznaky funkcí. Clustery používají pro správu nasazení a konfigurace uzlů Kubernetes sadu škálování virtuálního počítače. K zajištění odolnosti síťových komponent pro směrování provozu do vašeho clusteru se vyžaduje taky *standardní* SKU nástroje pro vyrovnávání zatížení Azure. Pomocí příkazu [AZ Feature Register][az-feature-register] Zaregistrujte příznaky funkcí *AvailabilityZonePreview*, *AKSAzureStandardLoadBalancer*a *VMSSPreview* , jak je znázorněno v následujícím příkladu:

> [!CAUTION]
> Když zaregistrujete funkci v rámci předplatného, nemůžete tuto funkci v tuto chvíli zrušit. Po povolení některých funkcí verze Preview se můžou použít výchozí hodnoty pro všechny clustery AKS vytvořené v rámci předplatného. Nepovolujte funkce ve verzi Preview u produkčních předplatných. Použijte samostatné předplatné k testování funkcí ve verzi Preview a získejte zpětnou vazbu.

```azurecli-interactive
az feature register --name AvailabilityZonePreview --namespace Microsoft.ContainerService
az feature register --name AKSAzureStandardLoadBalancer --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

Zobrazení stavu v *registraci*trvá několik minut. Stav registrace můžete zjistit pomocí příkazu [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AvailabilityZonePreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Až budete připraveni, aktualizujte registraci poskytovatele prostředků *Microsoft. ContainerService* pomocí příkazu [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations-and-region-availability"></a>Omezení a dostupnost oblasti

Clustery AKS se teď dají vytvářet pomocí zón dostupnosti v následujících oblastech:

* Východní USA 2
* Severní Evropa
* Jihovýchodní Asie
* Západní Evropa
* USA – západ 2

Při vytváření clusteru AKS pomocí zón dostupnosti platí následující omezení:

* Zóny dostupnosti můžete povolit jenom při vytváření clusteru.
* Po vytvoření clusteru se nastavení zóny dostupnosti nedá aktualizovat. Nemůžete také aktualizovat existující cluster zóny bez dostupnosti, aby bylo možné používat zóny dostupnosti.
* Po vytvoření se zóny dostupnosti pro cluster AKS nedají vypnout.
* Vybraná velikost uzlu (SKU virtuálního počítače) musí být dostupná ve všech zónách dostupnosti.
* Clustery s povolenými zónami dostupnosti vyžadují pro distribuci mezi zónami použití služby Azure Load Balancer úrovně Standard.
* Chcete-li nasadit standardní nástroje pro vyrovnávání zatížení, musíte použít Kubernetes verze 1.13.5 nebo vyšší.

Clustery AKS, které používají zóny dostupnosti, musí používat *standardní* SKU nástroje pro vyrovnávání zatížení Azure. Výchozí *základní* skladová položka služby Vyrovnávání zatížení Azure nepodporuje distribuci mezi zónami dostupnosti. Další informace a omezení standardního nástroje pro vyrovnávání zatížení najdete v tématu [omezení služby Azure Load Balancer úrovně Standard ve verzi Preview][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Omezení disků Azure

Svazky, které používají službu Azure Managed disks, nejsou aktuálně rozpracovatelné prostředky. Lusky Přeplánované v jiné zóně z původní zóny se nemůžou znovu připojit k předchozím diskům. Doporučuje se spouštět bezstavové úlohy, které nevyžadují trvalé úložiště, které se může nacházet v případě problémů s oblastmi.

Pokud musíte spustit stavové úlohy, pomocí specifikací v poli pod a jejich tolerování sdělte nástroji Scheduler Kubernetes, aby vytvořil lusky ve stejné zóně jako vaše disky. Alternativně můžete použít síťové úložiště, například soubory Azure, které se mohou připojit k luskům při jejich plánování mezi zónami.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Přehled Zóny dostupnosti pro clustery AKS

Zóny dostupnosti je nabídka s vysokou dostupností, která chrání vaše aplikace a data při selhání datacentra. Zóny jsou jedinečné fyzické umístění v oblasti Azure. Každá zóna se skládá z jednoho nebo více datových Center vybavených nezávislým napájením, chlazením a sítí. Aby se zajistila odolnost, existuje minimálně tři samostatné zóny ve všech povolených oblastech. Fyzické oddělení Zóny dostupnosti v rámci oblasti chrání aplikace a data před selháními datových center. Redundantní služby v zóně replikují aplikace a data napříč Zóny dostupnosti, aby se chránily před jednotlivými chybami.

Další informace najdete v tématu [co je zóny dostupnosti v Azure?][az-overview].

Clustery AKS, které jsou nasazené pomocí zón dostupnosti, můžou distribuovat uzly napříč několika zónami v rámci jedné oblasti. Například cluster v oblasti *východní USA 2* může vytvářet uzly ve všech třech zónách dostupnosti v *východní USA 2*. Tato distribuce prostředků clusteru AKS vylepšuje dostupnost clusteru, protože je odolná vůči selhání konkrétní zóny.

![Distribuce uzlů AKS napříč zónami dostupnosti](media/availability-zones/aks-availability-zones.png)

V případě výpadku zóny se uzly dají znovu vyrovnávat ručně nebo pomocí automatického škálování clusteru. Pokud se jedna zóna stane nedostupnou, vaše aplikace se budou dál spouštět.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Vytvoření clusteru AKS napříč zónami dostupnosti

Když vytvoříte cluster pomocí příkazu [AZ AKS Create][az-aks-create] , určí `--node-zones` parametr, do kterého se nasadí uzly agenta. Komponenty roviny ovládacího prvku AKS pro váš cluster jsou rozloženy i mezi zóny v nejvyšší dostupné konfiguraci při vytváření clusteru, který určuje `--node-zones` parametr.

Pokud při vytváření clusteru AKS nedefinujete žádné zóny pro výchozí fond agentů, nepoužijí komponenty řízení AKS pro váš cluster zóny dostupnosti. Přidáním dalších fondů uzlů (v současnosti ve verzi Preview v AKS) můžete přidat příkaz [AZ AKS nodepool Add][az-aks-nodepool-add] a zadat `--node-zones` pro tyto nové uzly agentů, ale komponenty roviny ovládacího prvku zůstanou bez vědomí zóny dostupnosti. Po nasazení nemůžete změnit povědomí o zónách pro fond uzlů nebo AKS součásti ovládacího prvku.

Následující příklad vytvoří cluster AKS s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*. Vytvoří se celkem *3* uzlů – jeden agent v zóně *1*, jeden v *2*a potom jeden ve *3*. Komponenty roviny ovládacího prvku AKS jsou také distribuovány mezi zóny v nejvyšší dostupné konfiguraci, protože jsou definovány jako součást procesu vytváření clusteru.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --enable-vmss \
    --load-balancer-sku standard \
    --node-count 3 \
    --node-zones 1 2 3
```

Vytvoření clusteru AKS trvá několik minut.

## <a name="verify-node-distribution-across-zones"></a>Ověření distribuce uzlu mezi zónami

Až bude cluster připravený, zobrazte seznam uzlů agentů v sadě škálování, abyste viděli, do jaké zóny dostupnosti se nasazují.

Nejdřív Získejte přihlašovací údaje clusteru AKS pomocí příkazu [AZ AKS Get-Credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

V dalším kroku pomocí příkazu [kubectl popsat][kubectl-describe] seznam uzlů v clusteru. Vyfiltrujte hodnotu *Failure-Domain.beta.Kubernetes.IO/Zone* , jak je znázorněno v následujícím příkladu:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

Následující příklad výstupu ukazuje tři uzly distribuované v rámci zadané oblasti a zón dostupnosti, jako je *eastus2-1* pro první zónu dostupnosti a *eastus2-2* pro druhou zónu dostupnosti:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Při přidávání dalších uzlů do fondu agentů platforma Azure automaticky distribuuje příslušné virtuální počítače v rámci zadané zóny dostupnosti.

## <a name="next-steps"></a>Další postup

V tomto článku se dozvíte, jak vytvořit cluster AKS, který používá zóny dostupnosti. Další informace o clusterech s vysokou dostupností najdete v tématu [osvědčené postupy pro kontinuitu podnikových procesů a zotavení po havárii v AKS][best-practices-bc-dr].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
