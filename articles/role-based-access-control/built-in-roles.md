---
title: Předdefinované role pro řízení přístupu na základě role (RBAC) v Azure | Microsoft Docs
description: Popisuje předdefinované role pro řízení přístupu na základě role (RBAC) v Azure. Obsahuje seznam akcí a notActions.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/18/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: it-pro
ms.openlocfilehash: 365959a588dc48e7991efea239ba823c3ca65e7a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640536"
---
# <a name="built-in-roles-for-azure-role-based-access-control"></a>Integrované role pro řízení přístupu na základě role Azure
[Řízení přístupu na základě role (RBAC)](overview.md) má několik definic předdefinovaná role, které můžete přiřadit uživatele, skupiny a objekty služby. Přiřazení role představují způsob, jak můžete řídit přístup k prostředkům v Azure. Pokud předdefinované role nesplňují specifické požadavky vaší organizace, můžete vytvořit vlastní [vlastní role](custom-roles.md).

Předdefinované role se vždycky vyvíjejí. Chcete-li získat nejnovější definice rolí, použijte [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) nebo [seznamu definice role az](/cli/azure/role/definition#az-role-definition-list).

## <a name="built-in-role-descriptions"></a>Popisy předdefinovaná role
Následující tabulka obsahuje stručný popis předdefinovaných rolí. Klikněte na název role zobrazíte seznam `actions`, `notActions`, `dataActions`, a `notDataActions` pro každou roli.


| Předdefinovaná role | Popis |
| --- | --- |
| [Vlastník](#owner) | Umožňuje správu všech položek včetně přístupu k prostředkům. |
| [Přispěvatel](#contributor) | Umožňuje správu všech položek kromě přístupu k prostředkům. |
| [Čtecí modul](#reader) | Umožňuje zobrazit všechny položky, ale ne provádět změny. |
| [AcrImageSigner](#acrimagesigner) | podepisující osoba image ACR |
| [AcrQuarantineReader](#acrquarantinereader) | čtenář dat karantény ACR |
| [AcrQuarantineWriter](#acrquarantinewriter) | zapisovatel dat karantény ACR |
| [Rozhraní API správy služby přispěvatele](#api-management-service-contributor) | Může spravovat službu a rozhraní API |
| [Role operátora služby API Management](#api-management-service-operator-role) | Může spravovat službu, ale ne rozhraní API. |
| [Role čtenáře služby API Management](#api-management-service-reader-role) | Přístup ke službě a rozhraním API jen ke čtení |
| [Application Insights součást Přispěvatel](#application-insights-component-contributor) | Může spravovat součásti Application Insights. |
| [Application Insights snímku ladicí program](#application-insights-snapshot-debugger) | Uděluje oprávnění uživatele používat funkce Application Insights Snapshot Debuggeru. |
| [Operátor automatizace úloh](#automation-job-operator) | Vytváření a správa úloh pomocí runbooků služby Automation |
| [Operátor automatizace](#automation-operator) | Operátoři Automation můžou spouštět, zastavovat, pozastavovat a obnovovat úlohy. |
| [Operátor automatizace sady Runbook](#automation-runbook-operator) | Čtení vlastností runbooků – aby se daly vytvářet úlohy daného runbooku |
| [Vlastník registrace Azure zásobníku](#azure-stack-registration-owner) | Umožňuje spravovat registrace Azure Stack. |
| [Zálohování přispěvatele](#backup-contributor) | Umožňuje spravovat službu zálohování, ale neumožňuje vytvářet trezory a udělovat přístup ostatním uživatelům. |
| [Operátor zálohování](#backup-operator) | Umožňuje spravovat služby zálohování s výjimkou odebírání záloh, vytváření trezorů a udělování přístupu jiným uživatelům. |
| [Zálohování čtečky](#backup-reader) | Může zobrazovat služby zálohování, ale nemůže provádět změny. |
| [Čtečka fakturace](#billing-reader) | Povolí přístup pro čtení fakturačních údajů. |
| [BizTalk přispěvatele](#biztalk-contributor) | Umožňuje správu služeb BizTalk, ale ne přístup k nim. |
| [Přispěvatel koncový bod CDN](#cdn-endpoint-contributor) | Může spravovat koncové body CDN, ale nemůže udělovat přístup jiným uživatelům. |
| [Čtečka koncový bod CDN](#cdn-endpoint-reader) | Může zobrazovat koncové body CDN, ale nemůže je měnit. |
| [Přispěvatel profil CDN](#cdn-profile-contributor) | Může spravovat profily CDN a jejich koncové body, ale nemůže udělovat přístup jiným uživatelům. |
| [Čtečka profil CDN](#cdn-profile-reader) | Může zobrazovat profily CDN a jejich koncové body, ale nemůže je měnit. |
| [Přispěvatel klasických sítí](#classic-network-contributor) | Umožňuje správu klasických sítí, ale ne přístup k nim. |
| [Přispěvatel účet úložiště Classic](#classic-storage-account-contributor) | Umožňuje správu klasických účtů úložiště, ale ne přístup k nim. |
| [Role služby operátor klíče účtu úložiště Classic](#classic-storage-account-key-operator-service-role) | Operátoři klíčů klasických účtů úložiště můžou vypisovat a znovu generovat klíče pro klasické účty úložiště. |
| [Přispěvatel Classic virtuálních počítačů](#classic-virtual-machine-contributor) | Umožňuje správu klasických virtuálních počítačů, ale ne přístup k nim ani k virtuální síti nebo účtu úložiště, ke kterým jsou připojené. |
| [Databáze MySQL cleardb – DB přispěvatele](#cleardb-mysql-db-contributor) | Umožňuje správu databází ClearDB MySQL, ale ne přístup k nim. |
| [Role čtenáře účet cosmos DB](#cosmos-db-account-reader-role) | Můžete číst data účtu Azure Cosmos DB. V tématu [Přispěvatel účet DocumentDB](#documentdb-account-contributor) pro správu účtů Azure Cosmos DB. |
| [Přispěvatel objekt pro vytváření dat](#data-factory-contributor) | Umožňuje vytvářet a spravovat datové továrny i podřízené prostředky v nich. |
| [Data Lake Analytics vývojáře](#data-lake-analytics-developer) | Umožňuje odesílat, monitorovat a spravovat vlastní úlohy, ale neumožňuje vytvářet ani odstraňovat účty Data Lake Analytics. |
| [Purger dat](#data-purger) | Můžete vyprázdnit analytická data |
| [Uživatel DevTest Labs](#devtest-labs-user) | Umožňuje spouštět, restartovat a vypínat virtuální počítače v Azure DevTest Labs a připojovat se k nim. |
| [Přispěvatel zóny DNS](#dns-zone-contributor) | Umožňuje spravovat zóny DNS a sady záznamů v Azure DNS, ale neumožňuje řídit, kdo k nim má přístup. |
| [Přispěvatel účtu DocumentDB](#documentdb-account-contributor) | Ke správě účtů Azure Cosmos DB. Azure Cosmos DB je dříve označované jako DocumentDB. |
| [Přispěvatel účet inteligentních systémů](#intelligent-systems-account-contributor) | Umožňuje správu účtů inteligentních systémů, ale ne přístup k nim. |
| [Přispěvatel trezoru klíčů](#key-vault-contributor) | Umožňuje spravovat trezory klíčů, ale ne přistupovat k nim. |
| [Tvůrce testovacího prostředí](#lab-creator) | Umožňuje vytvářet, spravovat a odstraňovat spravovaná testovací prostředí v účtech Azure Lab. |
| [Přispěvatel analýzy protokolů](#log-analytics-contributor) | Log Analytics Přispěvatel můžete načíst všechna data monitorování a upravit nastavení monitorování. Úprava nastavení monitorování, zahrnuje přidání rozšíření virtuálního počítače pro virtuální počítače; čtení klíče účtu úložiště, abyste mohli nakonfigurovat shromažďování protokolů ze služby Azure Storage; vytváření a konfiguraci účtů Automation; Přidání řešení; a konfigurace Azure diagnostics pro všechny prostředky Azure. |
| [Log Analytics Reader](#log-analytics-reader) | Čtenář Log Analytics si může zobrazit a vyhledat všechna data monitorování a jeho nastavení. Může si zobrazit konfiguraci diagnostiky Azure na všech prostředcích Azure. |
| [Přispěvatel aplikace logiky](#logic-app-contributor) | Umožňuje správu aplikace logiky, ale ne přístup k ní. |
| [Operátor aplikace logiky](#logic-app-operator) | Umožňuje číst, povolovat a zakazovat aplikaci logiky. |
| [Spravované Identity přispěvatele](#managed-identity-contributor) | Vytváření, čtení, aktualizace a odstraňování identity přiřazené uživateli |
| [Operátor spravované Identity](#managed-identity-operator) | Čtení a přiřazování identity přiřazené uživateli |
| [Monitorování přispěvatele](#monitoring-contributor) | Můžete načíst všechna data monitorování a upravit nastavení monitorování. Viz také [začít pracovat s rolí, oprávnění a zabezpečení pomocí Azure monitorování](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Monitorování čtečky](#monitoring-reader) | Může číst veškerá monitorování data (metriky, protokoly, atd.). Viz také [začít pracovat s rolí, oprávnění a zabezpečení pomocí Azure monitorování](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Přispěvatel sítě](#network-contributor) | Umožňuje správu sítí, ale ne přístup k nim. |
| [Nový přispěvatel účet New Relic APM](#new-relic-apm-account-contributor) | Umožňuje správu účtů a aplikací New Relic Application Performance Management, ale ne přístup k nim. |
| [Čtečka a přístup k datům](#reader-and-data-access) | Umožňuje zobrazit vše, ale nebude umožňují odstranit nebo vytvořit účet úložiště nebo obsaženého zdroje. Také umožní přístup pro čtení a zápis ke všem datům obsaženým v účtu úložiště prostřednictvím přístup k klíče účtu úložiště. |
| [Přispěvatel mezipaměti redis](#redis-cache-contributor) | Umožňuje správu mezipamětí Redis, ale ne přístup k nim. |
| [Prostředek zásad Přispěvatel (Preview)](#resource-policy-contributor-preview) | (Preview) Obnovení uživatelé z EA s oprávněními vytvářet a upravovat zásady prostředků, vytvářet lístky podpory a číst prostředky a hierarchii |
| [Přispěvatel kolekce úloh plánovače](#scheduler-job-collections-contributor) | Umožňuje správu kolekcí úloh Scheduleru, ale ne přístup k nim. |
| [Přispěvatel služby vyhledávání](#search-service-contributor) | Umožňuje správu služeb Search, ale ne přístup k nim. |
| [Správce zabezpečení](#security-admin) | V Centru zabezpečení pouze: můžete zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, upravit zásady zabezpečení, zobrazení výstrah a doporučení, zavření výstrahy a doporučení |
| [Správce zabezpečení (zastaralé)](#security-manager-legacy) | Toto je starší verze role. Místo toho použijte Správce zabezpečení. |
| [Čtečka zabezpečení](#security-reader) | V Centru zabezpečení pouze: můžete zobrazit doporučení a výstrahy, zobrazení zásady zabezpečení, zobrazit stavy zabezpečení, ale nelze provádět změny |
| [Přispěvatel obnovení lokality](#site-recovery-contributor) | Umožňuje spravovat službu Site Recovery, ale ne vytvářet trezory a přiřazovat role. |
| [Operátor obnovení lokality](#site-recovery-operator) | Umožňuje provádět převzetí služeb při selhání a navrácení služeb po obnovení, ale žádné jiné operace správy služby Site Recovery. |
| [Čtečka obnovení lokality](#site-recovery-reader) | Umožňuje zobrazovat stav služby Site Recovery, ale ne provádět jiné operace správy. |
| [Přispěvatel databází SQL](#sql-db-contributor) | Umožňuje spravovat databáze SQL, ale ne přístup k nim. Navíc se nedají spravovat jejich zásady zabezpečení nebo jejich nadřazené servery SQL. |
| [Správce zabezpečení SQL](#sql-security-manager) | Umožňuje vám spravovat zásady vztahující se k zabezpečení serverů a databází SQL, ale ne přístup k nim. |
| [Přispěvatel serveru SQL](#sql-server-contributor) | Umožňuje vám spravovat servery a databáze SQL, ale ne přístup k nim ani jejich zásady vztahující se k zabezpečení. |
| [Přispěvatel účtů úložiště](#storage-account-contributor) | Umožňuje správu účtů úložiště, ale ne přístup k nim. |
| [Role služby operátor klíče účtu úložiště](#storage-account-key-operator-service-role) | Operátoři klíčů účtů úložiště můžou vypisovat a znovu generovat klíče pro účty úložiště. |
| [Úložiště objektů Blob dat přispěvatele (Preview)](#storage-blob-data-contributor-preview) | Umožňuje číst, zapisovat a odstraňovat kontejnery a data objektů blob služby Azure Storage. |
| [Čtecí modul dat objektů Blob Storage (Preview)](#storage-blob-data-reader-preview) | Umožňuje číst kontejnery a data objektů blob služby Azure Storage. |
| [Úložiště fronty dat přispěvatele (Preview)](#storage-queue-data-contributor-preview) | Umožňuje číst, zapisovat a odstraňovat fronty služby Azure Storage a jejich zprávy. |
| [Čtecí modul dat fronty úložiště (Preview)](#storage-queue-data-reader-preview) | Umožňuje číst fronty služby Azure Storage a jejich zprávy. |
| [Přispěvatel žádosti o podporu](#support-request-contributor) | Umožňuje vytvořit a spravovat žádosti o podporu. |
| [Přispěvatel Traffic Manageru](#traffic-manager-contributor) | Umožňuje spravovat profily Traffic Manageru, ale neumožňuje řídit, kdo k nim má přístup. |
| [Správce přístupu uživatelů](#user-access-administrator) | Umožňuje správu přístupu uživatelů k prostředkům Azure. |
| [Přihlášení správce virtuálních počítačů](#virtual-machine-administrator-login) | –  Uživatelé s touto rolí se budou moct přihlašovat k virtuálnímu počítači s oprávněními správce Windows nebo uživatele root v Linuxu. |
| [Přispěvatel virtuálních počítačů](#virtual-machine-contributor) | Umožňuje spravovat virtuální počítače, ale ne přístup k nim a ne virtuální sítě nebo účet úložiště, který jste připojení k. |
| [Přihlášení uživatele virtuálního počítače](#virtual-machine-user-login) | Uživatelé s touto rolí se budou moct přihlašovat k virtuálnímu počítači jako běžní uživatelé. |
| [Plán přispěvatelů webu](#web-plan-contributor) | Umožňuje správu webových plánů pro weby, ale ne přístup k nim. |
| [Přispěvatel webu](#website-contributor) | Umožňuje správu webů (ne webových plánů), ale ne přístup k nim. |


## <a name="owner"></a>Vlastník
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu všech položek včetně přístupu k prostředkům. |
> | **ID** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Akce** |  |
> | * | Vytvořit a spravovat prostředky všech typů |

## <a name="contributor"></a>Přispěvatel
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu všech položek kromě přístupu k prostředkům. |
> | **ID** | b24988ac-6180-42A0-ab88-20f7382dd24c |
> | **Akce** |  |
> | * | Vytvořit a spravovat prostředky všech typů |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | Nelze odstranit role a přiřazení rolí |
> | Microsoft.Authorization/*/Write | Nelze vytvořit role a přiřazení rolí |
> | Microsoft.Authorization/elevateAccess/Action | Udělí volajícímu přístup Správce uživatelských přístupů v oboru tenanta. |

## <a name="reader"></a>Čtenář
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje zobrazit všechny položky, ale ne provádět změny. |
> | **ID** | acdd72a7-3385-48EF-bd42-f606fba81ae7 |
> | **Akce** |  |
> | * / Číst | Přečtěte si prostředky všech typů, s výjimkou tajných klíčů. |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | podepisující osoba image ACR |
> | **ID** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Akce** |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |
> | Microsoft.ContainerRegistry/registries/*/write |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | čtenář dat karantény ACR |
> | **ID** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Akce** |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | zapisovatel dat karantény ACR |
> | **ID** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Akce** |  |
> | Microsoft.ContainerRegistry/registries/*/write |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |

## <a name="api-management-service-contributor"></a>Přispěvatel služby API Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může spravovat službu a rozhraní API |
> | **ID** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Akce** |  |
> | Microsoft.ApiManagement/service/* | Vytvářet a spravovat služby API Management |
> | Microsoft.Authorization/*/read | Autorizace pro čtení |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="api-management-service-operator-role"></a>Role Operátor služby API Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může spravovat službu, ale ne rozhraní API. |
> | **ID** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Akce** |  |
> | Microsoft.ApiManagement/service/*/read | Instance služby API Management pro čtení |
> | Microsoft.ApiManagement/service/backup/action | Zálohování službu správy rozhraní API a zadaného kontejneru. v uživatel zadaný účet úložiště |
> | Microsoft.ApiManagement/service/delete | Odstranění instance služby API Management |
> | Microsoft.ApiManagement/service/managedeployments/action | Změnit SKU nebo jednotky, přidat nebo odebrat místní nasazení služby API Management |
> | Microsoft.ApiManagement/service/read | Číst metadata pro instanci služby API Management |
> | Microsoft.ApiManagement/service/restore/action | Obnovení ze zadaného kontejneru v uživatel zadaný účet úložiště služby API Management |
> | Microsoft.ApiManagement/service/updatecertificate/action | Nahrajte certifikát SSL pro službu správy rozhraní API |
> | Microsoft.ApiManagement/service/updatehostname/action | Instalační program, aktualizovat nebo odebrat vlastní názvy domén pro služby API Management |
> | Microsoft.ApiManagement/service/write | Vytvoření nové instance služby API Management |
> | Microsoft.Authorization/*/read | Autorizace pro čtení |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Získat seznam klíčů uživatele |

## <a name="api-management-service-reader-role"></a>Role Čtenář služby API Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Přístup ke službě a rozhraním API jen ke čtení |
> | **ID** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Akce** |  |
> | Microsoft.ApiManagement/service/*/read | Instance služby API Management pro čtení |
> | Microsoft.ApiManagement/service/read | Číst metadata pro instanci služby API Management |
> | Microsoft.Authorization/*/read | Autorizace pro čtení |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Získat seznam klíčů uživatele |

## <a name="application-insights-component-contributor"></a>Přispěvatel součástí Application Insights
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může spravovat součásti Application Insights. |
> | **ID** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.Insights/components/* | Vytvoření a správa přehled komponenty |
> | Microsoft.Insights/webtests/* | Vytvoření a Správa webové testy |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Uděluje oprávnění uživatele používat funkce Application Insights Snapshot Debuggeru. |
> | **ID** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="automation-job-operator"></a>Operátor úloh Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Vytváření a správa úloh pomocí runbooků služby Automation |
> | **ID** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Automation/automationAccounts/jobs/read | Získá úlohu automatizace Azure |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Obnoví úlohu automatizace Azure |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Zastaví úlohu Azure Automation |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Čte prostředky Hybrid Runbook Worker |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Získá stream úloh Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Pozastaví úlohu Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/write | Vytvoří úlohu Azure Automation |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="automation-operator"></a>Operátor služby Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Operátoři Automation můžou spouštět, zastavovat, pozastavovat a obnovovat úlohy. |
> | **ID** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Čte prostředky Hybrid Runbook Worker |
> | Microsoft.Automation/automationAccounts/jobs/read | Získá úlohu automatizace Azure |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Obnoví úlohu automatizace Azure |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Zastaví úlohu Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Získá stream úloh Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Pozastaví úlohu Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/write | Vytvoří úlohu Azure Automation |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Získá plán úloh Azure Automation. |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Vytvoří plán úloh Azure Automation. |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Získá prostoru propojí s účtem automation. |
> | Microsoft.Automation/automationAccounts/read | Získá účet automatizace Azure |
> | Microsoft.Automation/automationAccounts/runbooks/read | Načte runbook služby automatizace Azure |
> | Microsoft.Automation/automationAccounts/schedules/read | Získá prostředek plánování Azure Automation. |
> | Microsoft.Automation/automationAccounts/schedules/write | Vytvoří nebo aktualizuje prostředek plánování Azure Automation. |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Načte výstup úlohy |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="automation-runbook-operator"></a>Operátor runbooků Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Čtení vlastností runbooků – aby se daly vytvářet úlohy daného runbooku |
> | **ID** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Automation/automationAccounts/runbooks/read | Načte runbook služby automatizace Azure |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="azure-stack-registration-owner"></a>Vlastník registrace Azure Stack
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat registrace Azure Stack. |
> | **ID** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Akce** |  |
> | Microsoft.AzureStack/registrations/products/listDetails/action | Načte Rozšířené podrobnosti o produkt Azure Marketplace zásobníku |
> | Microsoft.AzureStack/registrations/products/read | Získá vlastnosti o produkt Azure Marketplace zásobníku |
> | Microsoft.AzureStack/registrations/read | Získá vlastnosti zápisu Azure zásobníku |

## <a name="backup-contributor"></a>Přispěvatel zálohování
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat službu zálohování, ale neumožňuje vytvářet trezory a udělovat přístup ostatním uživatelům. |
> | **ID** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Spravovat výsledky operace na správu záloh |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Vytvářet a spravovat zálohy kontejnery uvnitř zálohování Fabric trezor služeb zotavení |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Vytvářet a spravovat úlohy zálohování |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Export úloh |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Vytvářet a spravovat metadata související se správou zálohování |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Vytvořit a spravovat výsledky operací zálohování správy |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Vytvářet a spravovat zásady zálohování |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Vytváření a Správa položek, které lze zálohovat |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Vytváření a správa zálohovaných položek |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Vytvoření a Správa kontejnerů, která uchovává položky zálohování |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Vytváření a správě certifikátů související s zálohování v trezoru služeb zotavení |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Vytváření a správě rozšířené informace související s trezoru |
> | Microsoft.RecoveryServices/Vaults/read | Operace získání trezoru získá objekt, který reprezentuje prostředků Azure typu 'trezoru. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/* | Spravovat operaci zjišťování pro načítání nově vytvořený kontejnery |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Vytvářet a spravovat registrovaná identit |
> | Microsoft.RecoveryServices/Vaults/usages/* | Vytvářet a spravovat využití trezor služeb zotavení |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Vrátí souhrny pro chráněné položky a chráněné servery pro služeb zotavení. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp je interní operace, kterou používá služba |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Získá výstrahy pro trezor služeb zotavení. |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Vrátí výsledek operace úlohy exportu. |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="backup-operator"></a>Operátor zálohování
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat služby zálohování s výjimkou odebírání záloh, vytváření trezorů a udělování přístupu jiným uživatelům. |
> | **ID** | 00c29273-979b-4161-815C-10b084fb9324 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Vrátí stav operace |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Načte výsledky operace provedené na kontejneru ochrany. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Provede zálohování chráněné položky. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Načte výsledky operace provedené na chráněných položkách. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Načte stav operace provedené na chráněných položkách. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Vrátí podrobnosti o objektu chráněné položky |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Načíst body obnovení pro chráněné položky |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Obnoví body obnovení pro chráněné položky |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Vytvoření zálohy chráněné položky |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Vrátí všechny registrované kontejnery |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Vytvářet a spravovat úlohy zálohování |
> | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Zrušení úlohy |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Vrátí výsledek operace úlohy. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Vrátí všechny objekty úlohy |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Export úloh |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Vrátí metadata správy zálohování trezoru Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Vytvořit a spravovat výsledky operací zálohování správy |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Načte výsledky operace zásad. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Vrátí všechny zásady ochrany |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Vytváření a Správa položek, které lze zálohovat |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Vrátí seznam chránitelných položek. |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Vrátí seznam všech chráněných položek. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Vrátí všechny kontejnery, které patří k předplatnému |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Vrátí souhrny pro chráněné položky a chráněné servery pro služeb zotavení. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Microsoft.RecoveryServices/Vaults/read | Operace získání trezoru získá objekt, který reprezentuje prostředků Azure typu 'trezoru. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/* | Spravovat operaci zjišťování pro načítání nově vytvořený kontejnery |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Výsledky operace Get, lze operace získat stav operace a výsledek asynchronně odeslaná operaci |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Získat kontejnerů, které můžete použít operaci získat kontejnery zaregistrovat pro prostředek. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Operaci zaregistrovat kontejneru služby lze použít k registraci kontejner službou obnovení. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti využití trezoru Recovery Services. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Zřízení rychlých položky obnovení pro chráněné položky |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Odvolat rychlých položky obnovení pro chráněné položky |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp je interní operace, kterou používá služba |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Získá výstrahy pro trezor služeb zotavení. |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Vrátí výsledek operace úlohy exportu. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationStatus/read |  |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Operace aktualizace prostředek certifikátu aktualizuje prostředek nebo trezoru certifikát přihlašovacích údajů. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="backup-reader"></a>Čtenář záloh
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může zobrazovat služby zálohování, ale nemůže provádět změny. |
> | **ID** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Vrátí stav operace |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Načte výsledky operace provedené na kontejneru ochrany. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Načte výsledky operace provedené na chráněných položkách. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Načte stav operace provedené na chráněných položkách. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Vrátí podrobnosti o objektu chráněné položky |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Vrátí všechny registrované kontejnery |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Vrátí výsledek operace úlohy. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Vrátí všechny objekty úlohy |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Export úloh |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Vrátí metadata správy zálohování trezoru Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Vrátí výsledek operace zálohování trezoru Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Načte výsledky operace zásad. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Vrátí všechny zásady ochrany |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Vrátí seznam všech chráněných položek. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Vrátí všechny kontejnery, které patří k předplatnému |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Vrátí souhrny pro chráněné položky a chráněné servery pro služeb zotavení. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Microsoft.RecoveryServices/Vaults/read | Operace získání trezoru získá objekt, který reprezentuje prostředků Azure typu 'trezoru. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Výsledky operace Get, lze operace získat stav operace a výsledek asynchronně odeslaná operaci |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Získat kontejnerů, které můžete použít operaci získat kontejnery zaregistrovat pro prostředek. |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp je interní operace, kterou používá služba |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Získá výstrahy pro trezor služeb zotavení. |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Načíst body obnovení pro chráněné položky |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Vrátí výsledek operace úlohy exportu. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti využití trezoru Recovery Services. |

## <a name="billing-reader"></a>Čtenář fakturace
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Povolí přístup pro čtení fakturačních údajů. |
> | **ID** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Billing/*/read | Číst informace o fakturaci |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Management/managementGroups/read | Zobrazí seznam skupin pro správu pro ověřené uživatele. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="biztalk-contributor"></a>Přispěvatel BizTalk
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu služeb BizTalk, ale ne přístup k nim. |
> | **ID** | 5e3c6656-6cfa-4708-81FE-0de47ac73342 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.BizTalkServices/BizTalk/* | Vytvářet a spravovat služby BizTalk services |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="cdn-endpoint-contributor"></a>Přispěvatel koncového bodu CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může spravovat koncové body CDN, ale nemůže udělovat přístup jiným uživatelům. |
> | **ID** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="cdn-endpoint-reader"></a>Čtenář koncového bodu CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může zobrazovat koncové body CDN, ale nemůže je měnit. |
> | **ID** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="cdn-profile-contributor"></a>Přispěvatel profilu CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může spravovat profily CDN a jejich koncové body, ale nemůže udělovat přístup jiným uživatelům. |
> | **ID** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="cdn-profile-reader"></a>Čtenář profilu CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může zobrazovat profily CDN a jejich koncové body, ale nemůže je měnit. |
> | **ID** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="classic-network-contributor"></a>Přispěvatel klasických sítí
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu klasických sítí, ale ne přístup k nim. |
> | **ID** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Autorizace pro čtení |
> | Microsoft.ClassicNetwork/* | Vytvoření a správa klasické sítě |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="classic-storage-account-contributor"></a>Přispěvatel klasických účtů úložiště
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu klasických účtů úložiště, ale ne přístup k nim. |
> | **ID** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Autorizace pro čtení |
> | Microsoft.ClassicStorage/storageAccounts/* | Vytváření a správě účtů úložiště |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="classic-storage-account-key-operator-service-role"></a>Role služby Operátor klíčů klasických účtů úložiště
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Operátoři klíčů klasických účtů úložiště můžou vypisovat a znovu generovat klíče pro klasické účty úložiště. |
> | **ID** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Akce** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Umožňuje vypsat seznam přístupových klíčů pro účty úložiště. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Umožňuje znovu vytvořit existující přístupové klíče pro účet úložiště. |

## <a name="classic-virtual-machine-contributor"></a>Přispěvatel klasických virtuálních počítačů
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu klasických virtuálních počítačů, ale ne přístup k nim ani k virtuální síti nebo účtu úložiště, ke kterým jsou připojené. |
> | **ID** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Autorizace pro čtení |
> | Microsoft.ClassicCompute/domainNames/* | Vytvoření a správa klasické výpočetní názvů domén |
> | Microsoft.ClassicCompute/virtualMachines/* | Vytvářet a spravovat virtuální počítače |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Odkaz na rezervovanou IP adresu |
> | Microsoft.ClassicNetwork/reservedIps/read | Umožňuje načíst vyhrazené IP adresy. |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Připojí se k virtuální síti. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Umožňuje načíst virtuální síť. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | Vrátí disk účtu úložiště. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | Vrátí image účtu storage. |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Umožňuje vypsat seznam přístupových klíčů pro účty úložiště. |
> | Microsoft.ClassicStorage/storageAccounts/read | Umožňuje načíst účet úložiště se zadaným účtem. |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="cleardb-mysql-db-contributor"></a>Přispěvatel databází ClearDB MySQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu databází ClearDB MySQL, ale ne přístup k nim. |
> | **ID** | 9106cda0-8a86-4E81-b686-29a22c54effe |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
> | successbricks.cleardb/Databases/* | Vytvářet a spravovat databáze MySQL cleardb – |

## <a name="cosmos-db-account-reader-role"></a>Role čtenáře účtu Cosmos DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Můžete číst data účtu Azure Cosmos DB. V tématu [Přispěvatel účet DocumentDB](#documentdb-account-contributor) pro správu účtů Azure Cosmos DB. |
> | **ID** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí, můžete oprávnění ke čtení zadané pro každého uživatele |
> | Microsoft.DocumentDB/*/read | Číst všechny kolekce |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Čte databázi, klíče účtu jen pro čtení. |
> | Microsoft.Insights/MetricDefinitions/read | Číst definice metrik |
> | Microsoft.Insights/Metrics/read | Čtení metrik |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="data-factory-contributor"></a>Přispěvatel Data Factory
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje vytvářet a spravovat datové továrny i podřízené prostředky v nich. |
> | **ID** | 673868aa-7521-48A0-acc6-0f60742d39f5 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení role a role přiřazení |
> | Microsoft.DataFactory/dataFactories/* | Vytvořit a spravovat datové továrny a podřízené prostředky v nich. |
> | Microsoft.DataFactory/factories/* | Vytvořit a spravovat datové továrny a podřízené prostředky v nich. |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="data-lake-analytics-developer"></a>Vývojář Data Lake Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje odesílat, monitorovat a spravovat vlastní úlohy, ale neumožňuje vytvářet ani odstraňovat účty Data Lake Analytics. |
> | **ID** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | Odstranění účtu DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Udělení oprávnění k zrušení úlohy, odeslané jinými uživateli. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Vytvořit nebo aktualizovat účet DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Vytvořit nebo aktualizovat účet propojené DataLakeStore DataLakeAnalytics účtu. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Zrušit propojení účtu DataLakeStore z DataLakeAnalytics účtu. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Vytvořit nebo aktualizovat účet úložiště propojené DataLakeAnalytics účtu. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Zrušit propojení účtu úložiště z účtu DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Vytvořit nebo aktualizovat pravidlo brány firewall. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Odstraňte pravidlo brány firewall. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Vytvořit nebo aktualizovat zásadu výpočty. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Odstraníte zásadu výpočty. |

## <a name="data-purger"></a>Purger dat
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Můžete vyprázdnit analytická data |
> | **ID** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Akce** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action |  |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft.OperationalInsights/workspaces/purge/action | Odstranit zadaná data z pracovního prostoru |

## <a name="devtest-labs-user"></a>Uživatel služby DevTest Labs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spouštět, restartovat a vypínat virtuální počítače v Azure DevTest Labs a připojovat se k nim. |
> | **ID** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení role a role přiřazení |
> | Microsoft.Compute/availabilitySets/read | Umožňuje získat vlastnosti sady dostupnosti. |
> | Microsoft.Compute/virtualMachines/*/read | Číst vlastnosti virtuálního počítače (velikosti virtuálních počítačů, stav modulu runtime, rozšíření virtuálního počítače, atd.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Umožňuje vypnout virtuální počítač a uvolnit výpočetní prostředky. |
> | Microsoft.Compute/virtualMachines/read | Umožňuje získat vlastnosti virtuálního počítače. |
> | Microsoft.Compute/virtualMachines/restart/action | Umožňuje restartovat virtuální počítač. |
> | Microsoft.Compute/virtualMachines/start/action | Umožňuje spustit virtuální počítač. |
> | Microsoft.DevTestLab/*/read | Číst vlastnosti služby testovacího prostředí |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Vytvoření virtuálních počítačů v testovacím prostředí. |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Deklarace identity náhodných vymahatelných virtuálního počítače v testovacím prostředí. |
> | Microsoft.DevTestLab/labs/formulas/delete | Odstraňte vzorce. |
> | Microsoft.DevTestLab/labs/formulas/read | Přečtěte si vzorce. |
> | Microsoft.DevTestLab/labs/formulas/write | Přidat nebo upravit vzorce. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Vyhodnotí zásady testovacího prostředí. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Převzetí vlastnictví existujícího virtuálního počítače |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Spojí fond back-end adresy Vyrovnávání zatížení. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Spojí pravidlo příchozí nat nástroje pro vyrovnávání zatížení. |
> | Microsoft.Network/networkInterfaces/*/read | Při čtení vlastností rozhraní sítě (například všechny služby Vyrovnávání zatížení, které síťové rozhraní je součástí) |
> | Microsoft.Network/networkInterfaces/join/action | Virtuální počítač připojí k síťovému rozhraní |
> | Microsoft.Network/networkInterfaces/read | Získá definici rozhraní sítě.  |
> | Microsoft.Network/networkInterfaces/write | Vytvoří rozhraní sítě nebo aktualizuje existující rozhraní sítě.  |
> | Microsoft.Network/publicIPAddresses/*/read | Číst vlastnosti služby veřejnou IP adresu |
> | Microsoft.Network/publicIPAddresses/join/action | Spojí veřejnou ip adresu. |
> | Microsoft.Network/publicIPAddresses/read | Získá definici veřejné ip adresy. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Připojí virtuální sítě |
> | Microsoft.Resources/deployments/operations/read | Načte nebo vypíše operace nasazení. |
> | Microsoft.Resources/deployments/read | Načte nebo vypíše nasazení. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Umožňuje zobrazit seznam dostupných velikostí, na které je možné virtuální počítač aktualizovat. |

## <a name="dns-zone-contributor"></a>Přispěvatel zóny DNS
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat zóny DNS a sady záznamů v Azure DNS, ale neumožňuje řídit, kdo k nim má přístup. |
> | **ID** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.Network/dnsZones/* | Vytváření a Správa zón DNS a záznamů |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="documentdb-account-contributor"></a>Přispěvatel účtů DocumentDB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Ke správě účtů Azure Cosmos DB. Azure Cosmos DB je dříve označované jako DocumentDB. |
> | **ID** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení role a role přiřazení |
> | Microsoft.DocumentDb/databaseAccounts/* | Vytvořit a spravovat účty pro Azure Cosmos DB |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="intelligent-systems-account-contributor"></a>Přispěvatel účtů Inteligentních systémů
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu účtů inteligentních systémů, ale ne přístup k nim. |
> | **ID** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení role a role přiřazení |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.IntelligentSystems/accounts/* | Vytváření a správě účtů inteligentních systémů |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="key-vault-contributor"></a>Přispěvatel Key Vaultu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat trezory klíčů, ale ne přistupovat k nim. |
> | **ID** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Trvale odstraní trezor klíčů, který se nejdříve odstranil obnovitelně. |
> | Microsoft.KeyVault/hsmPools/* |  |

## <a name="lab-creator"></a>Autor testovacího prostředí
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje vytvářet, spravovat a odstraňovat spravovaná testovací prostředí v účtech Azure Lab. |
> | **ID** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Vytvoření testovacího prostředí v účtu testovacího prostředí. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="log-analytics-contributor"></a>Přispěvatel Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Log Analytics Přispěvatel můžete načíst všechna data monitorování a upravit nastavení monitorování. Úprava nastavení monitorování, zahrnuje přidání rozšíření virtuálního počítače pro virtuální počítače; čtení klíče účtu úložiště, abyste mohli nakonfigurovat shromažďování protokolů ze služby Azure Storage; vytváření a konfiguraci účtů Automation; Přidání řešení; a konfigurace Azure diagnostics pro všechny prostředky Azure. |
> | **ID** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Akce** |  |
> | * / Číst | Přečtěte si prostředky všech typů, s výjimkou tajných klíčů. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Umožňuje vypsat seznam přístupových klíčů pro účty úložiště. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.Insights/diagnosticSettings/* | Vytvoří, aktualizuje nebo načte nastavení diagnostiky pro Analysis serveru |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="log-analytics-reader"></a>Čtenář Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Čtenář Log Analytics si může zobrazit a vyhledat všechna data monitorování a jeho nastavení. Může si zobrazit konfiguraci diagnostiky Azure na všech prostředcích Azure. |
> | **ID** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Akce** |  |
> | * / Číst | Přečtěte si prostředky všech typů, s výjimkou tajných klíčů. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Vyhledávání pomocí nový modul. |
> | Microsoft.OperationalInsights/workspaces/search/action | Provede vyhledávací dotaz. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Načte se sdílené klíče pro pracovní prostor. Tyto klíče se používají k připojení agentů statistice provozu Microsoft do pracovního prostoru. |

## <a name="logic-app-contributor"></a>Přispěvatel aplikace logiky
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu aplikace logiky, ale ne přístup k ní. |
> | **ID** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Umožňuje vypsat seznam přístupových klíčů pro účty úložiště. |
> | Microsoft.ClassicStorage/storageAccounts/read | Umožňuje načíst účet úložiště se zadaným účtem. |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.Insights/diagnosticSettings/* | Vytvoří, aktualizuje nebo načte nastavení diagnostiky pro Analysis serveru |
> | Microsoft.Insights/logdefinitions/* | Tato oprávnění jsou nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivity prostřednictvím portálu. Seznam kategorií protokolu v protokolu aktivit. |
> | Microsoft.Insights/metricDefinitions/* | Číst definice metrik (seznamu dostupných typů metriky pro prostředek). |
> | Microsoft.Logic/* | Spravuje prostředky Logic Apps. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/operationresults/read | Načte výsledky operací s předplatným. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
> | Microsoft.Web/connectionGateways/* | Vytvořit a spravovat připojení brány. |
> | Microsoft.Web/connections/* | Vytvořit a spravovat připojení. |
> | Microsoft.Web/customApis/* | Vytváří a spravuje vlastní API. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Umožňuje získat vlastnosti na plán služby App Service |
> | Microsoft.Web/sites/functions/listSecrets/action | Funkce, webové aplikace seznamu tajných klíčů. |

## <a name="logic-app-operator"></a>Operátor aplikace logiky
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje číst, povolovat a zakazovat aplikaci logiky. |
> | **ID** | 515c2055-d9d4-4321-B1B9-bd0c9a0f79fe |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/*/read | Pravidla výstrah statistiky pro čtení |
> | Microsoft.Insights/diagnosticSettings/*/read | Získá nastavení diagnostiky pro Logic Apps |
> | Microsoft.Insights/metricDefinitions/*/read | Načte dostupné metriky pro Logic Apps. |
> | Microsoft.Logic/*/read | Čte prostředky Logic Apps. |
> | Microsoft.Logic/workflows/disable/action | Zakáže pracovní postup. |
> | Microsoft.Logic/workflows/enable/action | Povolí pracovní postup. |
> | Microsoft.Logic/workflows/validate/action | Ověří pracovní postup. |
> | Microsoft.Resources/deployments/operations/read | Načte nebo vypíše operace nasazení. |
> | Microsoft.Resources/subscriptions/operationresults/read | Načte výsledky operací s předplatným. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
> | Microsoft.Web/connectionGateways/*/read | Číst připojení brány. |
> | Microsoft.Web/connections/*/read | Číst připojení. |
> | Microsoft.Web/customApis/*/read | Vlastní rozhraní API pro čtení. |
> | Microsoft.Web/serverFarms/read | Umožňuje získat vlastnosti na plán služby App Service |

## <a name="managed-identity-contributor"></a>Přispěvatel spravovaných identit
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Vytváření, čtení, aktualizace a odstraňování identity přiřazené uživateli |
> | **ID** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Akce** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="managed-identity-operator"></a>Operátor spravovaných identit
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Čtení a přiřazování identity přiřazené uživateli |
> | **ID** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Akce** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="monitoring-contributor"></a>Přispěvatel monitorování
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Můžete načíst všechna data monitorování a upravit nastavení monitorování. Viz také [začít pracovat s rolí, oprávnění a zabezpečení pomocí Azure monitorování](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
> | **ID** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Akce** |  |
> | * / Číst | Přečtěte si prostředky všech typů, s výjimkou tajných klíčů. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/AlertRules/* | Pravidla výstrah pro čtení, zápisu a odstranění. |
> | Microsoft.Insights/components/* | Součásti Application Insights pro čtení, zápisu a odstranění. |
> | Microsoft.Insights/DiagnosticSettings/* | Nastavení diagnostiky pro čtení, zápisu a odstranění. |
> | Microsoft.Insights/eventtypes/* | Zobrazí seznam aktivity protokolu události (události management) v předplatném. Toto oprávnění se vztahuje na portálu i programový přístup k protokolu aktivit. |
> | Microsoft.Insights/LogDefinitions/* | Tato oprávnění jsou nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivity prostřednictvím portálu. Seznam kategorií protokolu v protokolu aktivit. |
> | Microsoft.Insights/MetricDefinitions/* | Číst definice metrik (seznamu dostupných typů metriky pro prostředek). |
> | Microsoft.Insights/Metrics/* | Číst metriky pro prostředek. |
> | Microsoft.Insights/Register/Action | Registrovat poskytovatele platformy Microsoft Insights |
> | Microsoft.Insights/webtests/* | Application Insights pro čtení, zápisu a odstranění webové testy. |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Balíčky řešení analýzy protokolů pro čtení, zápisu a odstranění. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Analýzy protokolů pro čtení, zápisu a odstraňování uložená hledání. |
> | Microsoft.OperationalInsights/workspaces/search/action | Provede vyhledávací dotaz. |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Načte se sdílené klíče pro pracovní prostor. Tyto klíče se používají k připojení agentů statistice provozu Microsoft do pracovního prostoru. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Konfigurace přehledy úložiště analýzy protokolů pro čtení, zápisu a odstranění. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
> | Microsoft.WorkloadMonitor/workloads/* |  |

## <a name="monitoring-reader"></a>Čtenář monitorování
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Může číst veškerá monitorování data (metriky, protokoly, atd.). Viz také [začít pracovat s rolí, oprávnění a zabezpečení pomocí Azure monitorování](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
> | **ID** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Akce** |  |
> | * / Číst | Přečtěte si prostředky všech typů, s výjimkou tajných klíčů. |
> | Microsoft.OperationalInsights/workspaces/search/action | Provede vyhledávací dotaz. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="network-contributor"></a>Přispěvatel sítě
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu sítí, ale ne přístup k nim. |
> | **ID** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení role a role přiřazení |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.Network/* | Vytvoření a Správa sítě |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="new-relic-apm-account-contributor"></a>Přispěvatel účtů New Relic APM
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu účtů a aplikací New Relic Application Performance Management, ale ne přístup k nim. |
> | **ID** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
> | NewRelic.APM/accounts/* |  |

## <a name="reader-and-data-access"></a>Čtenář a přístup k datům
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje zobrazit vše, ale nebude umožňují odstranit nebo vytvořit účet úložiště nebo obsaženého zdroje. Také umožní přístup pro čtení a zápis ke všem datům obsaženým v účtu úložiště prostřednictvím přístup k klíče účtu úložiště. |
> | **ID** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Akce** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu. |

## <a name="redis-cache-contributor"></a>Přispěvatel služby Redis Cache
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu mezipamětí Redis, ale ne přístup k nim. |
> | **ID** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení role a role přiřazení |
> | Microsoft.Cache/redis/* | Vytváření a správě mezipaměti Redis |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="resource-policy-contributor-preview"></a>Přispěvatel zásad prostředků (Preview)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | (Preview) Obnovení uživatelé z EA s oprávněními vytvářet a upravovat zásady prostředků, vytvářet lístky podpory a číst prostředky a hierarchii |
> | **ID** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Akce** |  |
> | * / Číst | Přečtěte si prostředky všech typů, s výjimkou tajných klíčů. |
> | Microsoft.Authorization/policyassignments/* | Vytvoření a správa přiřazení zásad. |
> | Microsoft.Authorization/policydefinitions/* | Vytvářet a spravovat definice zásady |
> | Microsoft.Authorization/policysetdefinitions/* | Vytvoření a Správa zásad skupiny |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="scheduler-job-collections-contributor"></a>Přispěvatel kolekcí úloh Scheduleru
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu kolekcí úloh Scheduleru, ale ne přístup k nim. |
> | **ID** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení role a role přiřazení |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Scheduler/jobcollections/* | Vytvoření a správa kolekcí úloh |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="search-service-contributor"></a>Přispěvatel služby Search
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu služeb Search, ale ne přístup k nim. |
> | **ID** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení role a role přiřazení |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Search/searchServices/* | Vytvoření a Správa služby vyhledávání |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="security-admin"></a>Správce zabezpečení
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | V Centru zabezpečení pouze: můžete zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, upravit zásady zabezpečení, zobrazení výstrah a doporučení, zavření výstrahy a doporučení |
> | **ID** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Authorization/policyAssignments/* | Vytvoření a správa přiřazení zásad. |
> | Microsoft.Authorization/policyDefinitions/* | Vytvářet a spravovat definice zásady |
> | Microsoft.Authorization/policySetDefinitions/* | Vytvoření a Správa zásad skupiny |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.operationalInsights/workspaces/*/read | Zobrazit data analýzy protokolů |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Security/*/read | Čtení bezpečnostních komponent a zásad |
> | Microsoft.Security/locations/alerts/dismiss/action | Zamítnutí výstrahy zabezpečení |
> | Microsoft.Security/locations/alerts/activate/action | Aktivovat výstrahy zabezpečení |
> | Microsoft.Security/locations/tasks/dismiss/action | Zavření doporučení zabezpečení |
> | Microsoft.Security/locations/tasks/activate/action | Aktivovat doporučení zabezpečení |
> | Microsoft.Security/policies/write | Aktualizace nastavení zásad zabezpečení |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="security-manager-legacy"></a>Správce zabezpečení (starší verze)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Toto je starší verze role. Místo toho použijte Správce zabezpečení. |
> | **ID** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.ClassicCompute/*/read | Přečtěte si informace o konfiguraci klasické virtuální počítače |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Zápis konfigurace pro klasické virtuální počítače |
> | Microsoft.ClassicNetwork/*/read | Přečtěte si informace o konfiguraci o classic sítě |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Security/* | Vytvořit a spravovat zabezpečení komponenty a zásady |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="security-reader"></a>Čtecí zařízení pro zabezpečení
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | V Centru zabezpečení pouze: můžete zobrazit doporučení a výstrahy, zobrazení zásady zabezpečení, zobrazit stavy zabezpečení, ale nelze provádět změny |
> | **ID** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Akce** |  |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.operationalInsights/workspaces/*/read | Zobrazit data analýzy protokolů |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Security/*/read | Čtení bezpečnostních komponent a zásad |

## <a name="site-recovery-contributor"></a>Přispěvatel Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat službu Site Recovery, ale ne vytvářet trezory a přiřazovat role. |
> | **ID** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp je interní operace, kterou používá služba |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp je interní operace, které používá služba |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Operace aktualizace prostředek certifikátu aktualizuje prostředek nebo trezoru certifikát přihlašovacích údajů. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Vytváření a správě rozšířené informace související s trezoru |
> | Microsoft.RecoveryServices/Vaults/read | Operace získání trezoru získá objekt, který reprezentuje prostředků Azure typu 'trezoru. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Vytvářet a spravovat registrovaná identit |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Vytvořit nebo aktualizovat nastavení replikace výstrahy |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Číst všechny události |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Vytvoření a Správa prostředků infrastruktury replikace |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Vytvářet a spravovat úlohy replikace |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Vytvoření a správě zásad replikace |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Vytváření a správě plánů obnovení |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Vytvoření a správě konfigurace úložiště z trezoru služeb zotavení |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Vrátí token informace o trezoru služeb zotavení. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti využití trezoru Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Trezor tokenu operaci lze použít k získání tokenu trezoru pro operace trezoru úrovně back-end. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Načtení výstrah pro trezor služeb zotavení |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="site-recovery-operator"></a>Operátor Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje provádět převzetí služeb při selhání a navrácení služeb po obnovení, ale žádné jiné operace správy služby Site Recovery. |
> | **ID** | 494ae006-db33-4328-BF46-533a6560a3ca |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp je interní operace, kterou používá služba |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp je interní operace, které používá služba |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Microsoft.RecoveryServices/Vaults/read | Operace získání trezoru získá objekt, který reprezentuje prostředků Azure typu 'trezoru. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Výsledky operace Get, lze operace získat stav operace a výsledek asynchronně odeslaná operaci |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Získat kontejnerů, které můžete použít operaci získat kontejnery zaregistrovat pro prostředek. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Číst všechny nastavení výstrah |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Číst všechny události |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Zkontroluje konzistenci prostředí infrastruktury. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Číst všechny prostředky infrastruktury |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Přidružení brány |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Obnovit certifikát pro prostředky infrastruktury |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Přečtěte si žádné sítě. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Číst veškerá jeho mapování sítě |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Číst všechny kontejnery ochrany |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Číst všechny položky, které jsou předmětem ochrany |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Použít bod obnovení |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Potvrzení převzetí služeb při selhání |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Plánované převzetí služeb při selhání |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Číst všechny chráněné položky |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Číst všechny body obnovení replikace |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Oprava replikace |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Znovu aktivujte ochranu chráněné položky |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Testovací převzetí služeb při selhání |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Vyčistit testovací převzetí služeb při selhání |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Převzetí služeb při selhání |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Aktualizace služby Mobility |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Číst veškerá jeho mapování kontejnerů ochrany |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Přečtěte si zprostředkovatelů služby obnovení |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Aktualizujte zprostředkovatele |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Číst všechny klasifikace úložiště |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Číst veškerá jeho mapování klasifikace úložiště |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Číst všechny úlohy |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Vytvářet a spravovat úlohy replikace |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Číst všechny zásady |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plán obnovení potvrzení převzetí služeb při selhání |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plán obnovení plánované převzetí služeb při selhání |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Číst všechny plány obnovení |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Znovu nastavte ochranu plánu obnovení |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Testovací převzetí služeb při selhání obnovení plán |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plán obnovení testovací převzetí služeb při selhání čištění |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plán obnovení převzetí služeb při selhání |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Načtení výstrah pro trezor služeb zotavení |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Vrátí token informace o trezoru služeb zotavení. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti využití trezoru Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Trezor tokenu operaci lze použít k získání tokenu trezoru pro operace trezoru úrovně back-end. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="site-recovery-reader"></a>Čtenář Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje zobrazovat stav služby Site Recovery, ale ne provádět jiné operace správy. |
> | **ID** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp je interní operace, kterou používá služba |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Získá výstrahy pro trezor služeb zotavení. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | Operace získání trezoru získá objekt, který reprezentuje prostředků Azure typu 'trezoru. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Výsledky operace Get, lze operace získat stav operace a výsledek asynchronně odeslaná operaci |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Získat kontejnerů, které můžete použít operaci získat kontejnery zaregistrovat pro prostředek. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Číst všechny nastavení výstrah |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Číst všechny události |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Číst všechny prostředky infrastruktury |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Přečtěte si žádné sítě. |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Číst veškerá jeho mapování sítě |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Číst všechny kontejnery ochrany |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Číst všechny položky, které jsou předmětem ochrany |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Číst všechny chráněné položky |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Číst všechny body obnovení replikace |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Číst veškerá jeho mapování kontejnerů ochrany |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Přečtěte si zprostředkovatelů služby obnovení |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Číst všechny klasifikace úložiště |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Číst veškerá jeho mapování klasifikace úložiště |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Číst všechny úlohy |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Číst všechny úlohy |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Číst všechny zásady |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Číst všechny plány obnovení |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Vrátí token informace o trezoru služeb zotavení. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti využití trezoru Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Trezor tokenu operaci lze použít k získání tokenu trezoru pro operace trezoru úrovně back-end. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="sql-db-contributor"></a>Přispěvatel databází SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat databáze SQL, ale ne přístup k nim. Navíc se nedají spravovat jejich zásady zabezpečení nebo jejich nadřazené servery SQL. |
> | **ID** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení role a role přiřazení |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | Vytvářet a spravovat databáze SQL |
> | Microsoft.Sql/servers/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
> | **NotActions** |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Nelze upravit zásady auditu |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Nelze upravit nastavení auditu |
> | Microsoft.Sql/servers/databases/auditRecords/read | Načtení záznamů auditu databáze objektů blob |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Nelze upravit zásady připojení |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Nelze upravit zásady maskování dat. |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Nelze upravovat zásady, výstrah zabezpečení |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Nelze upravit zabezpečení metriky |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |

## <a name="sql-security-manager"></a>Správce zabezpečení SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje vám spravovat zásady vztahující se k zabezpečení serverů a databází SQL, ale ne přístup k nim. |
> | **ID** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Autorizace Microsoft pro čtení |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Spojí prostředků, jako je například účet úložiště nebo SQL database k podsíti. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Sql/servers/auditingPolicies/* | Vytvořit a spravovat zásady auditování SQL serveru |
> | Microsoft.Sql/servers/auditingSettings/* | Vytvářet a spravovat nastavení auditování serveru SQL |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Vytvářet a spravovat zásady auditu databáze serveru SQL |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Vytvářet a spravovat nastavení auditování databáze SQL serveru |
> | Microsoft.Sql/servers/databases/auditRecords/read | Záznamy auditu pro čtení |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Vytvářet a spravovat zásady připojení databáze serveru SQL |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Vytvářet a spravovat zásady maskování dat databáze serveru SQL |
> | Microsoft.Sql/servers/databases/read | Vrátí seznam databází nebo získá vlastnosti pro určenou databázi. |
> | Microsoft.Sql/servers/databases/schemas/read | Načíst seznam schémat databáze |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Načíst seznam sloupců tabulky. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Načtení seznamu tabulek databáze |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Vytvoření a Správa výstrah zásady zabezpečení serveru SQL server databáze |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Vytvoření a správa metriky zabezpečení databáze serveru SQL |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Vytvoření a Správa výstrah zásady zabezpečení SQL serveru |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="sql-server-contributor"></a>Přispěvatel SQL Serveru
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje vám spravovat servery a databáze SQL, ale ne přístup k nim ani jejich zásady vztahující se k zabezpečení. |
> | **ID** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Vytvoření a správa serverů SQL |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
> | **NotActions** |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Nelze upravit zásady auditu SQL serveru |
> | Microsoft.Sql/servers/auditingSettings/* | Nastavení auditování serveru SQL server nelze upravit. |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Nelze upravit zásady auditu databáze serveru SQL |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Nelze upravit nastavení auditování databáze SQL serveru |
> | Microsoft.Sql/servers/databases/auditRecords/read | Nelze přečíst záznamů auditu |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Nelze upravit zásady připojení databáze serveru SQL |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Nelze upravit maskování zásady data v databázi SQL serveru |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Nelze upravit výstrahy zásady zabezpečení serveru SQL server databáze |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Nelze upravit metriky zabezpečení databáze serveru SQL |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Nelze upravit výstrahy zásady zabezpečení SQL serveru |

## <a name="storage-account-contributor"></a>Přispěvatel účtů úložiště
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu účtů úložiště, ale ne přístup k nim. |
> | **ID** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Číst všechny autorizace |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.Insights/diagnosticSettings/* | Spravovat nastavení pro diagnostiku |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Spojí prostředků, jako je například účet úložiště nebo SQL database k podsíti. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Storage/storageAccounts/* | Vytváření a správě účtů úložiště |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="storage-account-key-operator-service-role"></a>Role služby Operátor klíčů účtů úložiště
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Operátoři klíčů účtů úložiště můžou vypisovat a znovu generovat klíče pro účty úložiště. |
> | **ID** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Akce** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Obnoví přístupové klíče pro zadaný účet úložiště. |

## <a name="storage-blob-data-contributor-preview"></a>Přispěvatel dat objektu blob služby Storage (Preview)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje číst, zapisovat a odstraňovat kontejnery a data objektů blob služby Azure Storage. |
> | **ID** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Akce** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Vrátí výsledek odstraňování kontejneru. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Vrátí kontejner nebo seznam kontejnerů. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Vrátí výsledek vložení nebo vypůjčení kontejneru objektů blob. |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Vrátí výsledek odstraňování objektu blob. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Vrátí objekt blob nebo seznam objektů blob. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Vrátí výsledek zápisu objektu blob. |

## <a name="storage-blob-data-reader-preview"></a>Čtenář dat objektu blob služby Storage (Preview)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje číst kontejnery a data objektů blob služby Azure Storage. |
> | **ID** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Akce** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Vrátí kontejner nebo seznam kontejnerů. |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Vrátí objekt blob nebo seznam objektů blob. |

## <a name="storage-queue-data-contributor-preview"></a>Přispěvatel dat fronty služby Storage (Preview)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje číst, zapisovat a odstraňovat fronty služby Azure Storage a jejich zprávy. |
> | **ID** | 974c5e8b-45B9-4653-ba55-5f855dd0fb88 |
> | **Akce** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Vrátí výsledek odstranění fronty. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Vrátí frontu nebo seznam front. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Vrátí výsledek zápisu fronty. |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Vrátí výsledek odstranění zprávy. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Vrátí zprávu. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Vrátí výsledek zápisu zprávy. |

## <a name="storage-queue-data-reader-preview"></a>Čtenář dat fronty služby Storage (Preview)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje číst fronty služby Azure Storage a jejich zprávy. |
> | **ID** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Akce** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Vrátí frontu nebo seznam front. |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Vrátí zprávu. |

## <a name="support-request-contributor"></a>Přispěvatel žádostí o podporu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje vytvořit a spravovat žádosti o podporu. |
> | **ID** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Autorizace pro čtení |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="traffic-manager-contributor"></a>Přispěvatel Traffic Manageru
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat profily Traffic Manageru, ale neumožňuje řídit, kdo k nim má přístup. |
> | **ID** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="user-access-administrator"></a>Správce přístupu uživatelů
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu přístupu uživatelů k prostředkům Azure. |
> | **ID** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Akce** |  |
> | * / Číst | Přečtěte si prostředky všech typů, s výjimkou tajných klíčů. |
> | Microsoft.Authorization/* | Spravovat autorizace |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="virtual-machine-administrator-login"></a>Přihlášení správce virtuálního počítače
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | –  Uživatelé s touto rolí se budou moct přihlašovat k virtuálnímu počítači s oprávněními správce Windows nebo uživatele root v Linuxu. |
> | **ID** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Akce** |  |
> | Microsoft.Network/publicIPAddresses/read | Získá definici veřejné ip adresy. |
> | Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
> | Microsoft.Network/loadBalancers/read | Získá definici nástroje pro vyrovnávání zatížení. |
> | Microsoft.Network/networkInterfaces/read | Získá definici rozhraní sítě.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Přihlášení k virtuálnímu počítači jako běžný uživatel |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Přihlášení k virtuálnímu počítači s oprávněními správce ve Windows nebo kořenovými oprávněními na Linuxu |

## <a name="virtual-machine-contributor"></a>Přispěvatel virtuálních počítačů
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje spravovat virtuální počítače, ale ne přístup k nim a ne virtuální sítě nebo účet úložiště, který jste připojení k. |
> | **ID** | 9980e02c-c2be-4D73-94e8-173b1dc7cf3c |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Autorizace pro čtení |
> | Microsoft.Compute/availabilitySets/* | Vytvářet a spravovat skupiny dostupnosti výpočetní |
> | Microsoft.Compute/locations/* | Vytváření a správě výpočetních umístění |
> | Microsoft.Compute/virtualMachines/* | Vytvářet a spravovat virtuální počítače |
> | Microsoft.Compute/virtualMachineScaleSets/* | Vytvářet a spravovat sady škálování virtuálního počítače |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Spojí fond adres back-end brány aplikace |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Spojí fond back-end adresy Vyrovnávání zatížení. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Spojí Vyrovnávání zatížení fond příchozích pravidel nat |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Spojí pravidlo příchozí nat nástroje pro vyrovnávání zatížení. |
> | Microsoft.Network/loadBalancers/probes/join/action | Umožňuje použití sondy nástroje pro vyrovnávání zatížení. S touto vlastností healthProbe oprávnění měřítka virtuálních počítačů sady můžete odkazovat, sonda. |
> | Microsoft.Network/loadBalancers/read | Získá definici nástroje pro vyrovnávání zatížení. |
> | Microsoft.Network/locations/* | Vytvářet a spravovat síťová umístění |
> | Microsoft.Network/networkInterfaces/* | Vytváření a správě síťových rozhraní |
> | Microsoft.Network/networkSecurityGroups/join/action | Spojí skupinu zabezpečení sítě |
> | Microsoft.Network/networkSecurityGroups/read | Získá definici skupiny zabezpečení sítě |
> | Microsoft.Network/publicIPAddresses/join/action | Spojí veřejnou ip adresu. |
> | Microsoft.Network/publicIPAddresses/read | Získá definici veřejné ip adresy. |
> | Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Připojí virtuální sítě |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Vrátí podrobnosti o objektu chráněné položky |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Vytvoření zálohy chráněné položky |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Vytvoření zálohy záměr ochrany |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Vrátí všechny zásady ochrany |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Vytvoří zásadu ochrany |
> | Microsoft.RecoveryServices/Vaults/read | Operace získání trezoru získá objekt, který reprezentuje prostředků Azure typu 'trezoru. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti využití trezoru Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/write | Operace Vytvořit trezor vytvoří prostředek Azure typu trezor. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
> | Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="virtual-machine-user-login"></a>Přihlášení uživatele virtuálního počítače
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Uživatelé s touto rolí se budou moct přihlašovat k virtuálnímu počítači jako běžní uživatelé. |
> | **ID** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Akce** |  |
> | Microsoft.Network/publicIPAddresses/read | Získá definici veřejné ip adresy. |
> | Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
> | Microsoft.Network/loadBalancers/read | Získá definici nástroje pro vyrovnávání zatížení. |
> | Microsoft.Network/networkInterfaces/read | Získá definici rozhraní sítě.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Přihlášení k virtuálnímu počítači jako běžný uživatel |

## <a name="web-plan-contributor"></a>Přispěvatel webových plánů
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu webových plánů pro weby, ale ne přístup k nim. |
> | **ID** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Autorizace pro čtení |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
> | Microsoft.Web/serverFarms/* | Vytvoření a správa serverové farmy |

## <a name="website-contributor"></a>Přispěvatel webů
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Popis** | Umožňuje správu webů (ne webových plánů), ale ne přístup k nim. |
> | **ID** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Akce** |  |
> | Microsoft.Authorization/*/read | Autorizace pro čtení |
> | Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
> | Microsoft.Insights/components/* | Vytvoření a správa přehled komponenty |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
> | Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
> | Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
> | Microsoft.Web/certificates/* | Vytvářet a spravovat certifikáty webu |
> | Microsoft.Web/listSitesAssignedToHostName/read | Získáte názvy lokalit, které jsou přiřazeny k hostitele. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Umožňuje získat vlastnosti na plán služby App Service |
> | Microsoft.Web/sites/* | Vytvoření a správa webů (vytváření webu taky vyžaduje oprávnění k zápisu do přidružené plán služby App Service) |

## <a name="next-steps"></a>Další postup

- [Vlastní role](custom-roles.md)
- [Spravovat přiřazení role pomocí portálu Azure](role-assignments-portal.md)
- [Oprávnění v Azure Security Center](../security-center/security-center-permissions.md)
