---
title: Řízení zabezpečení Azure – správa inventáře a majetku
description: Inventář kontroly zabezpečení a správa majetku
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 2f24012a69a7673426644a8ded6162cd1021938e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75930059"
---
# <a name="security-control-inventory-and-asset-management"></a>Řízení zabezpečení: Správa zásob a majetku

Doporučení pro správu inventáře a majetku se zaměřují na řešení problémů souvisejících s aktivní správou (inventář, sledování a oprava) všech prostředků Azure, takže přístup mají pouze autorizované prostředky a neautorizované a nespravované prostředky jsou identifikovány a odstraněny.

## <a name="61-use-azure-asset-discovery"></a>6.1: Použití Azure Asset Discovery

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 6.1 | 1.1, 1.2, 1.3, 1.4, 9.1, 12.1 | Zákazník |

Pomocí Azure Resource Graph můžete v rámci předplatného dotazovat/zjišťovat všechny prostředky (například výpočetní prostředky, úložiště, sítě, porty a protokoly atd.).  Zajistěte příslušná (čtecí) oprávnění ve vašem tenantovi a vyjmenovali všechna předplatná Azure a také prostředky v rámci vašich předplatných.

I když klasické prostředky Azure může být zjištěna prostřednictvím grafu prostředků, je vysoce doporučeno vytvářet a používat prostředky Azure Resource Manager do budoucna.

Jak vytvářet dotazy pomocí Azure Resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak zobrazit předplatná Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Principy Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

## <a name="62-maintain-asset-metadata"></a>6.2: Udržování metadat datových zdrojů

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 6.2 | 1,5 | Zákazník |

Použijte značky pro prostředky Azure, které poskytují metadata k jejich logickému uspořádání do taxonomie.

Jak vytvořit a používat značky:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="63-delete-unauthorized-azure-resources"></a>6.3: Odstranění neautorizovaných prostředků Azure

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 6.3 | 1.6 | Zákazník |

V případě potřeby použijte značkování, skupiny pro správu a samostatná předplatná k uspořádání a sledování datových zdrojů. Pravidelně odsouhlasete zásoby a zajistěte, aby byly z předplatného včas odstraněny neoprávněné prostředky.

Jak vytvořit další předplatná Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak vytvořit skupiny pro správu:

https://docs.microsoft.com/azure/governance/management-groups/create

Jak vytvořit a používat značky:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Udržovat soupis schválených prostředků Azure a softwarových titulů

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 6.4 | 2.1 | Zákazník |

Definujte schválené prostředky Azure a schválený software pro výpočetní prostředky.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorování neschválených prostředků Azure

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 6.5 | 2.3, 2.4 | Zákazník |

Pomocí zásad Azure můžete omezit typ prostředků, které se dají vytvořit ve vašich předplatných.

Pomocí Azure Resource Graph můžete dotazovat/zjišťovat prostředky v rámci jejich předplatného. &nbsp;Ujistěte se, že jsou schváleny všechny prostředky Azure v prostředí.

Jak nakonfigurovat a spravovat zásady Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak vytvářet dotazy pomocí Azure Graphu:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Sledování neschválených softwarových aplikací v rámci výpočetních prostředků

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 6.6 | 2.3/2.4 | Zákazník |

Pomocí inventáře virtuálních počítačů Azure můžete automatizovat shromažďování informací o veškerém softwaru ve virtuálních počítačích. Název softwaru, verze, vydavatel a čas aktualizace jsou dostupné na webu Azure Portal. Chcete-li získat přístup k instalaci data a další informace, povolte diagnostiku na úrovni hosta a převést protokoly událostí systému Windows do pracovního prostoru Log Analytics.

Jak povolit inventář virtuálních strojů Azure:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Odebrání neschválených prostředků Azure a softwarových aplikací

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 6.7 | 2.5 | Zákazník |

Pomocí nástroje Azure Security Center monitorování integrity souborů (sledování změn) a inventářvirtuálního počítače k identifikaci veškerého softwaru nainstalovaného ve virtuálních počítačích. Můžete implementovat vlastní proces pro odstranění neoprávněného softwaru. K identifikaci neschváleného softwaru můžete také použít řešení třetí strany.

Jak používat monitorování integrity souborů:

https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring

Principy sledování změn Azure:

https://docs.microsoft.com/azure/automation/change-tracking

Jak povolit inventář virtuálních strojů Azure:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="68-use-only-approved-applications"></a>6.8: Používejte pouze schválené aplikace

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 6.8 | 2,6 | Zákazník |

Pomocí adaptivních aplikačních ovládacích prvků Centra zabezpečení Azure zajistíte, že se spustí jenom autorizovaný software a všemu neoprávněnému softwaru bude zablokováno spuštění ve virtuálních počítačích Azure.

Jak používat adaptivní aplikační ovládací prvky Centra zabezpečení Azure:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="69-use-only-approved-azure-services"></a>6.9: Používejte jenom schválené služby Azure

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 6.9 | 2,6 | Zákazník |

Pomocí zásad Azure můžete omezit služby, které můžete zřídit ve vašem prostředí.

Jak nakonfigurovat a spravovat zásady Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odepřít konkrétní typ prostředku pomocí zásad Azure:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

## <a name="610-implement-approved-application-list"></a>6.10: Implementace schváleného seznamu žádostí

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 6.1 | 2.7 | Zákazník |

Pomocí adaptivních aplikačních ovládacích prvků Centra zabezpečení Azure určete, na které typy souborů se pravidlo může nebo nemusí vztahovat.

Implementujte řešení třetí strany, pokud to nesplňuje požadavek.

Jak používat adaptivní aplikační ovládací prvky Centra zabezpečení Azure:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6.11: Omezte možnost uživatelů komunikovat se Správcem prostředků Azure pomocí skriptů

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 6.11 | 2,8 | Zákazník |

Pomocí podmíněného přístupu Azure omezte možnost uživatelů &quot;pracovat&quot; se &quot;Správcem&quot; prostředků Azure konfigurací blokového přístupu pro aplikaci Microsoft Azure Management.

Jak nakonfigurovat podmíněný přístup pro blokování přístupu ke Správci prostředků Azure:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Omezení možnosti uživatelů spouštět skripty v rámci výpočetních prostředků

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 6.12 | 2,8 | Zákazník |

Pomocí konfigurace specifické pro operační systém nebo prostředky třetích stran omezit schopnost uživatelů spouštět skripty v rámci výpočetních prostředků Azure.

Například jak řídit spuštění skriptu prostředí PowerShell v prostředí ch od Windows:

https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fyzicky nebo logicky oddělit vysoce rizikové aplikace

| Azure ID | ID CiS | Odpovědnost |
|--|--|--|
| 6.13 | 2.9 | Zákazník |

Software, který je vyžadován pro obchodní operace, ale může být pro organizaci vyšší riziko, by měl být izolován v rámci vlastního virtuálního počítače nebo virtuální sítě a dostatečně zabezpečen pomocí brány Azure Firewall nebo skupiny zabezpečení sítě.

Jak vytvořit virtuální síť:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Jak vytvořit skupinu zabezpečení zabezpečení s konfigurací zabezpečení:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="next-steps"></a>Další kroky

Podívejte se na další ovládací prvek zabezpečení: [Zabezpečená konfigurace](security-control-secure-configuration.md)
