---
title: Nejčastější dotazy pro Azure Kubernetes Service (AKS)
description: Poskytuje odpovědi na některé běžné otázky o Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/17/2018
ms.author: iainfou
ms.openlocfilehash: 55f32c403da179a0b7babc2172a80c2168cfab17
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636913"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Nejčastější dotazy o Azure Kubernetes Service (AKS)

Tento článek adresy časté otázky o Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Které oblasti Azure, které poskytuje Azure Kubernetes Service (AKS) ještě dnes?

Úplný seznam dostupných oblastí najdete v tématu [AKS oblasti a dostupnost][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>AKS podporuje automatické škálování uzlů?

Ano, je k dispozici prostřednictvím automatické škálování [Kubernetes automatického] [ auto-scaler] od Kubernetes 1.10. Další informace o tom, jak nakonfigurovat a používat automatického škálování clusteru najdete v tématu [automatické škálování clusteru v AKS][aks-cluster-autoscale].

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>Podporuje AKS Kubernetes řízení přístupu na základě role (RBAC)?

Ano, je standardně povolená Kubernetes RBAC, když clustery jsou vytvořeny pomocí Azure CLI. RBAC je možné povolit pro clustery vytvořit pomocí webu Azure portal nebo šablony.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Můžete nasadit AKS do existující virtuální síť?

Ano, můžete nasadit cluster AKS do existující virtuální sítě pomocí [rozšířeného sítě funkce][aks-advanced-networking].

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>Můžete omezit na serveru Kubernetes API k přístupné jenom v rámci virtuální sítě?

V tuto chvíli to není možné. Na serveru Kubernetes API je zveřejněné jako veřejný plně kvalifikovaný název domény (FQDN). Můžete řídit přístup k vašemu clusteru pomocí [Kubernetes na základě rolí řízení přístupu (RBAC) a Azure Active Directory (AAD)][aks-rbac-aad]

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Jsou aktualizace zabezpečení použít pro uzly AKS agenta?

Ano, Azure automaticky aplikuje opravy zabezpečení na uzly v clusteru na noční plánu. Ale, budete muset zajistit, že uzly se restartují podle potřeby. Máte několik možností, jak k provádění restartování uzlu:

- Ručně pomocí webu Azure portal nebo rozhraní příkazového řádku Azure.
- Díky upgradu clusteru AKS. Inovace clusteru automaticky [kordon a výpusť uzly][cordon-drain], následně vyvolejte místní každý uzel zpátky pomocí nejnovější image Ubuntu a nové verze opravy nebo dílčí verze Kubernetes. Další informace najdete v tématu [Upgrade clusteru AKS][aks-upgrade].
- Pomocí [Kured](https://github.com/weaveworks/kured), open source restartování démona pro Kubernetes. Kured pracuje jako [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) a sleduje každý uzel pro přítomnost souboru, která udává, že je vyžadován restart. Restartování operačního systému se spravují v clusteru pomocí stejných [kordon a výpusť procesu] [ cordon-drain] jako upgradu clusteru.

Další informace o používání kured najdete v tématu [použití aktualizací zabezpečení a jádra pro uzly ve službě AKS][node-updates-kured].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Proč jsou dvě skupiny prostředků vytvořené službou AKS?

Každé nasazení služby AKS zahrnuje dvě skupiny prostředků:

- První skupina prostředků je vytvořené vámi a obsahuje pouze příslušný prostředek služby Kubernetes. Poskytovateli prostředků pro AKS například automaticky vytvoří během nasazení, je druhý řádek *MC_myResourceGroup_myAKSCluster_eastus*.
- Tento druhý zdroj skupiny jako například *MC_myResourceGroup_myAKSCluster_eastus*, obsahuje všechny prostředky infrastruktury přidružené ke clusteru. Tyto prostředky zahrnují virtuální počítače uzlu Kubernetes, virtuální sítě a úložiště. Toto samostatné skupiny prostředků se vytvoří pro zjednodušení vyčištění prostředků.

Pokud jste vytvořili prostředky pro použití s vaším clusterem AKS, jako je například účty úložiště nebo vyhrazené veřejné IP adresy, je umístíte ve skupině automaticky generované prostředků.

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>Můžete upravit značky a dalších vlastností AKS prostředky ve skupině prostředků MC_ *?

Úprava a odstranění značky Azure vytvořilo a dalších vlastností prostředků v rámci *MC_** skupina prostředků může vést k neočekávaným výsledkům, například škálování a upgradu chyby. Podporuje se vytvářet a upravovat další vlastní značky, například přiřadit obchodní jednotky nebo nákladů centra. Úprava prostředků v rámci *MC_** v AKS cluster, přestane fungovat cíle na úrovni služby.

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

Není aktuálně možné upravit seznam jejich příchodu řadiče ve službě AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault integrovaná AKS?

AKS není aktuálně nativně integrovaná s Azure Key Vault. Ale [Azure Key Vault FlexVolume pro projekt Kubernetes] [ keyvault-flexvolume] umožňuje přímou integraci od podů Kubernetes k tajným kódům v trezoru klíčů.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Můžete spustit kontejnery Windows serveru v AKS

Pro spouštění kontejnerů Windows serveru, budete muset spustit uzly se systémem Windows Server. V tuto chvíli nejsou k dispozici ve službě AKS Windows serverových uzlů. Můžete však použít Virtual Kubelet můžete plánovat kontejnery Windows v Azure Container Instances a spravovat je jako součást clusteru AKS. Další informace najdete v tématu [Virtual Kubelet použití službou AKS][virtual-kubelet].

## <a name="does-aks-offer-a-service-level-agreement"></a>Nabízí AKS smlouvu o úrovni služeb?

Smlouvy o úrovni služeb (SLA) zprostředkovatele souhlasí uhradit zákazníkovi ceny za službu, pokud není splněná úroveň publikované služby. AKS, samotného je zdarma, je zdarma k dispozici a afilacím odpovídajícím a proto žádné formální smlouvu SLA. Ale AKS se snaží zachovat dostupnost minimálně 99,5 % serveru Kubernetes API.

<!-- LINKS - internal -->

[aks-regions]: ./container-service-quotas.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-advanced-networking.md
[aks-rbac-aad]: ./aad-integration.md
[node-updates-kured]: node-updates-kured.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol

