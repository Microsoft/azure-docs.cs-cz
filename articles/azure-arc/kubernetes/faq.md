---
title: Nejčastější dotazy k Azure ARC s povoleným Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Tento článek obsahuje seznam nejčastějších dotazů souvisejících s povoleným Kubernetesem Azure ARC.
keywords: Kubernetes, oblouk, Azure, kontejnery, konfigurace, GitOps, nejčastější dotazy
ms.openlocfilehash: 84368cc63bd9aaf1df4fb281395b47a6e886cb7f
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025845"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>Nejčastější dotazy – Kubernetes s povoleným obloukem Azure

Tento článek obsahuje nejčastější dotazy týkající se Kubernetes s povoleným ARC Azure.

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>Jaký je rozdíl mezi Kubernetes a službou Azure Kubernetes (AKS) s podporou ARC?

AKS je spravovaná nabídka Kubernetes pro Azure. AKS zjednodušuje nasazení spravovaného clusteru Kubernetes v Azure tím, že se do Azure převede do značné míry složitosti a provozní režie. Vzhledem k tomu, že hlavní servery Kubernetes spravuje Azure, spravujete a spravujete jenom uzly agentů.

Služba Azure ARC s povoleným Kubernetes umožňuje rozšiřování clusterů Kubernetes do Azure, jako jsou Azure Monitor a Azure Policy). Udržujete svůj základní cluster Kubernetes sám.

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>Potřebuji připojit své clustery AKS běžící na Azure do Azure ARC?

No. Všechny funkce Kubernetes s podporou ARC Azure, včetně Azure Monitor a Azure Policy (gatekeeper), jsou k dispozici na AKS (nativní prostředek v Azure Resource Manager).
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>Mám připojit cluster AKS-HCI a clustery Kubernetes v centru Azure Stack a Azure Stack Edge do Azure ARC?

Ano, propojíte cluster AKS-HCI nebo clustery Kubernetes v Azure Stack Edge nebo centrum Azure Stack do Azure ARC, které poskytuje clustery se reprezentacemi prostředků v Azure Resource Manager. Tato reprezentace prostředků rozšiřuje možnosti jako konfigurace clusteru, Azure Monitor a Azure Policy (gatekeeper) na připojené clustery Kubernetes.

Pokud je cluster Kubernetes s podporou Azure ARC na Azure Stack Edge, AKS na Azure Stack HCl (>= duben 2021), nebo AKS v systému Windows Server 2019 Datacenter (>= duben 2021), bude konfigurace Kubernetes zapsána zdarma.

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>Jak vyřešíte neplatných prostředků Kubernetes s povoleným obloukem ARC Azure?

Certifikát Identita spravované služby (MSI) přidružený k vašemu Kubernetesu s povoleným ARC Azure má období 90 dnů. Po vypršení platnosti certifikátu se tento prostředek posuzuje `Expired` a všechny funkce (třeba konfigurace, monitorování a zásady) přestanou na tomto clusteru fungovat. Postup opětovného fungování clusteru Kubernetes s obloukem Azure ARC:

1. Odstraňte prostředky a agenty Kubernetes s povoleným ARC Azure v clusteru. 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. Nasaďte agenty do clusteru a znovu vytvořte prostředek Kubernetes s povoleným ARC Azure.
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` odstraní také konfigurace nad clusterem. Po spuštění `az connectedk8s connect` znovu vytvořte konfigurace v clusteru, a to buď ručně, nebo pomocí Azure Policy.

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>I když už používám kanály CI/CD, můžu dál používat Kubernetes a konfigurace s povoleným ARC Azure?

Ano, konfigurace můžete dál používat v clusteru, který přijímá nasazení prostřednictvím kanálu CI/CD. V porovnání s tradičními kanály CI/CD přináší funkce konfigurace dvě další výhody:

**Odsouhlasení**

Kanál CI/CD aplikuje změny pouze jednou při spuštění kanálu. Operátor GitOps v clusteru se ale průběžně dotazuje úložiště Git, aby se načetl požadovaný stav prostředků Kubernetes v clusteru. Pokud operátor GitOps najde požadovaný stav prostředků, které se liší od skutečného stavu prostředků v clusteru, tento posun se sjednotí.

**Použít GitOps ve velkém měřítku**

Kanály CI/CD jsou užitečné pro nasazení řízená událostmi do vašeho clusteru Kubernetes (například nabízené oznámení do úložiště Git). Pokud ale chcete nasadit stejnou konfiguraci do všech clusterů Kubernetes, budete muset ručně nakonfigurovat každé přihlašovací údaje clusteru Kubernetes na kanál CI/CD. 

U Kubernetes s podporou Azure ARC, protože Azure Resource Manager spravuje vaše konfigurace, můžete automatizovat vytváření stejné konfigurace ve všech prostředcích Kubernetes s povolenou službou Azure ARC pomocí Azure Policy, a to v oboru předplatného nebo skupiny prostředků. Tato možnost se vztahuje i na prostředky Kubernetes s povoleným ARC Azure vytvořené po přiřazení zásady.

Tato funkce používá základní konfigurace (jako jsou zásady sítě, vazby rolí a zásady zabezpečení pod celým) v celém inventáři Kubernetes clusteru pro splnění požadavků na dodržování předpisů a zásad správného řízení.

## <a name="next-steps"></a>Další kroky

* Projděte si náš rychlý Start, který [připojí cluster Kubernetes ke službě Azure ARC](./quickstart-connect-cluster.md).
* Už máte připojený cluster Kubernetes ke clusteru Azure ARC? [Vytvořte konfigurace na clusteru Kubernetes s povoleným obloukem](./tutorial-use-gitops-connected-cluster.md).
* Naučte se [používat Azure Policy k aplikování konfigurací se škálováním](./use-azure-policy.md).