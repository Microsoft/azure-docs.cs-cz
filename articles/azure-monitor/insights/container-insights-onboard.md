---
title: Postup povolení Azure Monitor pro kontejnery | Microsoft Docs
description: Tento článek popisuje, jak povolíte a nakonfigurujete Azure Monitor pro kontejnery, abyste porozuměli tomu, jak váš kontejner vykonává a jaké byly zjištěny problémy související s výkonem.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: dd58ec08c6ec372cf53a79b75162748cfe336b23
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73477124"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Postup povolení Azure Monitor pro kontejnery

Tento článek poskytuje přehled možností, které jsou k dispozici pro nastavení Azure Monitor pro kontejnery pro monitorování výkonu úloh, které jsou nasazeny do prostředí Kubernetes a hostované ve [službě Azure Kubernetes](https://docs.microsoft.com/azure/aks/), AKS Engine v [Azure Stack ](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)nebo Kubernetes nasazená místně.

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

* Metriky Prometheus nejsou ve výchozím nastavení shromažďovány. Před [konfigurací agenta](container-insights-prometheus-integration.md) pro jejich shromáždění je důležité si projít [dokumentaci](https://prometheus.io/) k Prometheus, abyste zjistili, co můžete definovat.

## <a name="network-firewall-requirements"></a>Požadavky na bránu firewall sítě

Informace v následující tabulce uvádí informace o konfiguraci proxy serveru a brány firewall vyžadované pro kontejnerový Agent ke komunikaci s Azure Monitor for Containers. Veškerý síťový provoz z agenta je odchozí na Azure Monitor.

|Prostředek agenta|Porty |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 | 
| *.blob.core.windows.net | 443 |
| dc.services.visualstudio.com | 443 |
| *.microsoftonline.com | 443 |
| *. monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

Informace v následující tabulce uvádí informace o konfiguraci proxy serveru a brány firewall pro Azure Čína.

|Prostředek agenta|Porty |Popis | 
|--------------|------|-------------|
| *. ods.opinsights.azure.cn | 443 | Přijímání dat |
| *. oms.opinsights.azure.cn | 443 | Připojování OMS |
| *.blob.core.windows.net | 443 | Používá se pro monitorování odchozího připojení. |
| microsoft.com | 80 | Používá se pro připojení k síti. To se vyžaduje jenom v případě, že verze image agenta je ciprod09262019 nebo starší. |
| dc.services.visualstudio.com | 443 | Pro telemetrii agenta pomocí Application Insights veřejného cloudu Azure. |

Informace v následující tabulce uvádí informace o konfiguraci proxy serveru a brány firewall pro vládu Azure USA.

|Prostředek agenta|Porty |Popis | 
|--------------|------|-------------|
| *. ods.opinsights.azure.us | 443 | Přijímání dat |
| *. oms.opinsights.azure.us | 443 | Připojování OMS |
| *.blob.core.windows.net | 443 | Používá se pro monitorování odchozího připojení. |
| microsoft.com | 80 | Používá se pro připojení k síti. To se vyžaduje jenom v případě, že verze image agenta je ciprod09262019 nebo starší. |
| dc.services.visualstudio.com | 443 | Pro telemetrii agenta pomocí Application Insights veřejného cloudu Azure. |

## <a name="components"></a>Komponenty

Vaše schopnost monitorovat výkon se spoléhá na Log Analyticsho agenta kontejnerů pro Linux speciálně vyvinutých pro Azure Monitor pro kontejnery. Tento specializovaný agent shromažďuje data o výkonu a událostech ze všech uzlů v clusteru a Agent je automaticky nasazen a zaregistrován v zadaném Log Analytics pracovním prostoru během nasazování. Verze agenta je Microsoft/OMS: ciprod04202018 nebo novější a představuje datum v následujícím formátu: *MMDDYYYY*.

>[!NOTE]
>V rámci verze Preview podpory Windows serveru pro AKS má cluster AKS s uzly Windows serveru nainstalovaný Agent pro shromažďování dat a předávání do Azure Monitor. Místo toho se uzel Linux automaticky nasadí do clusteru jako součást standardního nasazení a přepošle data Azure Monitor jménem všech uzlů Windows v clusteru.  
>

Po vydání nové verze agenta se automaticky upgraduje na spravovaných clusterech Kubernetes hostovaných ve službě Azure Kubernetes Service (AKS). Pokud chcete postupovat podle vydaných verzí, přečtěte si téma [oznámení o vydáních](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)

>[!NOTE]
>Pokud jste už nasadili cluster AKS, povolíte monitorování pomocí Azure CLI nebo poskytované Azure Resource Manager šablony, jak je znázorněno dále v tomto článku. `kubectl` nemůžete použít k upgradu, odstranění, opětovnému nasazení nebo nasazení agenta.
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
| | [Povolit pro hybridní cluster Kubernetes](container-insights-hybrid-setup.md) | Můžete povolit monitorování AKS modulu hostovaného v Azure Stack nebo pro Kubernetes hostované místně. |

## <a name="next-steps"></a>Další kroky

* Díky monitorování, které povoluje zachycení metrik stavu pro uzly clusteru AKS a lusky, jsou tyto metriky stavu dostupné v Azure Portal. Informace o tom, jak používat Azure Monitor pro kontejnery, najdete v tématu [zobrazení stavu služby Azure Kubernetes](container-insights-analyze.md).
