---
title: Nejčastější dotazy k Azure ARC s povoleným Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Tento článek obsahuje seznam nejčastějších dotazů souvisejících s povoleným Kubernetesem Azure ARC.
keywords: Kubernetes, oblouk, Azure, kontejnery, konfigurace, GitOps, nejčastější dotazy
ms.openlocfilehash: 237b2629b833a63552b172636f46a1ac92e321c0
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561496"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>Nejčastější dotazy – Kubernetes s povoleným obloukem Azure

Tento článek obsahuje nejčastější dotazy týkající se Kubernetes s povoleným ARC Azure.

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>Jaký je rozdíl mezi Kubernetes a službou Azure Kubernetes (AKS) s podporou ARC?

AKS je spravovaná nabídka Kubernetes pro Azure. AKS zjednodušuje nasazení spravovaného clusteru Kubernetes v Azure tím, že se do Azure převede do značné míry složitosti a provozní režie. Vzhledem k tomu, že hlavní servery Kubernetes spravuje Azure, spravujete a spravujete jenom uzly agentů.

Služba Azure ARC s povoleným Kubernetes umožňuje rozšiřování clusterů Kubernetes do Azure, jako jsou Azure Monitor a Azure Policy). Udržujete svůj základní cluster Kubernetes sám.

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>Potřebuji připojit své clustery AKS běžící na Azure do Azure ARC?

Ne. Všechny funkce Kubernetes s podporou ARC Azure, včetně Azure Monitor a Azure Policy (gatekeeper), jsou k dispozici na AKS (nativní prostředek v Azure Resource Manager).
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>Mám připojit cluster AKS-HCI a clustery Kubernetes v centru Azure Stack a Azure Stack Edge do Azure ARC?

Ano, propojíte cluster AKS-HCI nebo clustery Kubernetes v Azure Stack Edge nebo centrum Azure Stack do Azure ARC, které poskytuje clustery se reprezentacemi prostředků v Azure Resource Manager. Tato reprezentace prostředků rozšiřuje možnosti, jako je konfigurace clusteru, Azure Monitor a Azure Policy (gatekeeper), do clusterů Kubernetes, které připojujete.

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>Jak vyřešíte neplatných prostředků Kubernetes s povoleným obloukem ARC Azure?

Certifikát služby (MSI), který je přidružený k vašemu Kuberenetes s povoleným obloukem Azure ARC, má období 90 dní. Po vypršení platnosti certifikátu se prostředek posuzuje `Expired` a všechny funkce, jako je konfigurace, monitorování a zásady, přestanou v tomto clusteru fungovat. Pomocí následujícího postupu můžete znovu získat svůj cluster Kubernetes s obloukem Azure ARC:

1. Odstranění prostředku a agentů Kubernetes s povoleným ARC Azure v clusteru 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. Znovu vytvořte prostředek Kubernetes s povoleným ARC Azure tím, že do clusteru znovu nasadíte agenty.
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` odstraní také konfigurace nad clusterem. Po spuštění `az connectedk8s connect` vytvořte konfigurace v clusteru znovu, a to buď ručně, nebo pomocí Azure Policy.

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>I když už používám kanály CI/CD, můžu dál používat Kubernetes a konfigurace s povoleným ARC Azure?

Ano, konfigurace můžete dál používat v clusteru, který přijímá nasazení prostřednictvím kanálu CI/CD. V porovnání s tradičními kanály CI/CD přináší funkce konfigurace dvě další výhody:

**Odsouhlasení**

Kanál CI/CD aplikuje změny pouze jednou při spuštění kanálu. Operátor GitOps v clusteru se ale průběžně dotazuje úložiště Git, aby se načetl požadovaný stav prostředků Kubernetes v clusteru. Pokud operátor GitOps najde požadovaný stav prostředků, které se liší od skutečného stavu prostředků v clusteru, tento posun se sjednotí.

**Použít GitOps ve velkém měřítku**

Kanály CI/CD jsou vhodné pro nasazení řízená událostmi do vašeho clusteru Kubernetes, kde může být událost nabízená do úložiště Git. Nasazení stejné konfigurace do všech clusterů Kubernetes ale vyžaduje, aby byl kanál CI/CD nakonfigurovaný s přihlašovacími údaji každého z těchto clusterů Kubernetes ručně. Na druhé straně platí, že v případě, že Azure Resource Manager spravuje vaše konfigurace, můžete použít Azure Policy k automatizaci aplikace požadované konfigurace na všech clusterech Kubernetes v rámci předplatného nebo rozsahu skupiny prostředků v jednom přechodu. Tato možnost se vztahuje i na prostředky Kubernetes s povoleným ARC Azure vytvořené po přiřazení zásady.

Funkce konfigurace se používá k aplikování konfigurace standardních hodnot, jako jsou zásady sítě, vazby rolí a zásady zabezpečení v celém inventáři Kubernetes clusterů pro dodržování předpisů a požadavky na dodržování zásad správného řízení.

## <a name="next-steps"></a>Další kroky

* [Připojit cluster ke službě Azure ARC](./connect-cluster.md)
* [Vytvoření konfigurací na clusteru Kubernetes s povoleným obloukem](./use-gitops-connected-cluster.md)
* [Použití Azure Policy k aplikování konfigurací ve velkém měřítku](./use-azure-policy.md)
