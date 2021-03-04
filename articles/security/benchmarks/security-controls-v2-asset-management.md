---
title: Azure Security test Testing v2 – Správa prostředků
description: Správa prostředků Azure Security benchmark v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 32b0a7e31fc0d595eacc2bf5257f41e4ce35566b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735807"
---
# <a name="security-control-v2-asset-management"></a>Řízení zabezpečení v2: Správa prostředků

Správa prostředků pokrývá ovládací prvky pro zajištění viditelnosti zabezpečení a zásad správného řízení pro prostředky Azure. To zahrnuje doporučení týkající se oprávnění pro bezpečnostní pracovníky, přístup k zabezpečení inventáře prostředků a správu schválení pro služby a prostředky (inventarizace, sledování a opravy).

Pokud se chcete podívat na příslušný integrovaný Azure Policy, přečtěte si [Podrobnosti o integrované iniciativě pro Azure Security test dodržování předpisů: zabezpečení sítě.](../../governance/policy/samples/azure-security-benchmark#asset-management)

## <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Zajištění přehledu o rizicích u prostředků pro bezpečnostní tým

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| DOP. 1 | 1,1, 1,2 | CM-8, PM-5 |

Ujistěte se, že jsou bezpečnostním týmům udělená oprávnění čtenáře zabezpečení ve vašem tenantovi Azure a předplatných, aby mohli monitorovat bezpečnostní rizika pomocí Azure Security Center.

V závislosti na tom, jak jsou strukturované zodpovědnosti týmu zabezpečení, může být monitorování rizik zabezpečení zodpovědností centrálního týmu zabezpečení nebo místního týmu. Přehledy zabezpečení a bezpečnostní rizika se však v rámci organizace musí vždy agregovat centrálně. 

Oprávnění Čtenář zabezpečení je možné přidělit pro celého tenanta (kořenová skupina pro správu) nebo je vymezit na konkrétní skupiny pro správu nebo konkrétní předplatná. 

Poznámka: K získání přehledu o úlohách a službách se můžou vyžadovat další oprávnění.

- [Přehled role Čtenář zabezpečení](../../role-based-access-control/built-in-roles.md#security-reader)

- [Přehled skupin pro správu Azure](../../governance/management-groups/overview.md)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Zajištění přístupu k inventáři a metadatům prostředků pro bezpečnostní tým

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| DOP. 2 | 1,1, 1,2, 1,4, 1,5, 9,1, 12,1 | CM-8, PM-5 |

Ujistěte se, že týmy zabezpečení mají přístup k průběžně aktualizovaným inventáři prostředků v Azure. Bezpečnostní týmy tento inventář často potřebují k tomu, aby vyhodnotily potenciální slabá místa organizace vůči vznikajícím rizikům, a jako vstup pro průběžná vylepšování zabezpečení. 

Funkce inventáře Azure Security Center a Azure Resource Graph se můžou dotazovat na všechny prostředky v předplatných, včetně služeb Azure, aplikací a síťových prostředků.

Logicky organizovat prostředky podle taxonomie vaší organizace pomocí značek a dalších metadat v Azure (název, popis a kategorie).

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../../governance/resource-graph/first-query-portal.md)

- [Správa inventáře Azure Security Center prostředků](../../security-center/asset-inventory.md)

- [Další informace o označování prostředků najdete v průvodci pro pojmenování a označení prostředku.](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-3-use-only-approved-azure-services"></a>AM-3: Používání jenom schválených služeb Azure

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| DOP. 3 | 2,3, 2,4 | CM-7, CM-8 |

Pomocí Azure Policy můžete auditovat a omezovat služby, které můžou uživatelé zřídit ve vašem prostředí. Pomocí Azure Resource Graphu se můžete dotazovat na prostředky v rámci jejich předplatných a zjišťovat je. Pomocí Azure Monitoru můžete také vytvořit pravidla pro aktivaci upozornění při zjištění neschválené služby.

- [Konfigurace a Správa Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](../../governance/policy/samples/index.md)

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../../governance/resource-graph/first-query-portal.md)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Zajištění zabezpečení správy životního cyklu prostředků

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| DOP. 4 | 2,3, 2,4, 2,5 | CM-7, CM-8, CM-10, CM-11 |

Stanovte nebo aktualizujte zásady zabezpečení, které řeší procesy správy životního cyklu prostředků pro potenciálně vysoce ovlivněné změny. Mezi tyto úpravy patří změny zprostředkovatelů identit a přístupu, citlivosti dat, konfigurace sítě nebo přiřazení oprávnění správce.

Pokud už je nepotřebujete, odeberte prostředky Azure.

- [Odstranění prostředku a skupiny prostředků Azure](../../azure-resource-manager/management/delete-resource-group.md)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>DOP. 5: Omezte schopnost uživatelů pracovat s Azure Resource Manager

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| DOP. 5 | 2.9 | AC-3 |

Pomocí podmíněného přístupu Azure AD omezte schopnost uživatelů komunikovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management.

- [Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manageru](../../role-based-access-control/conditional-access-azure-management.md)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="am-6-use-only-approved-applications-in-compute-resources"></a>DOP. 6: používejte ve výpočetních prostředcích jenom schválené aplikace.

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP 800-53 R4 ID (s) |
|--|--|--|--|
| DOP. 6 | 2,6, 2,7 | AC-3, CM-7, CM-8, CM-10, CM-11 |

Ujistěte se, že se spustí jenom autorizovaný software, a veškerý neautorizovaný software se zablokuje, aby se spustil v Azure Virtual Machines.

Použijte Adaptivní řízení aplikací Azure Security Center (ASC) pro zjišťování a generování seznamu povolených aplikací. Pomocí adaptivních řízení aplikací můžete také zajistit, aby se spustil pouze autorizovaný software a aby byl veškerý neautorizovaný software spuštěn v Azure Virtual Machines.

Pomocí Azure Automation Change Tracking a inventáře můžete automatizovat shromažďování informací o inventáři z virtuálních počítačů s Windows a Linux. Z Azure Portal je k dispozici název softwaru, verze, Vydavatel a čas aktualizace. Chcete-li získat datum instalace softwaru a další informace, povolte diagnostiku na úrovni hosta a nasměrujte protokoly událostí systému Windows tak, aby Log Analytics pracovní prostor.

V závislosti na typu skriptů můžete pomocí konfigurací specifických pro operační systém nebo prostředků třetích stran omezit schopnost uživatelů spouštět skripty ve výpočetních prostředcích Azure.

K vyhledání a identifikaci neschváleného softwaru můžete také použít řešení třetí strany.

- [Jak používat Azure Security Center Adaptivní řízení aplikací](../../security-center/security-center-adaptive-application.md)

- [Pochopení Azure Automation Change Tracking a inventáře](../../automation/change-tracking/overview.md)

- [Řízení spouštění skriptu PowerShellu v prostředích Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Odpovědnost:** Zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)
