---
title: Nejčastější dotazy pro Azure Kubernetes Service (AKS)
description: Najděte odpovědi na některé běžné otázky o Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/08/2019
ms.author: mlearned
ms.openlocfilehash: 495f182ed450d0fac69b31ea2996bacc60863fea
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672782"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Nejčastější dotazy o Azure Kubernetes Service (AKS)

Tento článek adresy časté otázky o Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>Které oblasti Azure aktuálně poskytuje AKS?

Úplný seznam dostupných oblastí najdete v tématu [AKS oblasti a dostupnost][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>AKS podporuje automatické škálování uzlů?

Ano, možnost automatického škálování uzlů agentů vodorovně ve službě AKS je aktuálně dostupná ve verzi preview. Zobrazit [automatické škálování clusteru, které splňují požadavky aplikace ve službě AKS][aks-cluster-autoscaler] for instructions. AKS autoscaling is based on the [Kubernetes autoscaler][auto-scaler].

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Můžete nasadit AKS do existující virtuální síť?

Ano, můžete nasadit cluster AKS do existující virtuální sítě pomocí [rozšířeného sítě funkce][aks-advanced-networking].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Můžete omezit, kdo má přístup k serveru Kubernetes API?

Ano, můžete omezit přístup k serveru pomocí rozhraní Kubernetes API [rozhraní API serveru oprávnění rozsahy IP adres][api-server-authorized-ip-ranges], která je aktuálně ve verzi preview.

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Můžete vytvořit na serveru Kubernetes API přístupné pouze v rámci virtuální sítě?

Není v tuto chvíli to je naplánovaná, ale. Průběh můžete sledovat na [úložiště AKS GitHub][private-clusters-github-issue].

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Může mít různé velikosti virtuálních počítačů v jednom clusteru?

Ano, můžete pomocí různých velikostí virtuálních počítačů ve vašem clusteru AKS tak, že vytvoříte [více fondy uzlů][multi-node-pools], která je aktuálně ve verzi preview.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Jsou aktualizace zabezpečení použít pro uzly AKS agenta?

Azure automaticky aplikuje na uzly s Linuxem v clusteru na noční plán oprav zabezpečení. Ale budete muset zajistit, že tyto Linux uzly se restartují jako povinné. Máte několik možností pro restartování uzlů:

- Ručně pomocí webu Azure portal nebo rozhraní příkazového řádku Azure.
- Díky upgradu clusteru AKS. Inovace clusteru [kordon a výpusť uzly][cordon-drain] automatically and then bring a new node online with the latest Ubuntu image and a new patch version or a minor Kubernetes version. For more information, see [Upgrade an AKS cluster][aks-upgrade].
- S použitím [Kured](https://github.com/weaveworks/kured), open source restartování démona pro Kubernetes. Kured pracuje jako [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) a sleduje každý uzel na přítomnost souboru, který označuje, že je vyžadován restart. Napříč clusterem, restartování operačního systému spravuje stejný [kordon a výpusť procesu][cordon-drain] jako upgradu clusteru.

Další informace o používání kured najdete v tématu [použití aktualizací zabezpečení a jádra pro uzly ve službě AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Uzly Windows serveru

Pro uzly Windows serveru (aktuálně ve verzi preview ve službě AKS) Windows Update automaticky spustit a použijte nejnovější aktualizace. V pravidelných intervalech kolem cyklu vydávání verzí Windows Update a procesu ověřování měli byste provést upgrade na fondy uzlů Windows Server ve vašem clusteru AKS. Pomocí tohoto procesu vytvoří uzly, na kterých běží nejnovější image Windows serveru a oprav a pak odstraní starší uzly. Další informace o tomto procesu najdete v tématu [fond uzlů ve službě AKS Upgrade][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Proč jsou dvě skupiny prostředků vytvořené službou AKS?

Každé nasazení služby AKS zahrnuje dvě skupiny prostředků:

1. Můžete vytvořit první skupinu prostředků. Tato skupina obsahuje pouze příslušný prostředek služby Kubernetes. Poskytovatel prostředků pro AKS automaticky vytvoří druhé skupině prostředků během nasazování. Příkladem druhého skupina prostředků je *MC_myResourceGroup_myAKSCluster_eastus*. Informace o tom, jak zadat název této druhé skupině prostředků najdete v další části.
1. Druhá skupiny prostředků, označované jako *uzlu skupiny prostředků*, obsahuje všechny prostředky infrastruktury přidružené ke clusteru. Tyto prostředky zahrnují virtuální počítače uzlu Kubernetes, virtuální sítě a úložiště. Ve výchozím nastavení, uzlu skupiny prostředků má název, například *MC_myResourceGroup_myAKSCluster_eastus*. AKS automaticky odstraní uzlu prostředku pokaždé, když se při odstranění clusteru, takže by měl používat pouze pro prostředky, které sdílejí životní cyklus clusteru.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Můžete zadat vlastní název pro skupinu prostředků uzlů AKS?

Ano. Ve výchozím nastavení, bude AKS nazvěte skupinu prostředků v uzlu *MC_clustername_resourcegroupname_location*, ale můžete taky zadat vlastní název.

Chcete-li zadat vlastní název skupiny prostředků, nainstalujte [aks ve verzi preview][aks-preview-cli] verze rozšíření Azure CLI *0.3.2* nebo novější. Při vytváření clusteru AKS pomocí [az aks vytvořit][az-aks-create] příkazu, použijte *– node-resource-group* parametr a zadejte název pro skupinu prostředků. Pokud jste [pomocí šablony Azure Resource Manageru][aks-rm-template] Pokud chcete nasadit AKS cluster, můžete definovat pomocí názvu skupiny prostředků *nodeResourceGroup* vlastnost.

* Poskytovatel prostředků Azure ve svém vlastním předplatném automaticky vytvoří skupinu sekundární prostředků.
* Název skupiny prostředků vlastní můžete zadat jenom při vytváření clusteru.

Při práci s vybranou skupinou prostředků uzlu, mějte na paměti, která není možné:

* Zadejte existující skupinu prostředků pro skupinu prostředků uzlu.
* Zadejte jiné předplatné pro skupinu prostředků uzlu.
* Změňte název skupiny prostředků uzlu po vytvoření clusteru.
* Zadejte názvy pro spravované prostředky v rámci uzlu skupiny prostředků.
* Úpravy nebo odstranění značky spravované prostředky v rámci uzlu skupiny prostředků. (Viz další informace v následující části.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Můžete upravit značky a dalších vlastností AKS prostředky ve skupině prostředků uzel?

Pokud upravíte nebo odstraníte Azure vytvořené značky a dalších vlastností prostředků ve skupině prostředků uzel, může získat neočekávané výsledky, jako je například škálování a upgradu chyby. AKS umožňuje vytvářet a upravovat vlastní značky. Můžete vytvořit nebo upravit vlastní značky, například, přiřadit obchodní jednotky nebo nákladů centra. Změnou prostředky ve skupině prostředků uzlu v clusteru AKS přerušit cíle úrovně služeb (SLO). Další informace najdete v tématu [nemá AKS nabízí smlouvu o úrovni služeb?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Jaké řadiče jejich příchodu Kubernetes AKS podporuje? Můžete jejich příchodu řadiče přidat nebo odebrat?

AKS podporuje následující [jejich příchodu řadiče][admission-controllers]:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *DenyEscalatingExec*
- *AlwaysPullImages*

V současné době nelze upravit seznam jejich příchodu řadiče ve službě AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault integrovaná AKS?

AKS není aktuálně nativně integrovaná s Azure Key Vault. Ale [Azure Key Vault FlexVolume pro projekt Kubernetes][keyvault-flexvolume] umožňuje přímou integraci od podů Kubernetes k tajným kódům v trezoru klíčů.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Můžete spustit kontejnery Windows serveru v AKS

Ano, kontejnery Windows serveru jsou dostupné ve verzi preview. Pro spouštění kontejnerů Windows serveru ve službě AKS, můžete vytvořit fond uzlů, na kterém běží Windows Server jako hostovaný operační systém. Kontejnery Windows serveru můžete použít pouze systém Windows Server 2019. Abyste mohli začít, najdete v článku [vytvoření clusteru AKS pomocí fond uzlů Windows Server][aks-windows-cli].

Podpora serveru okno pro fond uzlů obsahuje některá omezení, která jsou součástí Windows serveru pro odesílání dat v projektu Kubernetes. Další informace o těchto omezeních najdete v tématu [kontejnery Windows serveru v AKS omezení][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>Nabízí AKS smlouvu o úrovni služeb?

Smlouvu o úrovni služeb (SLA) zprostředkovatele souhlasí uhradit zákazníkovi ceny za službu, pokud není splněná úroveň publikované služby. Protože AKS je zdarma, bez nákladů je k dispozici uhradit, tak AKS nemá žádné formální smlouvu SLA. Ale AKS se snaží zachovat dostupnost aspoň 99,5 % serveru Kubernetes API.

## <a name="why-cant-i-set-maxpods-below-30"></a>Proč nelze nastavit maxPods pod 30?

Ve službě AKS, můžete nastavit `maxPods` hodnotu při vytváření clusteru pomocí šablony Azure Resource Manageru a Azure CLI. Ale vyžadují Kubenet i Azure CNI *minimální hodnota* (ověřit v okamžiku vytvoření):

| Sítě | Minimální | Maximum |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

Protože AKS je spravovaná služba, můžeme nasadit a spravovat doplňky a podů jako součást clusteru. V minulosti, můžou uživatelé definovat `maxPods` hodnotu nižší než hodnota, která spravované podů potřebné ke spuštění (například 30). AKS nyní vypočítá minimální počet podů pomocí tohoto vzorce: ((maxPods nebo (maxPods * vm_count)) > minimální podů spravovaného doplňku.

Uživatelé nemohou přepsat minimální `maxPods` ověření.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Můžete použít Azure rezervace slevy na své uzly AKS agenta?

AKS agentské uzly se účtují jako standardní virtuální počítače Azure, takže pokud jste si koupili [Azure rezervace][reservation-discounts] pro velikost virtuálního počítače, který používáte ve službě AKS, se automaticky provedou tyto slevy.

<!-- LINKS - internal -->

[aks-regions]: ./quotas-skus-regions.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948