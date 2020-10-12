---
title: Azure Security test Testing v2 – Správa prostředků
description: Správa prostředků Azure Security benchmark v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 845c4f3194f35c68306e74734e5ecc6109d9c103
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318020"
---
# <a name="security-control-v2-asset-management"></a>Řízení zabezpečení v2: Správa prostředků

Správa prostředků pokrývá ovládací prvky pro zajištění viditelnosti zabezpečení a zásad správného řízení pro prostředky Azure. To zahrnuje doporučení týkající se oprávnění pro bezpečnostní pracovníky, přístup k zabezpečení inventáře prostředků a správu schválení pro služby a prostředky (inventarizace, sledování a opravy).

## <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>DOP. 1: Zajistěte, aby měl tým zabezpečení přehled o rizicích pro prostředky

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| DOP. 1 | 1,1, 1,2 | CM-8, PM-5 |

Ujistěte se, že jsou bezpečnostním týmům udělená oprávnění čtenáře zabezpečení ve vašem tenantovi Azure a předplatných, aby mohli monitorovat bezpečnostní rizika pomocí Azure Security Center. 

V závislosti na tom, jak jsou strukturované zodpovědnosti týmu zabezpečení, může být monitorování rizik zabezpečení zodpovědností centrálního týmu zabezpečení nebo místního týmu. V takovém případě musí být přehledy a rizika zabezpečení vždy agregované centrálně v rámci organizace. 

Oprávnění čtenář zabezpečení je možné využít široce pro celého tenanta (kořenovou skupinu pro správu) nebo vymezenou pro skupiny pro správu nebo konkrétní předplatná. 

Poznámka: k získání přehledu o úlohách a službách může být potřeba další oprávnění. 

- [Přehled role čtecího modulu zabezpečení](../../role-based-access-control/built-in-roles.md#security-reader)

- [Přehled služby Azure Skupiny pro správu](../../governance/management-groups/overview.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>DOP. 2: Ujistěte se, že má tým zabezpečení přístup k inventáři prostředků a metadatům.

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| DOP. 2 | 1,1, 1,2, 1,4, 1,5, 9,1, 12,1 | CM-8, PM-5 |

Ujistěte se, že týmy zabezpečení mají přístup k průběžně aktualizovaným inventáři prostředků v Azure. Týmy zabezpečení často potřebují tento inventář, aby vyhodnotili potenciální expozici své organizace vznikajícím rizikům a jako vstup pro průběžná vylepšení zabezpečení. 

Funkce inventáře Azure Security Center a Azure Resource Graph se můžou dotazovat na všechny prostředky v předplatných, včetně služeb Azure, aplikací a síťových prostředků.  

Logicky organizovat prostředky podle taxonomie vaší organizace pomocí značek a dalších metadat v Azure (název, popis a kategorie).  

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource graphu](../../governance/resource-graph/first-query-portal.md)

- [Správa inventáře Azure Security Center prostředků](../../security-center/asset-inventory.md)

- [Další informace o označování prostředků najdete v průvodci pro pojmenování a označení prostředku.](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-3-use-only-approved-azure-services"></a>AM-3: Používejte jenom schválené služby Azure.

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| DOP. 3 | 2,3, 2,4 | CM-7, CM-8 |

Pomocí Azure Policy můžete auditovat a omezovat, které služby můžou uživatelé zřídit ve vašem prostředí. Pomocí Azure Resource graphu se můžete dotazovat a zjišťovat prostředky v rámci svých předplatných.  Pomocí Azure Monitor můžete také vytvořit pravidla pro aktivaci výstrah při zjištění neschválené služby.

- [Konfigurace a Správa Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Jak odepřít konkrétní typ prostředku pomocí Azure Policy](/azure/governance/policy/samples/not-allowed-resource-types)

- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource graphu](../../governance/resource-graph/first-query-portal.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

## <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>DOP. 4: zajištění zabezpečení správy životního cyklu prostředků

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| DOP. 4 | 2,3, 2,4, 2,5 | CM-7, CM-8, CM-10, CM-11 |

Stanovte nebo aktualizujte zásady zabezpečení, které řeší procesy správy životního cyklu prostředků pro potenciálně vysoce ovlivněné změny. Mezi tyto úpravy patří změny: zprostředkovatelé identity a přístup, citlivost dat, konfigurace sítě a přiřazení oprávnění správce.

Odeberte prostředky Azure, když už je nepotřebujete.

- [Odstranění prostředku a skupiny prostředků Azure](../../azure-resource-manager/management/delete-resource-group.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>DOP. 5: Omezte schopnost uživatelů pracovat s Azure Resource Manager

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| DOP. 5 | 2.9 | AC-3 |

Pomocí podmíněného přístupu Azure AD omezte schopnost uživatelů komunikovat s Azure Resource Manager konfigurací možnosti blokovat přístup pro aplikaci Microsoft Azure Management.

- [Jak nakonfigurovat podmíněný přístup k blokování přístupu k Azure Resource Manageru](../../role-based-access-control/conditional-access-azure-management.md)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="am-6-use-only-approved-applications-in-compute-resources"></a>DOP. 6: používejte ve výpočetních prostředcích jenom schválené aplikace.

| ID Azure | ID ovládacích prvků CIS v 7.1 | NIST SP800 – 53 R4 ID |
|--|--|--|--|
| DOP. 6 | 2,6, 2,7 | AC-3, CM-7, CM-8, CM-10, CM-11 |

Ujistěte se, že se spustí jenom autorizovaný software, a veškerý neautorizovaný software se zablokuje, aby se spustil v Azure Virtual Machines.

Použijte Adaptivní řízení aplikací Azure Security Center (ASC) pro zjišťování a generování seznamu povolených aplikací. Pomocí adaptivních řízení aplikací můžete také zajistit, aby se spustil pouze autorizovaný software a aby byl veškerý neautorizovaný software spuštěn v Azure Virtual Machines.

Pomocí Azure Automation Change Tracking a inventáře můžete automatizovat shromažďování informací o inventáři z virtuálních počítačů s Windows a Linux. Z Azure Portal je k dispozici název softwaru, verze, Vydavatel a čas aktualizace. Chcete-li získat datum instalace softwaru a další informace, povolte diagnostiku na úrovni hosta a nasměrujte protokoly událostí systému Windows tak, aby Log Analytics pracovní prostor.

V závislosti na typu skriptů můžete pomocí konfigurací specifických pro operační systém nebo prostředků třetích stran omezit schopnost uživatelů spouštět skripty ve výpočetních prostředcích Azure. 

K vyhledání a identifikaci neschváleného softwaru můžete také použít řešení třetí strany.

- [Jak používat Azure Security Center Adaptivní řízení aplikací](../../security-center/security-center-adaptive-application.md)

- [Pochopení Azure Automation Change Tracking a inventáře](../../automation/change-tracking.md)

- [Řízení spouštění skriptu PowerShellu v prostředích Windows](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Zodpovědnost**: zákazník

**Účastníci zabezpečení zákazníků** ([Další informace](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Zabezpečení infrastruktury a koncových bodů](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Správa stavu](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Správa dodržování předpisů zabezpečení](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

