---
title: Použití zón dostupnosti ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak vytvořit cluster, který distribuuje uzlů napříč zónami dostupnosti ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: iainfou
ms.openlocfilehash: 0f99386aa9eeb75a990507e383c32412fb39eceb
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840677"
---
# <a name="preview---create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Ve verzi Preview: vytvoření clusteru služby Azure Kubernetes Service (AKS), který používá zóny dostupnosti

Cluster Azure Kubernetes Service (AKS) distribuuje prostředky, jako uzly a úložiště v rámci logických částí Azure základní výpočetní infrastruktury. Tento model nasazení zajišťuje, že v uzlech je spuštěný v samostatné aktualizace domén selhání v datovém centru Azure. AKS clusterech nasazených s toto výchozí chování poskytovat vysokou dostupnost pro ochranu proti selhání hardwaru nebo plánované události údržby.

AKS clustery můžete zajistit vyšší úroveň dostupnosti pro vaše aplikace distribuovat napříč zónami dostupnosti. Tyto zóny jsou fyzicky oddělená datová centra v dané oblasti. Když součásti clusteru se distribuují napříč několika zónami, je schopna tolerovat selhání v jednom z těchto zónách clusteru AKS. Vaše aplikace a operace správy i nadále být k dispozici i v případě, že jeden celého datového centra, dojde k problému.

Tento článek ukazuje, jak vytvořit AKS cluster a distribuovat součásti uzel napříč zónami dostupnosti. Tato funkce je aktuálně ve verzi Preview.

> [!IMPORTANT]
> Funkce AKS ve verzi preview jsou samoobslužných služeb, vyjádřit výslovný souhlas. Jsou poskytovány shromažďovat zpětnou vazbu a chyb z naší komunitě. Ve verzi preview nejsou tyto funkce určené k použití v produkčním prostředí. Funkce ve verzi public preview spadají pod "co možná nejlepší" podporu. Pomoc od týmů AKS technická podpora je k dispozici během pracovní doby tichomořské časové pásmo (PST) pouze. Další informace najdete v tématu následující články o podpoře:
>
> * [Zásady podpory AKS][aks-support-policies]
> * [Nejčastější dotazy k podpoře Azure][aks-faq]

## <a name="before-you-begin"></a>Před zahájením

Musí mít Azure CLI verze 2.0.66 nebo později nainstalována a nakonfigurována. Spustit `az --version` k vyhledání verze. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Instalace rozšíření aks ve verzi preview rozhraní příkazového řádku

K vytvoření clusterů AKS, které zóny dostupnosti používat, musíte *aks ve verzi preview* CLI verze rozšíření 0.4.1 nebo vyšší. Nainstalujte *aks ve verzi preview* pomocí rozšíření Azure CLI [přidat rozšíření az][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] příkaz::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flags-for-your-subscription"></a>Zaregistrujte příznaky funkcí pro vaše předplatné

K vytvoření clusteru AKS této zóny dostupnosti, nejprve povolte některé příznaky funkcí v rámci předplatného. Clustery používají virtuálního počítače škálovací sady pro správu nasazení a konfigurace uzlů Kubernetes. *Standardní* SKU nástroje pro vyrovnávání zatížení Azure je také potřeba zajistit odolnost pro komponenty sítě směrovat provoz do clusteru. Zaregistrujte *AvailabilityZonePreview*, *AKSAzureStandardLoadBalancer*, a *VMSSPreview* příznaky funkcí pomocí [az funkce register][az-feature-register] příkaz, jak je znázorněno v následujícím příkladu:

> [!CAUTION]
> Při registraci funkce v rámci předplatného nelze nyní zrušit registraci této funkce. Po povolení některé funkce ve verzi preview se výchozí hodnoty lze pro všechny clustery AKS, pak jste vytvořili v rámci předplatného. Nepovolí funkce ve verzi preview na předplatná pro produkční prostředí. Testování funkce ve verzi preview a shromažďování zpětné vazby pomocí samostatné předplatné.

```azurecli-interactive
az feature register --name AvailabilityZonePreview --namespace Microsoft.ContainerService
az feature register --name AKSAzureStandardLoadBalancer --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

Trvá několik minut, než se stav zobrazíte *registrované*. Vy můžete zkontrolovat stav registrace pomocí [seznam funkcí az][az-feature-list] příkaz:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AvailabilityZonePreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Až to budete mít, aktualizujte registraci *Microsoft.ContainerService* poskytovatele prostředků pomocí [az provider register][az-provider-register] příkaz:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations-and-region-availability"></a>Omezení a dostupnost oblastí

AKS clustery se aktuálně dají vytvářet pomocí zón dostupnosti v těchto oblastech:

* Východní USA 2
* Severní Evropa
* Jihovýchodní Asie
* Západní Evropa
* USA – západ 2

Při vytváření clusteru AKS pomocí zóny dostupnosti se vztahují následující omezení:

* Zóny dostupnosti můžete povolit jenom při vytvoření clusteru.
* Po vytvoření clusteru nelze aktualizovat nastavení zón dostupnosti. Nelze aktualizovat také existující, nejsou k dispozici zóny clusteru k používání zón dostupnosti.
* Zóny dostupnosti pro AKS cluster nelze zakázat po jeho vytvoření.
* Vybraná velikost uzlu (skladovou Položku virtuálního počítače) musí být k dispozici ve všech zónách dostupnosti.
* Clustery s dostupností, které vyžadují zóny povoleno používat Azure Standard nástrojů pro vyrovnávání zatížení pro distribuci napříč zónami.
* Je nutné použít Kubernetes verze 1.13.5 nebo vyšší, k nasazení nástroje pro vyrovnávání zatížení.

Zóny dostupnosti se používají clustery AKS musíte použít nástroj pro vyrovnávání zatížení Azure *standardní* SKU. Výchozí hodnota *základní* SKU nástroje pro vyrovnávání zatížení Azure nepodporuje distribuci napříč zónami dostupnosti. Další informace a omezení standardní nástroj pro vyrovnávání zatížení, najdete v části [omezení standardní SKU verze preview pro Azure load nástroje pro vyrovnávání][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Omezení disků v Azure

Svazky, které používají Azure managed disks nejsou aktuálně zónové prostředky. Podů znovu naplánována v jiné zóně ze své původní zóny nejde znovu připojit jejich předchozí disky. Doporučuje se spouštět bezstavových úloh, které nevyžadují, že vydá trvalého úložiště, které můžou pocházet napříč oblastmi.

Pokud musíte spustit stavová zatížení, použijte poskvrnění a tolerations ve vaší pod specifikace říct Kubernetes scheduler k vytvoření podů ve stejné zóně jako disky. Alternativně použijte úložiště založené na síti jako jsou například soubory Azure, které můžete připojit ke podů, jako jsou naplánované mezi zónami.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Přehled zón dostupnosti pro AKS clusterů

Zóny dostupnosti je vysoká dostupnost služeb, které chrání vaše aplikace a data z datacenter selhání. Zóny jsou jedinečná fyzická umístění v rámci oblasti Azure. Každá zóna se skládá z jednoho nebo více datových Center vybavených nezávislým napájením, chlazením a sítí. K zajištění odolnosti proti chybám, je minimálně tří samostatných zón ve všech oblastech, povolené. Fyzické oddělení zón dostupnosti v rámci oblasti chrání aplikace a data před selháními datových center. Zónově redundantní služby replikaci vašich aplikací a dat napříč zónami dostupnosti pro zajištění ochrany z jednoho body z chyby.

Další informace najdete v tématu [co jsou zóny dostupnosti v Azure?][az-overview].

AKS clustery, které jsou nasazeny pomocí zóny dostupnosti distribuovat napříč několika zónami v rámci jedné oblasti uzly. Například cluster ve *USA – východ 2* oblast, mohou vytvářet uzly ve všech třech zónách dostupnosti v *USA – východ 2*. Toto rozdělení prostředků clusteru AKS zlepšuje dostupnost clusteru, jako jsou odolné vůči selhání konkrétní zónu.

![Distribuce uzlů AKS napříč zónami dostupnosti](media/availability-zones/aks-availability-zones.png)

V zóně výpadek, uzly mohou být znovu vyrovnána ručně nebo pomocí automatického škálování clusteru. Pokud se jedné oblasti stane nedostupným, vaše aplikace dál běží.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Vytvoření clusteru AKS napříč zónami dostupnosti

Při vytváření clusteru pomocí [az aks vytvořit][az-aks-create] příkazu `--node-zones` parametr definuje, které zóny agentské uzly jsou nasazené do. Součásti rovina řízení AKS pro váš cluster jsou také rozloženy zóny v konfiguraci nejvyšší dostupnosti při vytváření clusteru zadání `--node-zones` parametru.

Pokud nedefinujete žádné zóny pro výchozí fond agentů při vytváření clusteru AKS, součásti rovina řízení AKS pro váš cluster nebude používat zóny dostupnosti. Můžete přidat další uzel fondy (aktuálně ve verzi preview ve službě AKS) pomocí [přidání az aks nodepool][az-aks-nodepool-add] příkaz a zadejte `--node-zones` pro tyto nové uzly, agenta, ale součásti rovina řízení dál bez zóně dostupnosti sledování. Nelze změnit pásmo pro fond uzlů nebo AKS řídit roviny součásti po nasazení.

Následující příklad vytvoří cluster AKS, s názvem *myAKSCluster* ve skupině prostředků s názvem *myResourceGroup*. Celkem *3* uzly jsou vytvořeny - jednoho agenta v zóně *1*, jeden v *2*a pak ho *3*. Součásti rovina řízení AKS se taky distribuují napříč zónami v konfiguraci nejvyšší dostupnosti, protože se už definovaný jako součást clusteru vytvořit proces.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version 1.13.5 \
    --generate-ssh-keys \
    --enable-vmss \
    --load-balancer-sku standard \
    --node-count 3 \
    --node-zones 1 2 3
```

Trvá několik minut pro vytvoření clusteru AKS.

## <a name="verify-node-distribution-across-zones"></a>Ověřte uzlu distribuce napříč zónami

Když bude cluster připravený, seznam uzlů agentů ve škálovací sadě zobrazíte jaké zónu dostupnosti, kde jsou nasazeny v.

Nejprve získejte přihlašovací údaje clusteru AKS pomocí [az aks get-credentials][az-aks-get-credentials] příkaz:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Pak pomocí [popisují kubectl][kubectl-describe] zobrazte seznam uzlů v clusteru. Filtrování *failure-domain.beta.kubernetes.io/zone* hodnoty, jak je znázorněno v následujícím příkladu:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

Následující příklad výstupu ukazuje tři uzly distribuovány v určité oblasti a zóny dostupnosti, jako například *eastus2 1* pro první zónu dostupnosti a *eastus2 2* druhé Zóna dostupnosti:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Při přidávání dalších uzlů pro fond agentů, Platforma Azure je automaticky distribuuje základních virtuálních počítačů napříč zónami dostupnosti zadané.

## <a name="next-steps"></a>Další postup

Tento článek podrobné informace o tom, jak vytvořit cluster AKS, který používá zóny dostupnosti. Další důležité informace o clusterech s vysokou dostupností najdete v části [osvědčené postupy pro obchodní kontinuity podnikových procesů a zotavení po havárii ve službě AKS][best-practices-bc-dr].

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
