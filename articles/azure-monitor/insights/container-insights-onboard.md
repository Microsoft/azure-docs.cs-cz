---
title: Jak povolit Azure Monitor pro kontejnery | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak povolit a nakonfigurovat Azure Monitor pro kontejnery, takže můžete pochopit, jak si váš kontejner vede a jaké problémy související s výkonem byly identifikovány.
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: 7aad7e7dd5ec2569377f9276c2e4793c7afd631a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275305"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Jak povolit Azure Monitor pro kontejnery

Tento článek obsahuje přehled možností, které jsou k dispozici pro nastavení Azure Monitor pro kontejnery pro sledování výkonu úloh, které jsou nasazeny do prostředí Kubernetes a hostované na:

- [Služba Azure Kubernetes](https://docs.microsoft.com/azure/aks/) (AKS)

- Samoobslužné clustery Kubernetes hostované v Azure pomocí [AKS Engine](https://github.com/Azure/aks-engine).

- Clustery Kubernetes s vlastním spravovaným pomocí Služby AKS Engine hostované v [Azure Stacku](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) nebo v místním prostředí.

- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

Azure Monitor pro kontejnery můžete povolit pro nové nebo jedno nebo více existujících nasazení Kubernetes pomocí následujících podporovaných metod:

- Z webu Azure Portal, Azure PowerShellu nebo pomocí azure cli

- Použití [Terraform a AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte následující:

- **Pracovní prostor služby Log Analytics.**

    Azure Monitor pro kontejnery podporuje pracovní prostor Analýzy protokolů v oblastech uvedených v [Azure Products podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

    Pracovní prostor můžete vytvořit, když povolíte monitorování nového clusteru AKS nebo necháte možnost i připojení vytvořit výchozí pracovní prostor ve výchozí skupině prostředků předplatného clusteru AKS. Pokud jste se rozhodli vytvořit sami, můžete ji vytvořit prostřednictvím [Azure Resource Manager](../platform/template-workspace-configuration.md), prostřednictvím [PowerShellu](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)nebo na [webu Azure Portal](../learn/quick-create-workspace.md). Seznam podporovaných dvojic mapování použitých pro výchozí pracovní prostor najdete v tématu [Mapování oblasti pro Azure Monitor pro kontejnery](container-insights-region-mapping.md).

- Jste členem role **přispěvatele Log Analytics** povolit monitorování kontejnerů. Další informace o řízení přístupu k pracovnímu prostoru Log Analytics naleznete v [tématu Správa pracovních prostorů](../platform/manage-access.md).

- Jste členem role **[Vlastník](../../role-based-access-control/built-in-roles.md#owner)** prostředku clusteru AKS.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* Metriky Prometheus se ve výchozím nastavení neshromažďují. Před [konfigurací agenta](container-insights-prometheus-integration.md) pro jejich shromažďování je důležité, abyste si [přečetli dokumentaci](https://prometheus.io/) společnosti Prometheus, abyste pochopili, co lze oškrábat a podporované metody.

## <a name="supported-configurations"></a>Podporované konfigurace

Následující je oficiálně podporována pomocí Azure Monitor pro kontejnery.

- Prostředí: Azure Red Hat OpenShift, Kubernetes místní a AKS Engine v Azure a Azure Stack. Další informace najdete v tématu [AKS Engine na Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- Verze Kubernetes a zásady podpory jsou stejné jako verze [podporovaných AKS](../../aks/supported-kubernetes-versions.md). 

## <a name="network-firewall-requirements"></a>Požadavky na síťovou bránu firewall

Informace v následující tabulce uvádí proxy server a brány firewall informace o konfiguraci potřebné pro kontejnerizovaného agenta ke komunikaci s Azure Monitor pro kontejnery. Veškerý síťový provoz od agenta je odchozí na Azure Monitor.

|Prostředek agenta|Porty |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 | 
| *.blob.core.windows.net | 443 |
| dc.services.visualstudio.com | 443 |
| *.microsoftonline.com | 443 |
| *.monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

Informace v následující tabulce uvádí proxy server a informace o konfiguraci brány firewall pro Azure China.

|Prostředek agenta|Porty |Popis | 
|--------------|------|-------------|
| *.ods.opinsights.azure.cn | 443 | Přijímání dat |
| *.oms.opinsights.azure.cn | 443 | Registrace OMS |
| *.blob.core.windows.net | 443 | Používá se pro sledování odchozího připojení. |
| microsoft.com | 80 | Používá se pro připojení k síti. To je vyžadováno pouze v případě, že verze image agenta je ciprod09262019 nebo starší. |
| dc.services.visualstudio.com | 443 | Pro telemetrická data agenta pomocí Přehledy veřejných cloudových aplikací Azure. |

Informace v následující tabulce uvádí proxy server a informace o konfiguraci brány firewall pro Azure US Government.

|Prostředek agenta|Porty |Popis | 
|--------------|------|-------------|
| *.ods.opinsights.azure.us | 443 | Přijímání dat |
| *.oms.opinsights.azure.us | 443 | Registrace OMS |
| *.blob.core.windows.net | 443 | Používá se pro sledování odchozího připojení. |
| microsoft.com | 80 | Používá se pro připojení k síti. To je vyžadováno pouze v případě, že verze image agenta je ciprod09262019 nebo starší. |
| dc.services.visualstudio.com | 443 | Pro telemetrická data agenta pomocí Azure Public Cloud Application Insights. |

## <a name="components"></a>Komponenty

Vaše schopnost monitorovat výkon závisí na kontejnerizovaném agentovi Log Analytics pro Linux speciálně vyvinutém pro Azure Monitor pro kontejnery. Tento specializovaný agent shromažďuje data o výkonu a událostech ze všech uzlů v clusteru a agent se automaticky nasadí a zaregistruje v zadaném pracovním prostoru Log Analytics během nasazení. Verze agenta je microsoft/oms:ciprod04202018 nebo novější a je reprezentována datem v následujícím formátu: *mmddyyyy .*

>[!NOTE]
>Díky předběžné verzi podpory Windows Serveru pro AKS nemá cluster AKS s uzly Windows Serveru nainstalovaného agenta, který by shromažďoval data a přesměrovává do Azure Monitoru. Místo toho uzel Linuxu automaticky nasazený v clusteru jako součást standardního nasazení shromažďuje a předává data do Azure Monitoru jménem všech uzlů Windows v clusteru.  
>

Když je vydána nová verze agenta, je automaticky upgradována na spravovaných clusterech Kubernetes hostovaných ve službě Azure Kubernetes Service (AKS). Chcete-li sledovat vydané verze, [přečtěte si informace o vydání agenta](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Pokud jste již nasadili cluster AKS, povolíte monitorování pomocí azure cli nebo poskytnuté šablony Azure Resource Manager, jak je znázorněno dále v tomto článku. Agenta `kubectl` nelze použít k upgradu, odstranění, opětovnému nasazení nebo nasazení agenta.
>Šablonu je třeba nasadit ve stejné skupině prostředků jako cluster.

Azure Monitor pro kontejnery povolíte pomocí jedné z následujících metod popsaných v následující tabulce.

| Stav nasazení | Metoda | Popis |
|------------------|--------|-------------|
| Nový cluster AKS Kubernetes | [Vytvoření clusteru AKS pomocí azure cli](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Můžete povolit monitorování nového clusteru AKS, který vytvoříte pomocí azure CLI. |
| | [Vytvoření clusteru AKS pomocí Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Můžete povolit monitorování nového clusteru AKS, který vytvoříte pomocí open source nástroje Terraform. |
| | [Vytvoření clusteru OpenShift pomocí šablony Správce prostředků Azure](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | Monitorování nového clusteru OpenShift, který vytvoříte, můžete povolit pomocí předkonfigurované šablony Správce prostředků Azure. |
| | [Vytvoření clusteru OpenShift pomocí azure cli](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | Monitorování můžete povolit při nasazování nového clusteru OpenShift pomocí azure cli. |
| Existující cluster AKS Kubernetes | [Povolení clusteru AKS pomocí azure cli](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Můžete povolit monitorování clusteru AKS, který je už nasazený pomocí azure cli. |
| |[Povolit cluster AKS pomocí Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Můžete povolit monitorování clusteru AKS, který je již nasazen pomocí open source nástroje Terraform. |
| | [Povolení clusteru AKS z Azure Monitoru](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Můžete povolit monitorování jednoho nebo více clusterů AKS, které jsou už nasazené ze stránky více clusterů v Azure Monitoru. |
| | [Povolení z clusteru AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Monitorování můžete povolit přímo z clusteru AKS na webu Azure Portal. |
| | [Povolení pro cluster AKS pomocí šablony Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Monitorování clusteru AKS můžete povolit pomocí předem nakonfigurované šablony Správce prostředků Azure. |
| | [Povolit pro hybridní cluster Kubernetes](container-insights-hybrid-setup.md) | Můžete povolit monitorování AKS Engine hostované v Azure Stack nebo pro Kubernetes hostované místně. |
| | [Povolení pro cluster OpenShift pomocí šablony Správce prostředků Azure](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | Monitorování existujícího clusteru OpenShift můžete povolit pomocí předem nakonfigurované šablony Správce prostředků Azure. |
| | [Povolení pro cluster OpenShift z Azure Monitoru](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Můžete povolit monitorování jednoho nebo více clusterů OpenShift, které jsou již nasazeny ze stránky více clusterů v Azure Monitoru. |

## <a name="next-steps"></a>Další kroky

- S povoleným monitorováním můžete začít analyzovat výkon clusterů Kubernetes hostovaných ve službě Azure Kubernetes Service (AKS), Azure Stack nebo v jiném prostředí. Informace o tom, jak používat Azure Monitor pro kontejnery, najdete [v tématu Zobrazení výkonu clusteru Kubernetes](container-insights-analyze.md).
