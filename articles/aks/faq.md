---
title: Nejčastější dotazy pro Azure Kubernetes Service
description: Poskytuje odpovědi na některé běžné otázky o Azure Kubernetes Service.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/25/2018
ms.author: nepeters
ms.openlocfilehash: 5155d0c85e5b3698b0a13d2d5256a235858f0e82
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938385"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Nejčastější dotazy o Azure Kubernetes služby (AKS)

Tento článek adresy časté otázky o Azure Kubernetes služby (AKS).

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Které oblasti Azure ještě dnes poskytovat služby Kubernetes Azure (AKS)?

- Austrálie – východ
- Střední Kanada
- Východní Kanada
- Střed USA
- Východ USA
- Východní US2
- Severní Evropa
- Spojené království – jih
- Západní Evropa
- Západní USA
- Západní USA 2

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Platí pro uzly agenta AKS aktualizace zabezpečení?

Azure automaticky použije opravy zabezpečení pro uzly v clusteru na noční plánu. Ale jste zodpovědní za zajištění, že uzly se restartují podle potřeby. Máte několik možností pro provádění restartování uzlu:

- Ručně prostřednictvím portálu Azure nebo Azure CLI.
- Upgradem AKS clusteru. Upgrade clusteru automaticky [cordon a vyprazdňování uzlů](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/), aby byly zálohování nejnovější Ubuntu Image. Aktualizovat bitovou kopii operačního systému na uzly beze změny Kubernetes verze zadáním aktuální verze clusteru v `az aks upgrade`.
- Pomocí [Kured](https://github.com/weaveworks/kured), Kubernetes démon procesu restartování open source. Kured běží jako [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) a sleduje každý uzel pro přítomnost souboru, která udává, že je vyžadován restart. Potom ji orchestruje restartování napříč clusterem, stejné cordon a vyprazdňování proces popsaný výše.

## <a name="does-aks-support-node-autoscaling"></a>Podporuje AKS automatické škálování uzlu?

Ano, je k dispozici prostřednictvím automatického škálování [Kubernetes autoscaler] [ auto-scaler] od Kubernetes 1.10.

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>Podporuje AKS Kubernetes řízení přístupu na základě role (RBAC)?

Ano, RBAC lze povolit v případě, že nasazení clusteru služby AKS z rozhraní příkazového řádku Azure nebo Azure Resource Manager šablony. Tato funkce bude brzy se k portálu Azure.

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-this-be-configured"></a>Jaké řadiče jejich příchodu Kubernetes AKS podporuje? Může to být nakonfigurované?

AKS podporuje následující [jejich příchodu řadiče][admission-controllers]:

* NamespaceLifecycle
* LimitRanger
* Účet_služby
* DefaultStorageClass
* DefaultTolerationSeconds
* MutatingAdmissionWebhook 
* ValidatingAdmissionWebhook
* ResourceQuota
* DenyEscalatingExec
* AlwaysPullImages

Není aktuálně možné upravit seznam řadičů jejich příchodu v AKS.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Můžete nasadit AKS do mé existující virtuální síť?

Ano, můžete nasadit do existující virtuální sítě pomocí clusteru služby AKS [Pokročilá síťové funkce](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/networking-overview.md).

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault integrovat AKS?

AKS není nativně integrované s Azure Key Vault v tuto chvíli. Existují však komunity řešení, jako jsou [acs-keyvault agenta z Hexadite][hexadite].

## <a name="can-i-run-windows-server-containers-on-aks"></a>Můžete spustit systém Windows Server kontejnery na AKS?

Pokud chcete spustit Windows Server kontejnery, musíte spustit uzly se systémem Windows Server. Nyní nejsou k dispozici v AKS Windows serverových uzlů. Pokud potřebujete spustit systém Windows Server kontejnery na Kubernetes v Azure, přejděte na téma [dokumentace pro modul služby acs](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/windows.md).

## <a name="why-are-two-resource-groups-created-with-aks"></a>Proč jsou dvě skupiny prostředků vytvořené pomocí AKS?

Každé nasazení AKS zahrnuje dvě skupiny prostředků. První je vytvořené vámi a obsahuje pouze Kubernetes prostředek služby. Zprostředkovatel prostředků AKS automaticky vytvoří druhý během nasazení s názvem, jako je *MC_myResourceGroup_myAKSCluster_eastus*. Druhý skupina prostředků obsahuje všechny prostředky infrastruktury spojené s clusterem, jako jsou virtuální počítače, sítě a úložiště. Vytváří se ke zjednodušení vyčištění prostředků.

Pokud vytváříte prostředky, které budou použity s AKS cluster, například účty úložiště nebo vyhrazené veřejné IP adresy, měli byste je umístit ve skupině automaticky generované prostředků.

## <a name="does-aks-offer-a-service-level-agreement"></a>Nabízí AKS smlouvu o úrovni služeb?

Smlouvy o úrovni služeb (SLA) zprostředkovatele souhlasí uhradit zákazník nákladů na služby by neměl být splněny úrovně publikované služby. Totiž volné AKS sám sebe není k dispozici uhradit bez nákladů a proto žádné formální SLA. Ale jsme snaží zachovat dostupnost alespoň podmínku 99.5 % serveru Kubernetes rozhraní API.

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
