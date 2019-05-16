---
title: Jak povolit Azure Monitor pro kontejnery | Dokumentace Microsoftu
description: Tento článek popisuje, jak povolit a nakonfigurovat monitorování Azure pro kontejnery, to vám umožní pochopit, jaký je výkon vašeho kontejneru a byly zjištěny problémy související s výkonem.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: 5e149fa96e0a62656804906b52adf10273321d17
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521900"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Jak povolit Azure Monitor pro kontejnery  

Tento článek obsahuje přehled dostupné možnosti a nastavení monitorování Azure pro kontejnery pro monitorování výkonu úlohy, které jsou nasazené do prostředí Kubernetes a hostuje ho na [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/).

Azure Monitor pro kontejnery může být povoleno na nový, nebo nepodporuje jeden nebo více existujících nasazení AKS pomocí následující metody:

* Z webu Azure portal, prostředí Azure PowerShell nebo pomocí rozhraní příkazového řádku Azure
* Pomocí [Terraform a AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky 
Než začnete, ujistěte se, že máte následující:

- **Pracovní prostor Log Analytics.** Můžete jej vytvořit při povolení monitorování nový cluster AKS, nebo nechte prostředí registrace vytvoření výchozího pracovního prostoru do výchozí skupiny prostředků předplatného clusteru AKS. Pokud jste se rozhodli vytvořit sami, můžete vytvořit pomocí [Azure Resource Manageru](../platform/template-workspace-configuration.md), pomocí [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), nebo [webu Azure portal](../learn/quick-create-workspace.md).
- Jste členem **role Přispěvatel Log Analytics** povolit monitorování kontejnerů. Další informace o tom, jak řídit přístup k pracovnímu prostoru Log Analytics najdete v tématu [Správa pracovních prostorů](../platform/manage-access.md).
- Jste členem **[vlastníka](../../role-based-access-control/built-in-roles.md#owner)** role v prostředku clusteru AKS. 

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>Komponenty 

Vaše schopnost sledování výkonu spoléhá na kontejnerizovaných agenta Log Analytics pro Linux, konkrétně vyvinuté pro monitorování Azure pro kontejnery. Tento speciální agent shromažďuje data událostí výkonu a ze všech uzlů v clusteru, a automaticky agenta nasazení a registraci s zadaný pracovní prostor Log Analytics během nasazení. Verze agenta je microsoft / oms:ciprod04202018 nebo novější a představuje datum v následujícím formátu: *mmddyyyy*. 

>[!NOTE]
>Cluster AKS pomocí uzly Windows serveru s verzí preview podpory systému Windows Server pro AKS, nemají agent nainstalovaný na shromažďování dat a předávat je Azure Monitor. Místo toho Linux uzlu v clusteru automaticky nasazeny jako součást standardního nasazení shromažďuje a předává data do Azure monitoru jménem všech uzlů Windows v clusteru.  
>

Po vydání nové verze agenta, dojde k automatickému upgradu na vaše spravované clustery Kubernetes hostované ve službě Azure Kubernetes Service (AKS). Postupujte podle vydané verze, najdete v článku [oznámení verzi agenta](https://github.com/microsoft/docker-provider/tree/ci_feature_prod). 

>[!NOTE] 
>Pokud už jste nasadili AKS cluster, povolte monitorování pomocí Azure CLI nebo zadané šablony Azure Resource Manageru, jak je uvedeno dále v tomto článku. Nemůžete použít `kubectl` k upgradu, odstranit, znovu nasadit nebo nasadit agenta. Šablona musí být nasazený ve stejné skupině prostředků jako cluster.

Azure Monitor je možné povolit pro kontejnery pomocí jednoho z následujících metod popsaných v následující tabulce.

| Stav nasazení | Metoda | Popis | 
|------------------|--------|-------------| 
| Nový cluster AKS | [Vytvoření clusteru pomocí rozhraní příkazového řádku Azure](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Můžete povolit monitorování nový cluster AKS, který vytvoříte pomocí Azure CLI. | 
| | [Vytvoření clusteru pomocí Terraformu](container-insights-enable-new-cluster.md#enable-using-terraform)| Můžete povolit monitorování nový cluster AKS, kterou vytvoříte pomocí open source nástroj Terraformu. | 
| Existující cluster AKS | [Povolení s využitím rozhraní příkazového řádku Azure](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Můžete povolit monitorování clusteru AKS už provedli nasazení pomocí rozhraní příkazového řádku Azure. | 
| |[Povolení s využitím Terraformu](container-insights-enable-existing-clusters.md#enable-using-terraform) | Můžete povolit monitorování clusteru AKS už provedli nasazení pomocí open source nástroj Terraformu. | 
| | [Povolit ze služby Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Můžete povolit monitorování jeden nebo více clusterů AKS, už nasazená ze stránky více clusteru AKS ve službě Azure Monitor. | 
| | [Povolit v clusteru AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Můžete povolit monitorování přímo z clusteru AKS na portálu Azure portal. | 
| | [Povolit pomocí šablony Azure Resource Manageru](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Můžete povolit monitorování clusteru AKS pomocí předem nakonfigurované šablony Azure Resource Manageru. | 

## <a name="next-steps"></a>Další postup

* Pomocí monitorování povoleno zachycení stavu metriky pro uzly clusteru AKS a podů, jsou tyto metriky stavu k dispozici na webu Azure Portal. Naučte se používat Azure Monitor pro kontejnery, najdete v článku [zobrazení Azure Kubernetes Service health](container-insights-analyze.md).
