---
title: Použití virtuálních uzlů
titleSuffix: Azure Kubernetes Service
description: Přehled toho, jak používat virtuální uzel se službou Azure Kubernetes Services (AKS)
services: container-service
ms.topic: conceptual
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: 2dd91e5c506f229d653fdf98bc0549c173cec793
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93351871"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes"></a>Vytvoření a konfigurace clusteru Azure Kubernetes Services (AKS) pro použití virtuálních uzlů

Pokud chcete rychle škálovat úlohy aplikací v clusteru AKS, můžete použít virtuální uzly. U virtuálních uzlů máte rychlé zřizování lusků a platíte za dobu jejich spuštění jenom za sekundu. Nemusíte čekat na automatické škálování clusteru Kubernetes, aby se nasadily výpočetní uzly virtuálních počítačů, aby se spouštěly další lusky. Virtuální uzly jsou podporované jenom se systémy Linux a uzly.

Doplněk virtuálních uzlů pro AKS je založen na open source projektu [Virtual Kubelet][virtual-kubelet-repo].

Tento článek vám poskytne přehled o dostupnosti oblasti a požadavcích na síť pro používání virtuálních uzlů a také známých omezení.

## <a name="regional-availability"></a>Regionální dostupnost

Všechny oblasti, kde ACI podporuje skladové položky virtuální sítě, jsou podporované pro nasazení virtuálních uzlů.

U dostupných skladových položek procesoru a paměti v jednotlivých oblastech prosím zkontrolujte [dostupnost prostředků Azure Container Instances pro Azure Container Instances v oblastech Azure – skupiny kontejnerů Linux](../container-instances/container-instances-region-availability.md#linux-container-groups)

## <a name="network-requirements"></a>Požadavky sítě

Virtuální uzly umožňují síťovou komunikaci mezi lusky, které běží v Azure Container Instances (ACI) a clusteru AKS. Pro zajištění této komunikace se vytvoří podsíť virtuální sítě a přiřadí se delegovaná oprávnění. Virtuální uzly fungují jenom s clustery AKS vytvořenými pomocí *pokročilých* sítí (Azure CNI). Ve výchozím nastavení se clustery AKS vytvářejí se *základními* sítěmi (kubenet).

Lusky běžící v Azure Container Instances (ACI) potřebují přístup ke koncovému bodu serveru rozhraní AKS API, aby bylo možné konfigurovat síť.

## <a name="known-limitations"></a>Známá omezení

Funkce virtuálních uzlů je silně závislá na sadě funkcí ACI. Kromě [kvót a omezení pro Azure Container Instances](../container-instances/container-instances-quotas.md)se u virtuálních uzlů ještě nepodporují následující scénáře:

* Získání ACR imagí pomocí instančního objektu [Alternativním řešením](https://github.com/virtual-kubelet/azure-aci/blob/master/README.md#private-registry) je používání [tajných klíčů Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Omezení Virtual Network](../container-instances/container-instances-vnet.md) , včetně partnerských vztahů virtuálních sítí, zásad sítě Kubernetes a odchozího provozu do Internetu se skupinami zabezpečení sítě.
* Inicializovat kontejnery
* [Aliasy hostitele](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argumenty](../container-instances/container-instances-exec.md#restrictions) pro exec v ACI
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) nebude nasazovat lusky do virtuálních uzlů.
* Virtuální uzly podporují plánování lusků v systému Linux. K naplánování kontejnerů Windows serveru na ACI můžete ručně nainstalovat poskytovatele open source [Virtual KUBELET ACI](https://github.com/virtual-kubelet/azure-aci) .
* Virtuální uzly vyžadují clustery AKS s využitím sítě Azure CNI.
* Virtuální uzly s privátními clustery.
* Použití rozsahů IP adres autorizovaných serverem API pro AKS
* Svazek, ve kterém se namontují sdílené soubory Azure, podporují [obecné účely v1](../storage/common/storage-account-overview.md#types-of-storage-accounts). Postupujte podle pokynů pro připojení [svazku ke sdílené složce služby soubory Azure](azure-files-volume.md) .

## <a name="next-steps"></a>Další kroky

Konfigurace virtuálních uzlů pro clustery:

- [Vytváření virtuálních uzlů pomocí Azure CLI](virtual-nodes-cli.md)
- [Vytváření virtuálních uzlů pomocí portálu ve službě Azure Kubernetes Services (AKS)](virtual-nodes-portal.md)

Virtuální uzly jsou často jednou součástí řešení škálování v AKS. Další informace o škálování řešení najdete v následujících článcích:

- [Použití automatického škálování Kubernetes vodorovně pod][aks-hpa]
- [Použití automatického škálování clusteru Kubernetes][aks-cluster-autoscaler]
- [Podívejte se na ukázku automatického škálování pro virtuální uzly.][virtual-node-autoscale]
- [Přečtěte si další informace o knihovně open source knihovny Virtual Kubelet][virtual-kubelet-repo]

<!-- LINKS - external -->
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
