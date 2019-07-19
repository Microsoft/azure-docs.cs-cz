---
title: Postup povolení Azure Monitor pro kontejnery | Microsoft Docs
description: Tento článek popisuje, jak povolíte a nakonfigurujete Azure Monitor pro kontejnery, abyste porozuměli tomu, jak váš kontejner vykonává a jaké byly zjištěny problémy související s výkonem.
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
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: 25be8f166fec8a311fdc2ed1fa3fca6339185e94
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2019
ms.locfileid: "67867522"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Postup povolení Azure Monitor pro kontejnery

Tento článek poskytuje přehled možností, které jsou k dispozici pro nastavení Azure Monitor pro kontejnery pro monitorování výkonu úloh nasazených do prostředí Kubernetes a hostovaných ve [službě Azure Kubernetes](https://docs.microsoft.com/azure/aks/).

Azure Monitor pro kontejnery může být povoleno na nový, nebo nepodporuje jeden nebo více existujících nasazení AKS pomocí následující metody:

* Z Azure Portal, Azure PowerShell nebo pomocí Azure CLI
* Pomocí [Terraform a AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky
Než začnete, ujistěte se, že máte následující:

* **Log Analytics pracovní prostor.**

    Azure Monitor for Containers podporuje pracovní prostor Log Analytics v oblastech uvedených v [produktech Azure podle oblastí](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor), s výjimkou **US gov – Virginie**oblasti.

    Pracovní prostor můžete vytvořit, když povolíte monitorování nového clusteru AKS nebo necháte prostředí pro připojování vytvořit výchozí pracovní prostor ve výchozí skupině prostředků v rámci předplatného clusteru AKS. Pokud jste se rozhodli vytvořit sami, můžete vytvořit pomocí [Azure Resource Manageru](../platform/template-workspace-configuration.md), pomocí [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), nebo [webu Azure portal](../learn/quick-create-workspace.md). Seznam podporovaných dvojic mapování použitých pro výchozí pracovní prostor najdete v tématu [mapování oblastí pro Azure monitor kontejnerů](container-insights-region-mapping.md).

* Jste členem **role přispěvatel Log Analytics** , abyste povolili monitorování kontejnerů. Další informace o tom, jak řídit přístup k pracovnímu prostoru Log Analytics najdete v tématu [Správa pracovních prostorů](../platform/manage-access.md).

* Jste členem role **[vlastníka](../../role-based-access-control/built-in-roles.md#owner)** v prostředku clusteru AKS.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* Metriky Prometheus nejsou ve výchozím nastavení shromažďovány. Před [konfigurací agenta](container-insights-agent-config.md) pro jejich shromáždění je důležité si projít [dokumentaci](https://prometheus.io/) k Prometheus, abyste zjistili, co můžete definovat.

## <a name="components"></a>Komponenty

Vaše schopnost sledování výkonu spoléhá na kontejnerizovaných agenta Log Analytics pro Linux, konkrétně vyvinuté pro monitorování Azure pro kontejnery. Tento speciální agent shromažďuje data událostí výkonu a ze všech uzlů v clusteru, a automaticky agenta nasazení a registraci s zadaný pracovní prostor Log Analytics během nasazení. Verze agenta je microsoft / oms:ciprod04202018 nebo novější a představuje datum v následujícím formátu: *mmddyyyy*.

>[!NOTE]
>V rámci verze Preview podpory Windows serveru pro AKS má cluster AKS s uzly Windows serveru nainstalovaný Agent pro shromažďování dat a předávání do Azure Monitor. Místo toho se uzel Linux automaticky nasadí do clusteru jako součást standardního nasazení a přepošle data Azure Monitor jménem všech uzlů Windows v clusteru.  
>

Po vydání nové verze agenta, dojde k automatickému upgradu na vaše spravované clustery Kubernetes hostované ve službě Azure Kubernetes Service (AKS). Postupujte podle vydané verze, najdete v článku [oznámení verzi agenta](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Pokud už jste nasadili AKS cluster, povolte monitorování pomocí Azure CLI nebo zadané šablony Azure Resource Manageru, jak je uvedeno dále v tomto článku. Nemůžete použít `kubectl` k upgradu, odstranit, znovu nasadit nebo nasadit agenta.
>Šablona musí být nasazený ve stejné skupině prostředků jako cluster.

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

## <a name="next-steps"></a>Další postup

* Pomocí monitorování povoleno zachycení stavu metriky pro uzly clusteru AKS a podů, jsou tyto metriky stavu k dispozici na webu Azure Portal. Naučte se používat Azure Monitor pro kontejnery, najdete v článku [zobrazení Azure Kubernetes Service health](container-insights-analyze.md).
