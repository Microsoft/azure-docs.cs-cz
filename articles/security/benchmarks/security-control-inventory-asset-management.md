---
title: Řízení zabezpečení Azure – inventarizace a Správa prostředků
description: Inventář řízení zabezpečení a Správa prostředků
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 2f24012a69a7673426644a8ded6162cd1021938e
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930059"
---
# <a name="security-control-inventory-and-asset-management"></a>Řízení zabezpečení: inventář a Správa prostředků

Doporučení k inventarizaci a správě prostředků se zaměřují na řešení problémů souvisejících s aktivně se správou (inventarizace, sledováním a opravou) všech prostředků Azure tak, aby přístup k nim měly jenom autorizované prostředky a neautorizované a nespravované prostředky. identifikováno a odebráno.

## <a name="61-use-azure-asset-discovery"></a>6,1: použijte Azure Asset Discovery.

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 6.1 | 1,1, 1,2, 1,3, 1,4, 9,1, 12,1 | Zákazník |

Pomocí grafu prostředků Azure můžete v rámci vašich předplatných dotazovat nebo zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť, porty a protokoly atd.).  Zajistěte, aby ve vašem tenantovi byla vhodná (číst) oprávnění a aby se v rámci předplatných mohli vytvořit výčet všech předplatných Azure i prostředků

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme vytvořit a používat prostředky Azure Resource Manager, které budou předány.

Jak vytvářet dotazy pomocí Azure Resource graphu:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak zobrazit vaše předplatná Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Porozumění službě Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

## <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 6.2 | 1,5 | Zákazník |

Použijte značky pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie.

Vytváření a používání značek:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 6.3 | 1.6 | Zákazník |

Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

Jak vytvořit další předplatná Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Postup vytvoření Skupiny pro správu:

https://docs.microsoft.com/azure/governance/management-groups/create

Vytváření a používání značek:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: udržování inventáře schválených prostředků Azure a softwarových titulů

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 6.4 | 2.1 | Zákazník |

Definujte schválené prostředky Azure a schválený software pro výpočetní prostředky.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 6.5 | 2.3, 2.4 | Zákazník |

Pomocí Azure Policy můžete umístit omezení typu prostředků, které se dají vytvořit v předplatných.

Pomocí grafu prostředků Azure můžete v rámci svých předplatných dotazovat nebo zjišťovat prostředky. &nbsp;zajistěte, aby všechny prostředky Azure přítomné v daném prostředí byly schválené.

Jak nakonfigurovat a spravovat Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak vytvářet dotazy pomocí Azure graphu:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 6.6 | 2.3/2.4 | Zákazník |

Pomocí inventáře virtuálních počítačů Azure můžete automatizovat shromažďování informací o veškerém softwaru v Virtual Machines. Z Azure Portal je k dispozici název softwaru, verze, Vydavatel a čas aktualizace. Chcete-li získat přístup k datu instalace a dalším informacím, povolte diagnostiku na úrovni hosta a přiveďte protokoly událostí systému Windows do Log Analyticsho pracovního prostoru.

Jak povolit inventář virtuálních počítačů Azure:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 6.7 | 2.5 | Zákazník |

K identifikaci veškerého softwaru nainstalovaného v Virtual Machines použijte Azure Security Center monitorování integrity souborů (Change Tracking) a inventář virtuálních počítačů. Můžete implementovat vlastní proces odebrání neautorizovaného softwaru. K identifikaci neschváleného softwaru můžete také použít řešení třetí strany.

Jak používat monitorování integrity souborů:

https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring

Pochopení Change Tracking Azure:

https://docs.microsoft.com/azure/automation/change-tracking

Jak povolit inventář virtuálních počítačů Azure:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 6.8 | 2,6 | Zákazník |

Použijte Azure Security Center Adaptivní řízení aplikací, abyste zajistili, že se spustí jenom autorizovaný software, a veškerý neoprávněný software se zablokuje spouštění na Azure Virtual Machines.

Jak používat Azure Security Center Adaptivní řízení aplikací:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 6.9 | 2,6 | Zákazník |

Pomocí Azure Policy můžete omezit, které služby můžete ve vašem prostředí zřídit.

Jak nakonfigurovat a spravovat Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odepřít konkrétní typ prostředku s Azure Policy:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

## <a name="610-implement-approved-application-list"></a>6,10: Implementujte schválený seznam aplikací

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 6.1 | 2.7 | Zákazník |

Pomocí Azure Security Center Adaptivní řízení aplikací určíte, které typy souborů pravidlo může nebo nemusí platit.

Implementujte řešení třetích stran, pokud to nesplňuje požadavky.

Jak používat Azure Security Center Adaptivní řízení aplikací:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager prostřednictvím skriptů

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 6,11 | 2.8 | Zákazník |

Pomocí podmíněného přístupu Azure omezte schopnost uživatelů komunikovat s Azure Resource Manager konfigurací &quot;blokování přístupu&quot; pro aplikaci &quot;Microsoft Azure Management&quot;.

Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manager:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 6,12 | 2.8 | Zákazník |

Pomocí konfigurací specifických pro operační systém nebo prostředků třetích stran omezte schopnost uživatelů spouštět skripty v rámci výpočetních prostředků Azure.

Například způsob řízení spouštění skriptu prostředí PowerShell v prostředích systému Windows:

https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

| ID Azure | ID služby CI | Odpovědnost |
|--|--|--|
| 6,13 | 2.9 | Zákazník |

Software, který je vyžadován pro obchodní operace, ale může pro organizaci zvýšit riziko, by se měl izolovat v rámci vlastního virtuálního počítače nebo virtuální sítě a dostatečně zabezpečený pomocí Azure Firewall nebo skupiny zabezpečení sítě.

Jak vytvořit virtuální síť:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Vytvoření NSG s konfigurací zabezpečení:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="next-steps"></a>Další kroky

Podívejte se na další ovládací prvek zabezpečení: [Zabezpečená konfigurace](security-control-secure-configuration.md)
