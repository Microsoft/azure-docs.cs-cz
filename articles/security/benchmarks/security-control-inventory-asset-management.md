---
title: Řízení zabezpečení Azure – inventarizace a Správa prostředků
description: Inventář a Správa prostředků v Azure Security Control
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 7a5cd7a6019451b27b509aa7f486540c04c29ee2
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412642"
---
# <a name="security-control-inventory-and-asset-management"></a>Řízení zabezpečení: inventář a Správa prostředků

Doporučení k inventarizaci a správě prostředků se zaměřují na řešení problémů souvisejících s aktivně se správou (inventarizace, sledováním a opravou) všech prostředků Azure tak, aby byl přístup k nim udělen pouze autorizovaným prostředkům a identifikovali a odebrali neautorizované a nespravované prostředky

## <a name="61-use-automated-asset-discovery-solution"></a>6,1: použití řešení automatizovaného zjišťování prostředků

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 6.1 | 1,1, 1,2, 1,3, 1,4, 9,1, 12,1 | Zákazník |

Pomocí grafu prostředků Azure můžete v rámci vašich předplatných dotazovat nebo zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, síť, porty a protokoly atd.).  Zajistěte, aby ve vašem tenantovi byla vhodná (číst) oprávnění a aby se v rámci předplatných mohli vytvořit výčet všech předplatných Azure i prostředků

I když je možné zjistit klasické prostředky Azure pomocí grafu prostředků, důrazně doporučujeme vytvořit a používat prostředky Azure Resource Manager, které budou předány.

- [Jak vytvářet dotazy pomocí Azure Resource graphu](../../governance/resource-graph/first-query-portal.md)

- [Jak zobrazit vaše předplatná Azure](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Pochopení Azure RBAC](../../role-based-access-control/overview.md)

## <a name="62-maintain-asset-metadata"></a>6,2: Údržba metadat assetu

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 6,2 | 1.5 | Zákazník |

Použijte značky pro prostředky Azure poskytující metadata k logickému uspořádání do taxonomie.

- [Vytváření a používání značek](../../azure-resource-manager/management/tag-resources.md)

## <a name="63-delete-unauthorized-azure-resources"></a>6,3: odstranění neautorizovaných prostředků Azure

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 6.3 | 1.6 | Zákazník |

Používejte označení, skupiny pro správu a samostatné odběry, pokud je to vhodné, k organizování a sledování prostředků. Proveďte pravidelné sjednocení inventáře a zajistěte si včas odstranění neautorizovaných prostředků z předplatného.

- [Vytvoření dalších předplatných Azure](../../cost-management-billing/manage/create-subscription.md)

- [Postup vytvoření Skupiny pro správu](../../governance/management-groups/create-management-group-portal.md)

- [Vytváření a používání značek](../../azure-resource-manager/management/tag-resources.md)

## <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definování a údržba inventáře schválených prostředků Azure

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 6.4 | 2.1 | Zákazník |

Vytvořte inventarizaci schválených prostředků Azure a schváleného softwaru pro výpočetní prostředky podle potřeb organizace.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitorování neschválených prostředků Azure

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 6.5 | 2,3, 2,4 | Zákazník |

Pomocí Azure Policy můžete umístit omezení typu prostředků, které se dají vytvořit v předplatných.

Pomocí grafu prostředků Azure můžete v rámci svých předplatných dotazovat nebo zjišťovat prostředky.  Ujistěte se, že všechny prostředky Azure přítomné v daném prostředí jsou schválené.

- [Konfigurace a Správa Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Jak vytvářet dotazy pomocí Azure graphu](../../governance/resource-graph/first-query-portal.md)

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitorujte neschválené softwarové aplikace v rámci výpočetních prostředků.

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 6.6 | 2,3, 2,4 | Zákazník |

Pomocí inventáře virtuálních počítačů Azure můžete automatizovat shromažďování informací o veškerém softwaru v Virtual Machines. Z Azure Portal je k dispozici název softwaru, verze, Vydavatel a čas aktualizace. Chcete-li získat přístup k datu instalace a dalším informacím, povolte diagnostiku na úrovni hosta a přiveďte protokoly událostí systému Windows do Log Analyticsho pracovního prostoru.

- [Jak povolit inventář virtuálních počítačů Azure](../../automation/automation-tutorial-installed-software.md)

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Odeberte neschválené prostředky Azure a softwarové aplikace

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 6.7 | 2.5 | Zákazník |

K identifikaci veškerého softwaru nainstalovaného v Virtual Machines použijte Azure Security Center monitorování integrity souborů (Change Tracking) a inventář virtuálních počítačů. Můžete implementovat vlastní proces odebrání neautorizovaného softwaru. K identifikaci neschváleného softwaru můžete také použít řešení třetí strany.

- [Jak používat monitorování integrity souborů](../../security-center/security-center-file-integrity-monitoring.md)

- [Pochopení Change Tracking Azure](../../automation/change-tracking.md)

- [Jak povolit inventář virtuálních počítačů Azure](../../automation/automation-tutorial-installed-software.md)

## <a name="68-use-only-approved-applications"></a>6,8: Používejte pouze schválené aplikace.

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 6.8 | 2,6 | Zákazník |

Použijte Azure Security Center Adaptivní řízení aplikací, abyste zajistili, že se spustí jenom autorizovaný software, a veškerý neoprávněný software se zablokuje spouštění na Azure Virtual Machines.

- [Jak používat Azure Security Center Adaptivní řízení aplikací](../../security-center/security-center-adaptive-application.md)

## <a name="69-use-only-approved-azure-services"></a>6,9: Používejte jenom schválené služby Azure.

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 6.9 | 2,6 | Zákazník |

Pomocí Azure Policy můžete omezit, které služby můžete ve vašem prostředí zřídit.

- [Konfigurace a Správa Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../../governance/policy/samples/index.md)

## <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: udržování inventáře schválených softwarových titulů

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 6,10 | 2.7 | Zákazník |

Pomocí Azure Security Center Adaptivní řízení aplikací určíte, které typy souborů pravidlo může nebo nemusí platit.

Implementujte řešení třetích stran, pokud to nesplňuje požadavky.

- [Jak používat Azure Security Center Adaptivní řízení aplikací](../../security-center/security-center-adaptive-application.md)

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: Omezte schopnost uživatelů pracovat s Azure Resource Manager

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 6,11 | 2.9 | Zákazník |

Pomocí podmíněného přístupu Azure omezte schopnost uživatelů pracovat se správcem prostředků Azure tak, že pro aplikaci Microsoft Azure Management nakonfigurujete "blokování přístupu".

- [Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manageru](../../role-based-access-control/conditional-access-azure-management.md)

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Omezte schopnost uživatelů spouštět skripty ve výpočetních prostředcích.

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 6,12 | 2.9 | Zákazník |

V závislosti na typu skriptů můžete pomocí konfigurací specifických pro operační systém nebo prostředků třetích stran omezit schopnost uživatelů spouštět skripty v rámci výpočetních prostředků Azure.  Můžete také využít Azure Security Center Adaptivní řízení aplikací, abyste zajistili, že se spustí jenom autorizovaný software, a veškerý neautorizovaný software bude zablokovaný na Azure Virtual Machines.

- [Řízení spouštění skriptu PowerShellu v prostředích Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [Jak používat Azure Security Center Adaptivní řízení aplikací](../../security-center/security-center-adaptive-application.md)

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fyzicky nebo logicky oddělené aplikace s vysokým rizikem

| ID Azure | ID služby CI | Zodpovědní |
|--|--|--|
| 6,13 | 2.9 | Zákazník |

Software, který je vyžadován pro obchodní operace, ale může pro organizaci zvýšit riziko, by se měl izolovat v rámci vlastního virtuálního počítače nebo virtuální sítě a dostatečně zabezpečený pomocí Azure Firewall nebo skupiny zabezpečení sítě.

- [Jak vytvořit virtuální síť](../../virtual-network/quick-create-portal.md)

- [Vytvoření NSG s konfigurací zabezpečení](../../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-steps"></a>Další kroky

- Podívejte se na další ovládací prvek zabezpečení: [Zabezpečená konfigurace](security-control-secure-configuration.md)