---
title: Povolit službu Container Insights | Microsoft Docs
description: Tento článek popisuje, jak povolit a nakonfigurovat službu Container Insights, abyste mohli porozumět tomu, jak váš kontejner vykonává a jaké problémy byly zjištěny v souvislosti s výkonem.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 01246a728f204ed9cb43eee392c637b495208aaf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109331"
---
# <a name="enable-container-insights"></a>Povolit službu Container Insights

Tento článek poskytuje přehled možností, které jsou k dispozici pro nastavení služby Container Insights pro monitorování výkonu úloh nasazených do prostředí Kubernetes a hostovaných na:

- [Azure Kubernetes Service (AKS)](../../aks/index.yml)  
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) verze 3. x a 4. x  
- [Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) verze 4. x  
- [Kubernetes cluster s podporou ARC](../../azure-arc/kubernetes/overview.md)

Můžete také monitorovat výkon úloh, které jsou nasazeny na samoobslužně spravované clustery Kubernetes hostované na:
- Azure, pomocí [modulu AKS](https://github.com/Azure/aks-engine)
- [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) nebo místně pomocí modulu AKS.

Můžete povolit službu Container Insights pro nové nasazení nebo pro jedno nebo více existujících nasazení Kubernetes pomocí kterékoli z následujících podporovaných metod:

- Azure Portal
- Azure PowerShell
- Azure CLI
- [Terraformu a AKS](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že jste splnili následující požadavky:

> [!IMPORTANT]
> Log Analytics kontejner pro Linux (REPLICASET pod) zpřístupňuje rozhraní API všem uzlům Windows na zabezpečeném portu Kubelet (10250) v rámci clusteru ke shromáždění metrik souvisejících s výkonem uzlů a kontejnerů. Kubelet zabezpečený port (: 10250) by měl být otevřen v rámci virtuální sítě clusteru pro příchozí i odchozí shromažďování metrik souvisejících s výkonem uzlů Windows a kontejneru.
>
> Pokud máte cluster Kubernetes s uzly systému Windows, zkontrolujte a nakonfigurujte skupinu zabezpečení sítě a zásady sítě, abyste se ujistili, že je otevřený port Kubelet (: 10250) pro příchozí i odchozí připojení ve virtuální síti clusteru.


- Máte Log Analytics pracovní prostor.

   Služba Container Insights podporuje pracovní prostor Log Analytics v oblastech, které jsou uvedeny v [produktech dostupných v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

   Pracovní prostor můžete vytvořit, když povolíte monitorování pro nový cluster AKS, nebo můžete nechat prostředí pro připojování vytvořit výchozí pracovní prostor ve výchozí skupině prostředků v rámci předplatného clusteru AKS. 
   
   Pokud se rozhodnete vytvořit pracovní prostor sami, můžete ho vytvořit pomocí: 
   - [Azure Resource Manager](../logs/resource-manager-workspace.md)
   - [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)
   - [Azure Portal](../logs/quick-create-workspace.md) 
   
   Seznam podporovaných dvojic mapování, které se mají použít pro výchozí pracovní prostor, najdete v tématu [mapování oblastí pro službu Container Insights](container-insights-region-mapping.md).

- Jste členem skupiny *přispěvatelů Log Analytics* pro povolení monitorování kontejnerů. Další informace o tom, jak řídit přístup k pracovnímu prostoru Log Analytics, najdete v tématu [Správa pracovních prostorů](../logs/manage-access.md).

- Jste členem [skupiny *Owner*](../../role-based-access-control/built-in-roles.md#owner) v prostředku clusteru AKS.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- Chcete-li zobrazit data monitorování, musíte mít roli [*čtenář Log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) v pracovním prostoru Log Analytics, která je nakonfigurována pomocí kontejneru Insights.

- Metriky Prometheus nejsou ve výchozím nastavení shromažďovány. Předtím, než [nakonfigurujete agenta](container-insights-prometheus-integration.md) tak, aby shromáždil metriky, je důležité si projít [dokumentaci k Prometheus](https://prometheus.io/) , která vám pomůže pochopit, jaká data se dají vyřadit a jaké metody se podporují.
- Cluster AKS se dá připojit k pracovnímu prostoru Log Analytics v jiném předplatném Azure ve stejném Tenantovi Azure AD. Tuto možnost teď nejde udělat pomocí webu Azure Portal, ale můžete ji udělat pomocí Azure CLI nebo šablony Správce prostředků.

## <a name="supported-configurations"></a>Podporované konfigurace

Container Insights oficiálně podporuje následující konfigurace:

- Prostředí: Azure Red Hat OpenShift, Kubernetes v místním prostředí a modul AKS v Azure a Azure Stack. Další informace najdete v tématu [AKS Engine on Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview).
- Verze Kubernetes a zásady podpory jsou stejné jako ty, které jsou [podporované ve službě Azure Kubernetes Service (AKS)](../../aks/supported-kubernetes-versions.md). 

## <a name="network-firewall-requirements"></a>Požadavky na bránu firewall sítě

V následující tabulce jsou uvedené informace o konfiguraci proxy serveru a brány firewall, které jsou potřebné pro informování agentů s kontejnerem pro komunikaci se službou Container Insights. Veškerý síťový provoz z agenta je odchozí na Azure Monitor.

|Prostředek agenta|Port |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

V následující tabulce jsou uvedené informace o konfiguraci proxy serveru a brány firewall pro Azure Čína 21Vianet:

|Prostředek agenta|Port |Description | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | Přijímání dat |
| `*.oms.opinsights.azure.cn` | 443 | Připojování OMS |
| `dc.services.visualstudio.com` | 443 | Pro telemetrii agenta, která používá veřejné cloudy Azure Application Insights |

Následující tabulka uvádí informace o konfiguraci proxy serveru a brány firewall pro vládu Azure USA:

|Prostředek agenta|Port |Description | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | Přijímání dat |
| `*.oms.opinsights.azure.us` | 443 | Připojování OMS |
| `dc.services.visualstudio.com` | 443 | Pro telemetrii agenta, která používá veřejné cloudy Azure Application Insights |

## <a name="components"></a>Komponenty

Vaše schopnost monitorovat výkon se spoléhá na Log Analyticsho agenta kontejnerů pro Linux, který je speciálně vyvinutý pro službu Container Insights. Tento specializovaný agent shromažďuje data o výkonu a událostech ze všech uzlů v clusteru a Agent je automaticky nasazen a zaregistrován v zadaném Log Analytics pracovním prostoru během nasazování. 

Verze agenta je Microsoft/OMS: ciprod04202018 nebo novější a představuje datum v následujícím formátu: *MMDDYYYY*.

>[!NOTE]
>S obecnou dostupností podpory Windows serveru pro AKS má cluster AKS s uzly Windows serveru nainstalovaný Agent verze Preview jako daemonset pod na každém jednotlivém uzlu Windows serveru pro shromažďování protokolů a jejich přeposílání do Log Analytics. Pro metriky výkonu se uzel Linux, který je automaticky nasazen v clusteru, v rámci standardního nasazení shromažďují a přepošle data Azure Monitor jménem všech uzlů Windows v clusteru.

Po vydání nové verze agenta se automaticky upgraduje na spravované clustery Kubernetes, které jsou hostované ve službě Azure Kubernetes Service (AKS). Informace o vydaných verzích najdete v tématu [oznámení o vydáních agentů](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

> [!NOTE]
> Pokud jste už nasadili cluster AKS, povolili byste monitorování pomocí Azure CLI nebo poskytované Azure Resource Manager šablony, jak je znázorněno dále v tomto článku. Nemůžete použít `kubectl` k upgradu, odstranění, opětovnému nasazení nebo nasazení agenta.
>
> Šablona musí být nasazená ve stejné skupině prostředků jako cluster.

Pokud chcete povolit službu Container Insights, použijte jednu z metod, která je popsaná v následující tabulce:

| Stav nasazení | Metoda | Popis |
|------------------|--------|-------------|
| Nový cluster Kubernetes | [Vytvoření clusteru AKS pomocí Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Monitorování můžete povolit pro nový cluster AKS, který vytvoříte pomocí Azure CLI. |
| | [Vytvoření clusteru AKS pomocí Terraformu](container-insights-enable-new-cluster.md#enable-using-terraform)| Monitorování můžete povolit pro nový cluster AKS, který vytvoříte pomocí nástroje Open Source Tool Terraformu. |
| | [Vytvoření clusteru OpenShift pomocí šablony Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | Monitorování můžete povolit pro nový cluster OpenShift, který vytvoříte pomocí předkonfigurované šablony Azure Resource Manager. |
| | [Vytvoření clusteru OpenShift pomocí Azure CLI](/cli/azure/openshift#az-openshift-create) | Monitorování můžete povolit, když nasadíte nový cluster OpenShift pomocí Azure CLI. |
| Existující cluster Kubernetes | [Povolení monitorování clusteru AKS pomocí Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Můžete povolit monitorování pro cluster AKS, který je už nasazený pomocí Azure CLI. |
| |[Povolení pro cluster AKS pomocí Terraformu](container-insights-enable-existing-clusters.md#enable-using-terraform) | Můžete povolit monitorování pro cluster AKS, který je už nasazený, pomocí Open Source nástroje Terraformu. |
| | [Povolit pro AKS cluster z Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Monitorování můžete povolit pro jeden nebo víc clusterů AKS, které už jsou nasazené ze stránky s více clustery v Azure Monitor. |
| | [Povolit z clusteru AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Monitorování můžete povolit přímo z clusteru AKS v Azure Portal. |
| | [Povolení pro AKS cluster pomocí šablony Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Monitorování clusteru AKS můžete povolit pomocí šablony předkonfigurovaného Azure Resource Manager. |
| | [Povolit pro hybridní cluster Kubernetes](container-insights-hybrid-setup.md) | Můžete povolit monitorování pro modul AKS, který je hostovaný na Azure Stack, nebo pro cluster Kubernetes, který je hostovaný místně. |
| | [Povolte cluster Kubernetes s povoleným ARC](container-insights-enable-arc-enabled-clusters.md). | Můžete povolit monitorování pro clustery Kubernetes, které jsou hostované mimo Azure a jsou povolené pomocí ARC Azure. |
| | [Povolení pro OpenShift cluster pomocí šablony Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | Monitorování můžete povolit pro existující cluster OpenShift pomocí předkonfigurované šablony Azure Resource Manager. |
| | [Povolit pro OpenShift cluster z Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Monitorování můžete povolit pro jeden nebo víc clusterů OpenShift, které už jsou nasazené ze stránky s více clustery v Azure Monitor. |

## <a name="next-steps"></a>Další kroky

Teď, když jste povolili monitorování, můžete začít s analýzou výkonu clusterů Kubernetes hostovaných ve službě Azure Kubernetes Service (AKS), Azure Stack nebo jiném prostředí. Informace o používání služby Container Insights najdete v tématu [zobrazení výkonu clusteru Kubernetes](container-insights-analyze.md).
