---
title: Nejčastější dotazy ke službě Azure Kubernetes
description: Poskytuje odpovědi na některé běžné dotazy týkající se služby Azure Kubernetes Service.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/20/2018
ms.author: iainfou
ms.openlocfilehash: ea22b33233f85da117de54829e5a16bd7dcab36a
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205244"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Nejčastější dotazy o Azure Kubernetes Service (AKS)

Tento článek adresy časté otázky o Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Které oblasti Azure, které poskytuje Azure Kubernetes Service (AKS) ještě dnes?

Viz služby Azure Kubernetes Service [oblasti a dostupnost] [ aks-regions] dokumentaci úplný seznam.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Jsou aktualizace zabezpečení použít pro uzly AKS agenta?

Azure automaticky aplikuje na uzly v clusteru na noční plán oprav zabezpečení. Ale, budete muset zajistit, že uzly se restartují podle potřeby. Máte několik možností, jak k provádění restartování uzlu:

- Ručně pomocí webu Azure portal nebo rozhraní příkazového řádku Azure.
- Díky upgradu clusteru AKS. Inovace clusteru automaticky [kordon a výpusť uzly](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/), přenést jejich zálohování s použitím nejnovější image Ubuntu. Aktualizace image operačního systému na svých uzlech beze změny tak, že zadáte v aktuální verzi clusteru Kubernetes verze `az aks upgrade`.
- Pomocí [Kured](https://github.com/weaveworks/kured), open source restartování démona pro Kubernetes. Kured pracuje jako [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) a sleduje každý uzel pro přítomnost souboru, která udává, že je vyžadován restart. Potom orchestruje restartování v clusteru stejné cordon a proces vyprazdňování popsané výše.

## <a name="does-aks-support-node-autoscaling"></a>AKS podporuje automatické škálování uzlů?

Ano, je k dispozici prostřednictvím automatické škálování [Kubernetes automatického] [ auto-scaler] od Kubernetes 1.10.

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>Podporuje AKS Kubernetes řízení přístupu na základě role (RBAC)?

Ano, může být RBAC povolen [nasazujete cluster AKS pomocí šablony Azure Resource Manageru nebo rozhraní příkazového řádku Azure](https://docs.microsoft.com/en-us/azure/aks/aad-integration). Tato funkce brzy přijde na webu Azure portal.

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-this-be-configured"></a>Jaké řadiče jejich příchodu Kubernetes AKS podporuje? Lze to nakonfigurovat?

AKS podporuje následující [jejich příchodu řadiče][admission-controllers]:

* NamespaceLifecycle
* LimitRanger
* ServiceAccount
* DefaultStorageClass
* DefaultTolerationSeconds
* MutatingAdmissionWebhook
* ValidatingAdmissionWebhook
* ResourceQuota
* DenyEscalatingExec
* AlwaysPullImages

Není aktuálně možné upravit seznam jejich příchodu řadiče ve službě AKS.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Můžete nasadit AKS do existující virtuální síť?

Ano, můžete nasadit cluster AKS do existující virtuální sítě pomocí [rozšířeného sítě funkce](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/networking-overview.md).

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>Můžete omezit na serveru Kubernetes API k přístupné jenom v rámci virtuální sítě?

V tuto chvíli to není možné. Na serveru Kubernetes API je vystavena jako veřejný plně kvalifikovaný název domény (plně kvalifikovaný název domény). By měla řídit přístup k vašemu clusteru pomocí [Kubernetes na základě rolí řízení přístupu (RBAC) a Azure Active Directory (AAD)](https://docs.microsoft.com/en-us/azure/aks/aad-integration).

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault integrovaná AKS?

AKS není nativně integrovaná s Azure Key Vault v tuto chvíli. Existují však komunitních řešení jako [acs-keyvault agenta z Hexadite][hexadite].

## <a name="can-i-run-windows-server-containers-on-aks"></a>Můžete spustit kontejnery Windows serveru v AKS

Pro spouštění kontejnerů Windows serveru, budete muset spustit uzly se systémem Windows Server. V tuto chvíli nejsou k dispozici ve službě AKS Windows serverových uzlů. Můžete však použít Virtual Kubelet můžete plánovat kontejnery Windows v Azure Container Instances a spravovat je jako součást clusteru AKS. Další informace najdete v tématu [Virtual Kubelet použití službou AKS][virtual-kubelet].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Proč jsou dvě skupiny prostředků vytvořené službou AKS?

Každé nasazení služby AKS zahrnuje dvě skupiny prostředků. První je vytvořené a obsahuje pouze příslušný prostředek služby Kubernetes. Poskytovateli prostředků pro AKS automaticky vytvoří druhou během nasazení s názvem jako *MC_myResourceGroup_myAKSCluster_eastus*. Druhý skupina prostředků obsahuje všechny prostředky infrastruktury přidružené ke clusteru, jako jsou virtuální počítače, sítě a úložiště. Vytvoří se pro zjednodušení vyčištění prostředků.

Při vytváření prostředků, které se použije pro cluster AKS, jako je například účty úložiště nebo vyhrazené veřejné IP adrese, měli byste je umístit do skupiny prostředků pro automaticky generované.

## <a name="does-aks-offer-a-service-level-agreement"></a>Nabízí AKS smlouvu o úrovni služeb?

Smlouvy o úrovni služeb (SLA) zprostředkovatele souhlasí uhradit odběratele pro ceny služby by neměl být splněny úroveň publikované služby. AKS, samotného je zdarma, je zdarma k dispozici a afilacím odpovídajícím a proto žádné formální smlouvu SLA. Ale jsme se snaží zachovat dostupnost minimálně 99,5 % serveru Kubernetes API.

<!-- LINKS - internal -->

[aks-regions]: ./container-service-quotas.md
[virtual-kubelet]: virtual-kubelet.md

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
