---
title: Postup povolení Azure Monitor pro kontejnery | Microsoft Docs
description: Tento článek popisuje, jak povolíte a nakonfigurujete Azure Monitor pro kontejnery, abyste porozuměli tomu, jak váš kontejner vykonává a jaké byly zjištěny problémy související s výkonem.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/12/2019
ms.openlocfilehash: 44cdc2d6b93ac9a62f96875ca6c679fbb97d85a9
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555399"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Postup povolení Azure Monitor pro kontejnery

Tento článek poskytuje přehled možností, které jsou k dispozici pro nastavení Azure Monitor pro kontejnery pro monitorování výkonu úloh nasazených do prostředí Kubernetes a hostovaných ve [službě Azure Kubernetes](https://docs.microsoft.com/azure/aks/).

Azure Monitor pro kontejnery lze povolit pro nové nebo jedno nebo více existujících nasazení AKS pomocí následujících podporovaných metod:

* Z Azure Portal, Azure PowerShell nebo pomocí Azure CLI
* Použití [terraformu a AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Předpoklady
Než začnete, ujistěte se, že máte následující:

* **Log Analytics pracovní prostor.**

    Azure Monitor for Containers podporuje pracovní prostor Log Analytics v oblastech uvedených v [produktech Azure podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

    Pracovní prostor můžete vytvořit, když povolíte monitorování nového clusteru AKS nebo necháte prostředí pro připojování vytvořit výchozí pracovní prostor ve výchozí skupině prostředků v rámci předplatného clusteru AKS. Pokud se rozhodnete ho vytvořit sami, můžete ho vytvořit prostřednictvím [Azure Resource Manager](../platform/template-workspace-configuration.md), prostřednictvím [PowerShellu](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)nebo v [Azure Portal](../learn/quick-create-workspace.md). Seznam podporovaných dvojic mapování použitých pro výchozí pracovní prostor najdete v tématu [mapování oblastí pro Azure monitor kontejnerů](container-insights-region-mapping.md).

* Jste členem **role přispěvatel Log Analytics** , abyste povolili monitorování kontejnerů. Další informace o tom, jak řídit přístup k pracovnímu prostoru Log Analytics, najdete v tématu [Správa pracovních prostorů](../platform/manage-access.md).

* Jste členem role **[vlastníka](../../role-based-access-control/built-in-roles.md#owner)** v prostředku clusteru AKS.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* Metriky Prometheus nejsou ve výchozím nastavení shromažďovány. Před [konfigurací agenta](container-insights-agent-config.md) pro jejich shromáždění je důležité si projít [dokumentaci](https://prometheus.io/) k Prometheus, abyste zjistili, co můžete definovat.

## <a name="components"></a>Komponenty

Vaše schopnost monitorovat výkon se spoléhá na Log Analyticsho agenta kontejnerů pro Linux speciálně vyvinutých pro Azure Monitor pro kontejnery. Tento specializovaný agent shromažďuje data o výkonu a událostech ze všech uzlů v clusteru a Agent je automaticky nasazen a zaregistrován v zadaném Log Analytics pracovním prostoru během nasazování. Verze agenta je Microsoft/OMS: ciprod04202018 nebo novější a představuje datum v následujícím formátu: *MMDDYYYY*.

>[!NOTE]
>V rámci verze Preview podpory Windows serveru pro AKS má cluster AKS s uzly Windows serveru nainstalovaný Agent pro shromažďování dat a předávání do Azure Monitor. Místo toho se uzel Linux automaticky nasadí do clusteru jako součást standardního nasazení a přepošle data Azure Monitor jménem všech uzlů Windows v clusteru.  
>

Po vydání nové verze agenta se automaticky upgraduje na spravovaných clusterech Kubernetes hostovaných ve službě Azure Kubernetes Service (AKS). Pokud chcete postupovat podle vydaných verzí, přečtěte si téma [oznámení o vydáních](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)

>[!NOTE]
>Pokud jste už nasadili cluster AKS, povolíte monitorování pomocí Azure CLI nebo poskytované Azure Resource Manager šablony, jak je znázorněno dále v tomto článku. @No__t_0 nemůžete použít k upgradu, odstranění, opětovnému nasazení nebo nasazení agenta.
>Šablona musí být nasazená ve stejné skupině prostředků jako cluster.

Azure Monitor pro kontejnery povolíte pomocí jedné z následujících metod popsaných v následující tabulce.

| Stav nasazení | Metoda | Popis |
|------------------|--------|-------------|
| Nový cluster AKS | [Vytvoření clusteru pomocí rozhraní příkazového řádku Azure](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Můžete povolit monitorování nového clusteru AKS, který vytvoříte pomocí Azure CLI. |
| | [Vytvoření clusteru pomocí Terraformu](container-insights-enable-new-cluster.md#enable-using-terraform)| Můžete povolit monitorování nového clusteru AKS, který vytvoříte pomocí nástroje Open Source Tool Terraformu. |
| Existující cluster AKS | [Povolit pomocí Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Můžete povolit monitorování clusteru AKS již nasazeného pomocí Azure CLI. |
| |[Povolit pomocí Terraformu](container-insights-enable-existing-clusters.md#enable-using-terraform) | Monitorování clusteru AKS, který je už nasazený, můžete povolit pomocí Open Source nástroje Terraformu. |
| | [Povolit z Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Monitorování jednoho nebo více clusterů AKS, které už jsou nasazené, můžete povolit na stránce s více clustery AKS v Azure Monitor. |
| | [Povolit z clusteru AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Monitorování můžete povolit přímo z clusteru AKS v Azure Portal. |
| | [Povolení použití šablony Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Můžete povolit monitorování clusteru AKS s předem nakonfigurovanou šablonou Azure Resource Manager. |

## <a name="next-steps"></a>Další kroky

* Díky monitorování, které povoluje zachycení metrik stavu pro uzly clusteru AKS a lusky, jsou tyto metriky stavu dostupné v Azure Portal. Informace o tom, jak používat Azure Monitor pro kontejnery, najdete v tématu [zobrazení stavu služby Azure Kubernetes](container-insights-analyze.md).
