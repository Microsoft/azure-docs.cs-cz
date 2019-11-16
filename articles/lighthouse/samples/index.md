---
title: Šablony a ukázky pro Azure Lighthouse
description: Tyto ukázky a šablony Azure Resource Manageru ukazují, jak nasadit zákazníky pro správu delegovaných prostředků Azure a podporovat scénářů služby Azure Lighthouse.
ms.topic: sample
ms.date: 10/17/2019
ms.openlocfilehash: 20c1fc62c83202f97ed9cff68fd285280d94a321
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132167"
---
# <a name="azure-lighthouse-samples"></a>Ukázky služby Azure Lighthouse

Následující tabulka obsahuje odkazy na šablony Azure Resource Manageru pro Azure Lighthouse. Tyto a další soubory najdete také v [úložišti ukázek služby Azure Lighthouse](https://github.com/Azure/Azure-Lighthouse-samples/).

## <a name="onboarding-customers-to-azure-delegated-resource-management"></a>Onboarding zákazníků do správy delegovaných prostředků Azure

Poskytujeme šablony zaměřené na konkrétní scénáře onboardingu. Zvolte si možnost, která vám nejlépe vyhovuje, a nezapomeňte upravit soubor parametrů tak, aby odpovídal vašemu prostředí. Další informace o tom, jak používat tyto soubory ve vašem nasazení, najdete v tématu [Onboard zákazníka do delegované správy prostředků Azure ](../how-to/onboard-customer.md).

| **Šablona** | **Popis** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Nasazení předplatných zákazníků do správy delegovaných prostředků Azure Pro každé předplatné se musí provést samostatné nasazení. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Nasaďte jednu nebo několik zákaznických skupin prostředků do správy delegovaných prostředků Azure. K nasazení jedné skupiny prostředků použijte **rgDelegatedResourceManagement**, k nasazení několika skupin prostředků ve stejném předplatném použijte **multipleRgDelegatedResourceManagement**. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Pokud jste [publikovali nabídku spravovaných služeb na Azure Marketplace ](../how-to/publish-managed-services-offers.md), můžete tuto šablonu volitelně použít k nasazení prostředků pro zákazníky, kteří tuto nabídku využili. Hodnoty pro marketplace v souboru parametrů se musí shodovat s hodnotami, které jste použili při publikování vaší nabídky. |

Obvykle je potřeba pro každé nasazované předplatné samostatné nasazení, ale můžete také nasadit šablony v rámci více předplatných.

| **Šablona** | **Popis** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Nasazení šablon Azure Resource Manageru napříč několika předplatnými |

## <a name="azure-policy"></a>Azure Policy

Tyto ukázky ukazují, jak používat Azure Policy s předplatnými, která jsou nasazená pro správu delegovaných prostředků Azure.

| **Šablona** | **Popis** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) | Přiřadí zásadu, která pro delegované předplatné přidá nebo odebere značku (pomocí efektu modify). Další informace najdete v tématu věnovaném [nasazení zásady, kterou je možné v rámci delegovaného předplatného napravit](../how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-audit-delegation) | Přiřadí zásadu, která bude auditovat přiřazení delegování. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) | Přiřadí zásadu, která umožňuje diagnostiku prostředků služby Azure Key Vault v delegovaných předplatných (pomocí efektu deployIfNotExists). Další informace najdete v tématu věnovaném [nasazení zásady, kterou je možné v rámci delegovaného předplatného napravit](../how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-sub-monitoring) | Přiřadí několik zásad pro umožnění diagnostiky v delegovaném předplatném a připojí všechny virtuální počítače s Windows a Linuxem k pracovnímu prostoru Log Analytics vytvořenému touto zásadou. Další informace najdete v tématu věnovaném [nasazení zásady, kterou je možné v rámci delegovaného předplatného napravit](../how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-initiative) | Aplikuje iniciativu (několik souvisejících definic zásad) na delegované předplatné. |

## <a name="azure-monitor"></a>Azure Monitor

Tyto ukázky ukazují, jak používat Azure Monitor k vytváření upozornění pro předplatná, která jsou nasazená pro správu delegovaných prostředků Azure.

| **Šablona** | **Popis** |
|---------|---------|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | Tato šablona vytvoří upozornění Azure a připojí ho k existující skupině akcí.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Vytvoří několik upozornění protokolu na základě dotazů Kusto.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | Nasadí upozornění do tenanta, když uživatel deleguje předplatné spravovanému tenantovi.|

## <a name="additional-cross-tenant-scenarios"></a>Další scénáře pro více tenantů

Tyto ukázky ukazují různé úlohy, které je možné provádět ve scénářích správy pro více tenantů.

| **Šablona** | **Popis** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | Nasadí účty úložiště do dvou různých skupin prostředků.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Vytvoření služeb pro správu Azure, jejich propojení a nasazení dalších řešení Ke kompletnímu nasazení použijte šablonu **rgWithAzureMgmt.json**. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | Povolení a konfigurace služby Azure Security Center v rámci cílového předplatného Azure |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-sentinel) | Nasadí a povolí Azure Sentinel pro stávající pracovní prostor Log Analytics v delegovaném předplatném. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-log-analytics-vm-extensions) | Tyto šablony umožňují nasadit rozšíření Log Analytics do virtuálních počítačů s Windows a Linuxem a připojit je k určenému pracovnímu prostoru Log Analytics. |

## <a name="next-steps"></a>Další kroky

- Další informace o [správě delegovaných prostředků Azure](../concepts/azure-delegated-resource-management.md)
- Projděte si [úložiště ukázek služby Azure Lighthouse](https://github.com/Azure/Azure-Lighthouse-samples/).
