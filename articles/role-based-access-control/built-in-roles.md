---
title: Předdefinované role pro řízení přístupu Azure na základě rolí (RBAC) | Microsoft Docs
description: Toto téma popisuje předdefinovaných do rolí pro řízení přístupu na základě role (RBAC). Role jsou neustále přidali, proto zkontrolujte aktuálnosti dokumentaci.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: it-pro
ms.openlocfilehash: 3b689c6028470021734ad78dd88748f4079c383f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="built-in-roles-for-azure-role-based-access-control"></a>Předdefinované role pro řízení přístupu Azure na základě rolí
Azure na základě rolí řízení přístupu (RBAC) obsahuje následující předdefinované role, které lze přiřadit uživatelům, skupinám a službám. Definice předdefinované role se nedá změnit. Můžete však vytvořit [vlastní role v Azure RBAC](custom-roles.md) podle konkrétních potřeb vaší organizace.

## <a name="built-in-role-descriptions"></a>Popisy předdefinovaná role
Následující tabulka obsahuje stručný popis předdefinovaných rolí. Klikněte na název role, které chcete zobrazit podrobný seznam **akce** a **notactions** pro roli. **Akce** vlastnost určuje povolené akce u prostředků Azure. Řetězce akce můžete použít zástupné znaky. **Notactions** vlastnost určuje akce, které jsou vyloučeny z povolených akcí.

Akce definuje, jaký typ operace můžete provádět na typ daného prostředku. Příklad:
- **Zápis** umožňuje provádět operace PUT, POST, PATCH a DELETE.
- **Čtení** umožňuje provádět operace GET.

V tomto článku pouze adresy různé role, které existují ještě dnes. Když přiřadíte roli pro uživatele, ale můžete omezit povolených akcí další definováním obor. To je užitečné, pokud chcete, aby někdo webu Přispěvatel, ale jenom u jedné skupiny prostředků.

> [!NOTE]
> Definice Azure role se neustále vyvíjejí. Tento článek se ukládají jako aktuální nejblíže, ale vždy najdete nejnovější definice rolí v prostředí Azure PowerShell. Použití [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) rutiny seznamu všechny aktuální role. Můžete podrobné informace konkrétní roli pomocí `(get-azurermroledefinition "<role name>").actions` nebo `(get-azurermroledefinition "<role name>").notactions` podle. Použití [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) k operacím seznamu zprostředkovatelů konkrétních prostředků Azure.


| Předdefinovaná role | Popis |
| --- | --- |
| [Vlastník](#owner) | Umožňuje správu všech položek včetně přístupu k prostředkům. |
| [Přispěvatel](#contributor) | Umožňuje správu všech položek kromě přístupu k prostředkům. |
| [Čtecí modul](#reader) | Umožňuje zobrazit všechny položky, ale ne provádět změny. |
| [Rozhraní API správy služby přispěvatele](#api-management-service-contributor) | Může spravovat službu a rozhraní API |
| [Role operátora služby API Management](#api-management-service-operator-role) | Může spravovat službu, ale ne rozhraní API. |
| [Role čtenáře služby API Management](#api-management-service-reader-role) | Přístup ke službě a rozhraním API jen ke čtení |
| [Application Insights součást Přispěvatel](#application-insights-component-contributor) | Může spravovat součásti Application Insights. |
| [Application Insights snímku ladicí program](#application-insights-snapshot-debugger) | Uděluje oprávnění uživatele používat funkce ladicího programu pro snímky Application Insights. |
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
| [Přispěvatel Classic virtuálních počítačů](#classic-virtual-machine-contributor) | Umožňuje spravovat klasické virtuální počítače, ale ne přístup k nim a nikoli na virtuální sítě nebo účet úložiště, který jste připojení k. |
| [Databáze MySQL cleardb – DB přispěvatele](#cleardb-mysql-db-contributor) | Umožňuje správu databází ClearDB MySQL, ale ne přístup k nim. |
| [Role čtenáře účet cosmos DB](#cosmos-db-account-reader-role) | Můžete číst data účtu Azure Cosmos DB. V tématu [Přispěvatel účet DocumentDB](#documentdb-account-contributor) pro správu účtů Azure Cosmos DB. |
| [Přispěvatel objekt pro vytváření dat](#data-factory-contributor) | Umožňuje vytvářet a spravovat datové továrny i podřízené prostředky v nich. |
| [Data Lake Analytics vývojáře](#data-lake-analytics-developer) | Umožňuje odesílat, monitorovat a spravovat vlastní úlohy, ale neumožňuje vytvářet ani odstraňovat účty Data Lake Analytics. |
| [Uživatel DevTest Labs](#devtest-labs-user) | Umožňuje spouštět, restartovat a vypínat virtuální počítače v Azure DevTest Labs a připojovat se k nim. |
| [Přispěvatel zóny DNS](#dns-zone-contributor) | Umožňuje spravovat zóny DNS a sady záznamů v Azure DNS, ale neumožňuje řídit, kdo k nim má přístup. |
| [Přispěvatel účtu DocumentDB](#documentdb-account-contributor) | Ke správě účtů Azure Cosmos DB. Azure Cosmos DB je dříve označované jako DocumentDB. |
| [Přispěvatel účet inteligentních systémů](#intelligent-systems-account-contributor) | Umožňuje správu účtů inteligentních systémů, ale ne přístup k nim. |
| [Přispěvatel trezoru klíčů](#key-vault-contributor) | Umožňuje spravovat trezory klíčů, ale ne přistupovat k nim. |
| [Tvůrce testovacího prostředí](#lab-creator) | Umožňuje vytvořit, spravovat, odstraňovat vaše spravované labs pod vaše účty testovacího prostředí Azure. |
| [Přispěvatel analýzy protokolů](#log-analytics-contributor) | Log Analytics Přispěvatel můžete načíst všechna data monitorování a upravit nastavení monitorování. Úprava nastavení monitorování, zahrnuje přidání rozšíření virtuálního počítače pro virtuální počítače; čtení klíče účtu úložiště, abyste mohli nakonfigurovat shromažďování protokolů ze služby Azure Storage; vytváření a konfiguraci účtů Automation; Přidání řešení; a konfigurace Azure diagnostics pro všechny prostředky Azure. |
| [Log Analytics Reader](#log-analytics-reader) | Čtenář Log Analytics si může zobrazit a vyhledat všechna data monitorování a jeho nastavení. Může si zobrazit konfiguraci diagnostiky Azure na všech prostředcích Azure. |
| [Přispěvatel aplikace logiky](#logic-app-contributor) | Umožňuje správu aplikace logiky, ale ne přístup k ní. |
| [Operátor aplikace logiky](#logic-app-operator) | Umožňuje číst, povolovat a zakazovat aplikaci logiky. |
| [Spravované Identity přispěvatele](#managed-identity-contributor) | Vytvářet, číst, aktualizovat a odstraňovat přiřazeného Identity uživatele |
| [Operátor spravované Identity](#managed-identity-operator) | Přečtěte si a přiřadit přiřazeného Identity uživatele |
| [Monitorování přispěvatele](#monitoring-contributor) | Můžete načíst všechna data monitorování a upravit nastavení monitorování. Viz také [začít pracovat s rolí, oprávnění a zabezpečení pomocí Azure monitorování](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Monitorování čtečky](#monitoring-reader) | Může číst veškerá monitorování data (metriky, protokoly, atd.). Viz také [začít pracovat s rolí, oprávnění a zabezpečení pomocí Azure monitorování](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Přispěvatel sítě](#network-contributor) | Umožňuje správu sítí, ale ne přístup k nim. |
| [Nový přispěvatel účet New Relic APM](#new-relic-apm-account-contributor) | Umožňuje správu účtů a aplikací New Relic Application Performance Management, ale ne přístup k nim. |
| [Přispěvatel mezipaměti redis](#redis-cache-contributor) | Umožňuje správu mezipamětí Redis, ale ne přístup k nim. |
| [Přispěvatel kolekce úloh plánovače](#scheduler-job-collections-contributor) | Umožňuje správu kolekcí úloh Scheduleru, ale ne přístup k nim. |
| [Přispěvatel služby vyhledávání](#search-service-contributor) | Umožňuje správu služeb Search, ale ne přístup k nim. |
| [Správce zabezpečení](#security-admin) | V Centru zabezpečení pouze: můžete zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, upravit zásady zabezpečení, zobrazení výstrah a doporučení, zavření výstrahy a doporučení |
| [Správce zabezpečení](#security-manager) | Umožňuje správu součástí zabezpečení, zásad zabezpečení a virtuálních počítačů. |
| [Čtečka zabezpečení](#security-reader) | V Centru zabezpečení pouze: můžete zobrazit doporučení a výstrahy, zobrazení zásady zabezpečení, zobrazit stavy zabezpečení, ale nelze provádět změny |
| [Přispěvatel obnovení lokality](#site-recovery-contributor) | Umožňuje spravovat službu Site Recovery, ale ne vytvářet trezory a přiřazovat role. |
| [Operátor obnovení lokality](#site-recovery-operator) | Umožňuje provádět převzetí služeb při selhání a navrácení služeb po obnovení, ale žádné jiné operace správy služby Site Recovery. |
| [Čtečka obnovení lokality](#site-recovery-reader) | Umožňuje zobrazovat stav služby Site Recovery, ale ne provádět jiné operace správy. |
| [Přispěvatel databází SQL](#sql-db-contributor) | Umožňuje spravovat databáze SQL, ale ne přístup k nim. Navíc se nedají spravovat jejich zásady zabezpečení nebo jejich nadřazené servery SQL. |
| [Správce zabezpečení SQL](#sql-security-manager) | Umožňuje vám spravovat zásady vztahující se k zabezpečení serverů a databází SQL, ale ne přístup k nim. |
| [Přispěvatel serveru SQL](#sql-server-contributor) | Umožňuje vám spravovat servery a databáze SQL, ale ne přístup k nim ani jejich zásady vztahující se k zabezpečení. |
| [Přispěvatel účtu úložiště](#storage-account-contributor) | Umožňuje správu účtů úložiště, ale ne přístup k nim. |
| [Role služby operátor klíče účtu úložiště](#storage-account-key-operator-service-role) | Operátoři klíčů účtů úložiště můžou vypisovat a znovu generovat klíče pro účty úložiště. |
| [Přispěvatel žádosti o podporu](#support-request-contributor) | Umožňuje vytvořit a spravovat žádosti o podporu. |
| [Přispěvatel Traffic Manageru](#traffic-manager-contributor) | Umožňuje spravovat profily Traffic Manageru, ale neumožňuje řídit, kdo k nim má přístup. |
| [Správce přístupu uživatelů](#user-access-administrator) | Umožňuje správu přístupu uživatelů k prostředkům Azure. |
| [Přihlášení správce virtuálních počítačů](#virtual-machine-administrator-login) | – Uživatelé s touto rolí mají možnost přihlášení k virtuálnímu počítači pomocí Správce služby Windows nebo Linux oprávnění uživatele root. |
| [Přispěvatel virtuálních počítačů](#virtual-machine-contributor) | Umožňuje spravovat virtuální počítače, ale ne přístup k nim a ne virtuální sítě nebo účet úložiště, který jste připojení k. |
| [Přihlášení uživatele virtuálního počítače](#virtual-machine-user-login) | Uživatelé s touto rolí mají možnost přihlášení k virtuálnímu počítači jako běžný uživatel. |
| [Plán přispěvatelů webu](#web-plan-contributor) | Umožňuje správu webových plánů pro weby, ale ne přístup k nim. |
| [Přispěvatel webu](#website-contributor) | Umožňuje správu webů (ne webových plánů), ale ne přístup k nim. |

Následující tabulky popisují konkrétní oprávnění na každou roli. To může zahrnovat **akce**, který udělit oprávnění, a **NotActions**, což je omezit.

## <a name="owner"></a>Vlastník
Umožňuje správu všech položek včetně přístupu k prostředkům.

| **Akce** |  |
| --- | --- |
| * | Vytvořit a spravovat prostředky všech typů |

## <a name="contributor"></a>Přispěvatel
Umožňuje správu všech položek kromě přístupu k prostředkům.

| **Akce** |  |
| --- | --- |
| * | Vytvořit a spravovat prostředky všech typů |

| **NotActions** |  |
| --- | --- |
| Microsoft.Authorization/*/Delete | Nelze odstranit role a přiřazení rolí |
| Microsoft.Authorization/*/Write | Nelze vytvořit role a přiřazení rolí |
| Microsoft.Authorization/elevateAccess/Action | Udělí volajícímu přístup Správce uživatelských přístupů v oboru tenanta. |

## <a name="reader"></a>Čtenář
Umožňuje zobrazit všechny položky, ale ne provádět změny.

| **Akce** |  |
| --- | --- |
| * / Číst | Přečtěte si prostředky všech typů, s výjimkou tajných klíčů. |

## <a name="api-management-service-contributor"></a>Přispěvatel služby API Management
Může spravovat službu a rozhraní API

| **Akce** |  |
| --- | --- |
| Microsoft.ApiManagement/service/* | Vytvářet a spravovat služby API Management |
| Microsoft.Authorization/*/read | Autorizace pro čtení |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="api-management-service-operator-role"></a>Role Operátor služby API Management
Může spravovat službu, ale ne rozhraní API.

| **Akce** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | Instance služby API Management pro čtení |
| Microsoft.ApiManagement/service/backup/action | Zálohování službu správy rozhraní API a zadaného kontejneru. v uživatel zadaný účet úložiště |
| Microsoft.ApiManagement/service/delete | Odstranění instance služby API Management |
| Microsoft.ApiManagement/service/managedeployments/action | Změnit SKU nebo jednotky, přidat nebo odebrat místní nasazení služby API Management |
| Microsoft.ApiManagement/service/read | Číst metadata pro instanci služby API Management |
| Microsoft.ApiManagement/service/restore/action | Obnovení ze zadaného kontejneru v uživatel zadaný účet úložiště služby API Management |
| Microsoft.ApiManagement/service/updatecertificate/action | Nahrajte certifikát SSL pro službu správy rozhraní API |
| Microsoft.ApiManagement/service/updatehostname/action | Instalační program, aktualizovat nebo odebrat vlastní názvy domén pro služby API Management |
| Microsoft.ApiManagement/service/write | Vytvoření nové instance služby API Management |
| Microsoft.Authorization/*/read | Autorizace pro čtení |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | Získat seznam klíčů uživatele |

## <a name="api-management-service-reader-role"></a>Role Čtenář služby API Management
Přístup ke službě a rozhraním API jen ke čtení

| **Akce** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | Instance služby API Management pro čtení |
| Microsoft.ApiManagement/service/read | Číst metadata pro instanci služby API Management |
| Microsoft.Authorization/*/read | Autorizace pro čtení |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | Získat seznam klíčů uživatele |

## <a name="application-insights-component-contributor"></a>Přispěvatel součástí Application Insights
Může spravovat součásti Application Insights.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
| Microsoft.Insights/components/* | Vytvoření a správa přehled komponenty |
| Microsoft.Insights/webtests/* | Vytvoření a Správa webové testy |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="application-insights-snapshot-debugger"></a>Ladicí program snímků Application Insights
Uděluje oprávnění uživatele používat funkce ladicího programu pro snímky Application Insights.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.Insights/components/*/read |  |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="automation-job-operator"></a>Operátor úloh Automation
Vytváření a správa úloh pomocí runbooků služby Automation

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Automation/automationAccounts/jobs/read | Získá úlohu automatizace Azure |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Obnoví úlohu automatizace Azure |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Zastaví úlohu Azure Automation |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Čte prostředky Hybrid Runbook Worker |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Získá stream úloh Azure Automation |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Pozastaví úlohu Azure Automation |
| Microsoft.Automation/automationAccounts/jobs/write | Vytvoří úlohu Azure Automation |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="automation-operator"></a>Operátor služby Automation
Operátoři Automation můžou spouštět, zastavovat, pozastavovat a obnovovat úlohy.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Automation/automationAccounts/jobs/read | Získá úlohu automatizace Azure |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Obnoví úlohu automatizace Azure |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Zastaví úlohu Azure Automation |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Získá stream úloh Azure Automation |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Pozastaví úlohu Azure Automation |
| Microsoft.Automation/automationAccounts/jobs/write | Vytvoří úlohu Azure Automation |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Získá plán úloh Azure Automation. |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Vytvoří plán úloh Azure Automation. |
| Microsoft.Automation/automationAccounts/linkedWorkspace/read | Získá prostoru propojí s účtem automation. |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Čte prostředky Hybrid Runbook Worker |
| Microsoft.Automation/automationAccounts/read | Získá účet automatizace Azure |
| Microsoft.Automation/automationAccounts/runbooks/read | Načte runbook služby automatizace Azure |
| Microsoft.Automation/automationAccounts/schedules/read | Získá prostředek plánování Azure Automation. |
| Microsoft.Automation/automationAccounts/schedules/write | Vytvoří nebo aktualizuje prostředek plánování Azure Automation. |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="automation-runbook-operator"></a>Operátor runbooků Automation
Čtení vlastností runbooků – aby se daly vytvářet úlohy daného runbooku

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Automation/automationAccounts/runbooks/read | Načte runbook služby automatizace Azure |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="azure-stack-registration-owner"></a>Vlastník registrace Azure Stack
Umožňuje spravovat registrace Azure Stack.

| **Akce** |  |
| --- | --- |
| Microsoft.AzureStack/registrations/products/listDetails/action | Načte Rozšířené podrobnosti o produkt Azure Marketplace zásobníku |
| Microsoft.AzureStack/registrations/products/read | Získá vlastnosti o produkt Azure Marketplace zásobníku |
| Microsoft.AzureStack/registrations/read | Získá vlastnosti zápisu Azure zásobníku |

## <a name="backup-contributor"></a>Přispěvatel zálohování
Umožňuje spravovat službu zálohování, ale neumožňuje vytvářet trezory a udělovat přístup ostatním uživatelům.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Spravovat výsledky operace na správu záloh |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Vytvářet a spravovat zálohy kontejnery uvnitř zálohování Fabric trezor služeb zotavení |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Vytvářet a spravovat úlohy zálohování |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Export úloh |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Vytvářet a spravovat metadata související se správou zálohování |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Vytvořit a spravovat výsledky operací zálohování správy |
| Microsoft.RecoveryServices/Vaults/backupPolicies/* | Vytvářet a spravovat zásady zálohování |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Vytváření a Správa položek, které lze zálohovat |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Vytváření a správa zálohovaných položek |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Vytvoření a Správa kontejnerů, která uchovává položky zálohování |
| Microsoft.RecoveryServices/Vaults/certificates/* | Vytváření a správě certifikátů související s zálohování v trezoru služeb zotavení |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Vytváření a správě rozšířené informace související s trezoru |
| Microsoft.RecoveryServices/Vaults/read | Operace získání trezoru získá objekt, který reprezentuje prostředků Azure typu 'trezoru. |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Spravovat operaci zjišťování pro načítání nově vytvořený kontejnery |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Vytvářet a spravovat registrovaná identit |
| Microsoft.RecoveryServices/Vaults/usages/* | Vytvářet a spravovat využití trezor služeb zotavení |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Vrátí souhrny pro chráněné položky a chráněné servery pro služeb zotavení. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu. |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp je interní operace, kterou používá služba |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Získá výstrahy pro trezor služeb zotavení. |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Vrátí výsledek operace úlohy exportu. |
| Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="backup-operator"></a>Operátor zálohování
Umožňuje spravovat služby zálohování s výjimkou odebírání záloh, vytváření trezorů a udělování přístupu jiným uživatelům.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Vrátí stav operace |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/čtení | Načte výsledky operace provedené na kontejneru ochrany. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/zálohování nebo akce | Provede zálohování chráněné položky. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/čtení | Načte výsledky operace provedené na chráněných položkách. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/čtení | Načte stav operace provedené na chráněných položkách. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/čtení | Vrátí podrobnosti o objektu chráněné položky |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/čtení | Načíst body obnovení pro chráněné položky |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints nebo obnovení nebo akce | Obnoví body obnovení pro chráněné položky |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems a zápis | Vytvoření zálohy chráněné položky |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Vrátí všechny registrované kontejnery |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Vytvářet a spravovat úlohy zálohování |
| Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Zrušení úlohy |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Vrátí výsledek operace úlohy. |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | Vrátí všechny objekty úlohy |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Export úloh |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Vrátí metadata správy zálohování trezoru Recovery Services. |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Vytvořit a spravovat výsledky operací zálohování správy |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Načte výsledky operace zásad. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Vrátí všechny zásady ochrany |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Vytváření a Správa položek, které lze zálohovat |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Vrátí seznam chránitelných položek. |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Vrátí seznam všech chráněných položek. |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Vrátí všechny kontejnery, které patří k předplatnému |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Vrátí souhrny pro chráněné položky a chráněné servery pro služeb zotavení. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/write | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
| Microsoft.RecoveryServices/Vaults/read | Operace získání trezoru získá objekt, který reprezentuje prostředků Azure typu 'trezoru. |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Spravovat operaci zjišťování pro načítání nově vytvořený kontejnery |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Výsledky operace Get, lze operace získat stav operace a výsledek asynchronně odeslaná operaci |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Získat kontejnerů, které můžete použít operaci získat kontejnery zaregistrovat pro prostředek. |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Operaci zaregistrovat kontejneru služby lze použít k registraci kontejner službou obnovení. |
| Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti využití trezoru Recovery Services. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/provisionInstantItemRecovery nebo akce | Zřízení rychlých položky obnovení pro chráněné položky |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/revokeInstantItemRecovery nebo akce | Odvolat rychlých položky obnovení pro chráněné položky |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp je interní operace, kterou používá služba |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Získá výstrahy pro trezor služeb zotavení. |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Vrátí výsledek operace úlohy exportu. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationStatus/read |  |
| Microsoft.RecoveryServices/Vaults/certificates/write | Operace aktualizace prostředek certifikátu aktualizuje prostředek nebo trezoru certifikát přihlašovacích údajů. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="backup-reader"></a>Čtenář záloh
Může zobrazovat služby zálohování, ale nemůže provádět změny.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Vrátí stav operace |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/čtení | Načte výsledky operace provedené na kontejneru ochrany. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/čtení | Načte výsledky operace provedené na chráněných položkách. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/čtení | Načte stav operace provedené na chráněných položkách. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/čtení | Vrátí podrobnosti o objektu chráněné položky |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Vrátí všechny registrované kontejnery |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Vrátí výsledek operace úlohy. |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | Vrátí všechny objekty úlohy |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Export úloh |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Vrátí metadata správy zálohování trezoru Recovery Services. |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Vrátí výsledek operace zálohování trezoru Recovery Services. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Načte výsledky operace zásad. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Vrátí všechny zásady ochrany |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Vrátí seznam všech chráněných položek. |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Vrátí všechny kontejnery, které patří k předplatnému |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Vrátí souhrny pro chráněné položky a chráněné servery pro služeb zotavení. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
| Microsoft.RecoveryServices/Vaults/read | Operace získání trezoru získá objekt, který reprezentuje prostředků Azure typu 'trezoru. |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Výsledky operace Get, lze operace získat stav operace a výsledek asynchronně odeslaná operaci |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Získat kontejnerů, které můžete použít operaci získat kontejnery zaregistrovat pro prostředek. |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp je interní operace, kterou používá služba |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/čtení |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Získá výstrahy pro trezor služeb zotavení. |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/čtení | Načíst body obnovení pro chráněné položky |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Vrátí výsledek operace úlohy exportu. |
| Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti využití trezoru Recovery Services. |

## <a name="billing-reader"></a>Čtenář fakturace
Povolí přístup pro čtení fakturačních údajů.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Billing/*/read | Číst informace o fakturaci |
| Microsoft.Consumption/*/read |  |
| Microsoft.Commerce/*/read |  |
| Microsoft.Management/managementGroups/read | Zobrazí seznam skupin pro správu pro ověřené uživatele. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="biztalk-contributor"></a>Přispěvatel BizTalk
Umožňuje správu služeb BizTalk, ale ne přístup k nim.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.BizTalkServices/BizTalk/* | Vytvářet a spravovat služby BizTalk services |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="cdn-endpoint-contributor"></a>Přispěvatel koncového bodu CDN
Může spravovat koncové body CDN, ale nemůže udělovat přístup jiným uživatelům.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/endpoints/* |  |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="cdn-endpoint-reader"></a>Čtenář koncového bodu CDN
Může zobrazovat koncové body CDN, ale nemůže je měnit.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/endpoints/*/read |  |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="cdn-profile-contributor"></a>Přispěvatel profilu CDN
Může spravovat profily CDN a jejich koncové body, ale nemůže udělovat přístup jiným uživatelům.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/* |  |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="cdn-profile-reader"></a>Čtenář profilu CDN
Může zobrazovat profily CDN a jejich koncové body, ale nemůže je měnit.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/*/read |  |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="classic-network-contributor"></a>Přispěvatel klasických sítí
Umožňuje správu klasických sítí, ale ne přístup k nim.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorizace pro čtení |
| Microsoft.ClassicNetwork/* | Vytvoření a správa klasické sítě |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="classic-storage-account-contributor"></a>Přispěvatel klasických účtů úložiště
Umožňuje správu klasických účtů úložiště, ale ne přístup k nim.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorizace pro čtení |
| Microsoft.ClassicStorage/storageAccounts/* | Vytváření a správě účtů úložiště |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="classic-storage-account-key-operator-service-role"></a>Role služby Operátor klíčů klasických účtů úložiště
Operátoři klíčů klasických účtů úložiště můžou vypisovat a znovu generovat klíče pro klasické účty úložiště.

| **Akce** |  |
| --- | --- |
| Microsoft.ClassicStorage/storageAccounts/listkeys/action | Umožňuje vypsat seznam přístupových klíčů pro účty úložiště. |
| Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Umožňuje znovu vytvořit existující přístupové klíče pro účet úložiště. |

## <a name="classic-virtual-machine-contributor"></a>Přispěvatel klasických virtuálních počítačů
Umožňuje spravovat klasické virtuální počítače, ale ne přístup k nim a nikoli na virtuální sítě nebo účet úložiště, který jste připojení k.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorizace pro čtení |
| Microsoft.ClassicCompute/domainNames/* | Vytvoření a správa klasické výpočetní názvů domén |
| Microsoft.ClassicCompute/virtualMachines/* | Vytvářet a spravovat virtuální počítače |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
| Microsoft.ClassicNetwork/reservedIps/link/action | Odkaz na rezervovanou IP adresu |
| Microsoft.ClassicNetwork/reservedIps/read | Umožňuje načíst vyhrazené IP adresy. |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Připojí se k virtuální síti. |
| Microsoft.ClassicNetwork/virtualNetworks/read | Umožňuje načíst virtuální síť. |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Vrátí disk účtu úložiště. |
| Microsoft.ClassicStorage/storageAccounts/images/read | Vrátí image účtu storage. |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Umožňuje vypsat seznam přístupových klíčů pro účty úložiště. |
| Microsoft.ClassicStorage/storageAccounts/read | Umožňuje načíst účet úložiště se zadaným účtem. |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="cleardb-mysql-db-contributor"></a>Přispěvatel databází ClearDB MySQL
Umožňuje správu databází ClearDB MySQL, ale ne přístup k nim.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
| successbricks.cleardb/Databases/* | Vytvářet a spravovat databáze MySQL cleardb – |

## <a name="cosmos-db-account-reader-role"></a>Role čtenáře účtu Cosmos DB
Můžete číst data účtu Azure Cosmos DB. V tématu [Přispěvatel účet DocumentDB](#documentdb-account-contributor) pro správu účtů Azure Cosmos DB.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí, můžete oprávnění ke čtení zadané pro každého uživatele |
| Microsoft.DocumentDB/*/read | Číst všechny kolekce |
| Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Čte databázi, klíče účtu jen pro čtení. |
| Microsoft.Insights/MetricDefinitions/read | Číst definice metrik |
| Microsoft.Insights/Metrics/read | Čtení metrik |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="data-factory-contributor"></a>Přispěvatel Data Factory
Umožňuje vytvářet a spravovat datové továrny i podřízené prostředky v nich.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení role a role přiřazení |
| Microsoft.DataFactory/dataFactories/* | Vytvořit a spravovat datové továrny a podřízené prostředky v nich. |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="data-lake-analytics-developer"></a>Vývojář Data Lake Analytics
Umožňuje odesílat, monitorovat a spravovat vlastní úlohy, ale neumožňuje vytvářet ani odstraňovat účty Data Lake Analytics.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.BigAnalytics/accounts/* |  |
| Microsoft.DataLakeAnalytics/accounts/* |  |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

| **NotActions** |  |
| --- | --- |
| Microsoft.BigAnalytics/accounts/Delete |  |
| Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
| Microsoft.BigAnalytics/accounts/Write |  |
| Microsoft.DataLakeAnalytics/accounts/Delete | Odstranění účtu DataLakeAnalytics. |
| Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Udělení oprávnění k zrušení úlohy, odeslané jinými uživateli. |
| Microsoft.DataLakeAnalytics/accounts/Write | Vytvořit nebo aktualizovat účet DataLakeAnalytics. |
| Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Vytvořit nebo aktualizovat účet propojené DataLakeStore DataLakeAnalytics účtu. |
| Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Zrušit propojení účtu DataLakeStore z DataLakeAnalytics účtu. |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Vytvořit nebo aktualizovat účet úložiště propojené DataLakeAnalytics účtu. |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Zrušit propojení účtu úložiště z účtu DataLakeAnalytics. |
| Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Vytvořit nebo aktualizovat pravidlo brány firewall. |
| Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Odstraňte pravidlo brány firewall. |
| Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Vytvořit nebo aktualizovat zásadu výpočty. |
| Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Odstraníte zásadu výpočty. |

## <a name="devtest-labs-user"></a>Uživatel služby DevTest Labs
Umožňuje spouštět, restartovat a vypínat virtuální počítače v Azure DevTest Labs a připojovat se k nim.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení role a role přiřazení |
| Microsoft.Compute/availabilitySets/read | Umožňuje získat vlastnosti sady dostupnosti. |
| Microsoft.Compute/virtualMachines/*/read | Číst vlastnosti virtuálního počítače (velikosti virtuálních počítačů, stav modulu runtime, rozšíření virtuálního počítače, atd.) |
| Microsoft.Compute/virtualMachines/deallocate/action | Umožňuje vypnout virtuální počítač a uvolnit výpočetní prostředky. |
| Microsoft.Compute/virtualMachines/read | Umožňuje získat vlastnosti virtuálního počítače. |
| Microsoft.Compute/virtualMachines/restart/action | Umožňuje restartovat virtuální počítač. |
| Microsoft.Compute/virtualMachines/start/action | Umožňuje spustit virtuální počítač. |
| Microsoft.DevTestLab/*/read | Číst vlastnosti služby testovacího prostředí |
| Microsoft.DevTestLab/labs/createEnvironment/action | Vytvoření virtuálních počítačů v testovacím prostředí. |
| Microsoft.DevTestLab/labs/claimAnyVm/action | Deklarace identity náhodných vymahatelných virtuálního počítače v testovacím prostředí. |
| Microsoft.DevTestLab/labs/formulas/delete | Odstraňte vzorce. |
| Microsoft.DevTestLab/labs/formulas/read | Přečtěte si vzorce. |
| Microsoft.DevTestLab/labs/formulas/write | Přidat nebo upravit vzorce. |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Vyhodnotí zásady testovacího prostředí. |
| Microsoft.DevTestLab/labs/virtualMachines/claim/action | Převzetí vlastnictví existujícího virtuálního počítače |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Spojí fond back-end adresy Vyrovnávání zatížení. |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Spojí pravidlo příchozí nat nástroje pro vyrovnávání zatížení. |
| Microsoft.Network/networkInterfaces/*/read | Při čtení vlastností rozhraní sítě (například všechny služby Vyrovnávání zatížení, které síťové rozhraní je součástí) |
| Microsoft.Network/networkInterfaces/join/action | Virtuální počítač připojí k síťovému rozhraní |
| Microsoft.Network/networkInterfaces/read | Získá definici rozhraní sítě.  |
| Microsoft.Network/networkInterfaces/write | Vytvoří rozhraní sítě nebo aktualizuje existující rozhraní sítě.  |
| Microsoft.Network/publicIPAddresses/*/read | Číst vlastnosti služby veřejnou IP adresu |
| Microsoft.Network/publicIPAddresses/join/action | Spojí veřejnou ip adresu. |
| Microsoft.Network/publicIPAddresses/read | Získá definici veřejné ip adresy. |
| Microsoft.Network/virtualNetworks/subnets/join/action | Připojí virtuální sítě |
| Microsoft.Resources/deployments/operations/read | Načte nebo vypíše operace nasazení. |
| Microsoft.Resources/deployments/read | Načte nebo vypíše nasazení. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Storage/storageAccounts/listKeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |

| **NotActions** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/vmSizes/read | Umožňuje zobrazit seznam dostupných velikostí, na které je možné virtuální počítač aktualizovat. |

## <a name="dns-zone-contributor"></a>Přispěvatel zóny DNS
Umožňuje spravovat zóny DNS a sady záznamů v Azure DNS, ale neumožňuje řídit, kdo k nim má přístup.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
| Microsoft.Network/dnsZones/* | Vytváření a Správa zón DNS a záznamů |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="documentdb-account-contributor"></a>Přispěvatel účtů DocumentDB
Ke správě účtů Azure Cosmos DB. Azure Cosmos DB je dříve označované jako DocumentDB.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení role a role přiřazení |
| Microsoft.DocumentDb/databaseAccounts/* | Vytvořit a spravovat účty pro Azure Cosmos DB |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="intelligent-systems-account-contributor"></a>Přispěvatel účtů Inteligentních systémů
Umožňuje správu účtů inteligentních systémů, ale ne přístup k nim.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení role a role přiřazení |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
| Microsoft.IntelligentSystems/accounts/* | Vytváření a správě účtů inteligentních systémů |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="key-vault-contributor"></a>Přispěvatel Key Vaultu
Umožňuje spravovat trezory klíčů, ale ne přistupovat k nim.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.KeyVault/* |  |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

| **NotActions** |  |
| --- | --- |
| Microsoft.KeyVault/locations/deletedVaults/purge/action | Trvale odstraní trezor klíčů, který se nejdříve odstranil obnovitelně. |
| Microsoft.KeyVault/hsmPools/* |  |

## <a name="lab-creator"></a>Tvůrce testovacího prostředí
Umožňuje vytvořit, spravovat, odstraňovat vaše spravované labs pod vaše účty testovacího prostředí Azure.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.ManagedLab/labAccounts/createLab/action | Vytvoření testovacího prostředí v účtu testovacího prostředí. |
| Microsoft.ManagedLab/labAccounts/*/read |  |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="log-analytics-contributor"></a>Přispěvatel Log Analytics
Log Analytics Přispěvatel můžete načíst všechna data monitorování a upravit nastavení monitorování. Úprava nastavení monitorování, zahrnuje přidání rozšíření virtuálního počítače pro virtuální počítače; čtení klíče účtu úložiště, abyste mohli nakonfigurovat shromažďování protokolů ze služby Azure Storage; vytváření a konfiguraci účtů Automation; Přidání řešení; a konfigurace Azure diagnostics pro všechny prostředky Azure.

| **Akce** |  |
| --- | --- |
| * / Číst | Přečtěte si prostředky všech typů, s výjimkou tajných klíčů. |
| Microsoft.Automation/automationAccounts/* |  |
| Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Umožňuje vypsat seznam přístupových klíčů pro účty úložiště. |
| Microsoft.Compute/virtualMachines/extensions/* |  |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.Insights/diagnosticSettings/* | Vytvoří, aktualizuje nebo načte nastavení diagnostiky pro Analysis serveru |
| Microsoft.OperationalInsights/* |  |
| Microsoft.OperationsManagement/* |  |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
| Microsoft.Storage/storageAccounts/listKeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="log-analytics-reader"></a>Čtenář Log Analytics
Čtenář Log Analytics si může zobrazit a vyhledat všechna data monitorování a jeho nastavení. Může si zobrazit konfiguraci diagnostiky Azure na všech prostředcích Azure.

| **Akce** |  |
| --- | --- |
| * / Číst | Přečtěte si prostředky všech typů, s výjimkou tajných klíčů. |
| Microsoft.OperationalInsights/workspaces/analytics/query/action | Vyhledávání pomocí nový modul. |
| Microsoft.OperationalInsights/workspaces/search/action | Provede vyhledávací dotaz. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

| **NotActions** |  |
| --- | --- |
| Microsoft.OperationalInsights/workspaces/sharedKeys/read | Načte se sdílené klíče pro pracovní prostor. Tyto klíče se používají k připojení agentů statistice provozu Microsoft do pracovního prostoru. |

## <a name="logic-app-contributor"></a>Přispěvatel aplikace logiky
Umožňuje správu aplikace logiky, ale ne přístup k ní.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Umožňuje vypsat seznam přístupových klíčů pro účty úložiště. |
| Microsoft.ClassicStorage/storageAccounts/read | Umožňuje načíst účet úložiště se zadaným účtem. |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.Insights/diagnosticSettings/* | Vytvoří, aktualizuje nebo načte nastavení diagnostiky pro Analysis serveru |
| Microsoft.Insights/logdefinitions/* | Tato oprávnění jsou nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivity prostřednictvím portálu. Seznam kategorií protokolu v protokolu aktivit. |
| Microsoft.Insights/metricDefinitions/* | Číst definice metrik (seznamu dostupných typů metriky pro prostředek). |
| Microsoft.Logic/* | Spravuje prostředky Logic Apps. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/operationresults/read | Načte výsledky operací s předplatným. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Storage/storageAccounts/listkeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
| Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
| Microsoft.Web/connectionGateways/* | Vytvořit a spravovat připojení brány. |
| Microsoft.Web/connections/* | Vytvořit a spravovat připojení. |
| Microsoft.Web/customApis/* | Vytváří a spravuje vlastní API. |
| Microsoft.Web/serverFarms/join/action |  |
| Microsoft.Web/serverFarms/read | Umožňuje získat vlastnosti na plán služby App Service |
| Microsoft.Web/sites/functions/listSecrets/action | Funkce, webové aplikace seznamu tajných klíčů. |

## <a name="logic-app-operator"></a>Operátor aplikace logiky
Umožňuje číst, povolovat a zakazovat aplikaci logiky.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Insights/alertRules/*/read | Pravidla výstrah statistiky pro čtení |
| Microsoft.Insights/diagnosticSettings/*/read | Získá nastavení diagnostiky pro Logic Apps |
| Microsoft.Insights/metricDefinitions/*/read | Načte dostupné metriky pro Logic Apps. |
| Microsoft.Logic/*/read | Čte prostředky Logic Apps. |
| Microsoft.Logic/workflows/disable/action | Zakáže pracovní postup. |
| Microsoft.Logic/workflows/enable/action | Povolí pracovní postup. |
| Microsoft.Logic/workflows/validate/action | Ověří pracovní postup. |
| Microsoft.Resources/deployments/operations/read | Načte nebo vypíše operace nasazení. |
| Microsoft.Resources/subscriptions/operationresults/read | Načte výsledky operací s předplatným. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
| Microsoft.Web/connectionGateways/*/read | Číst připojení brány. |
| Microsoft.Web/connections/*/read | Číst připojení. |
| Microsoft.Web/customApis/*/read | Vlastní rozhraní API pro čtení. |
| Microsoft.Web/serverFarms/read | Umožňuje získat vlastnosti na plán služby App Service |

## <a name="managed-identity-contributor"></a>Spravované Identity přispěvatele
Vytvářet, číst, aktualizovat a odstraňovat přiřazeného Identity uživatele

| **Akce** |  |
| --- | --- |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="managed-identity-operator"></a>Operátor spravované Identity
Přečtěte si a přiřadit přiřazeného Identity uživatele

| **Akce** |  |
| --- | --- |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="monitoring-contributor"></a>Přispěvatel monitorování
Můžete načíst všechna data monitorování a upravit nastavení monitorování. Viz také [začít pracovat s rolí, oprávnění a zabezpečení pomocí Azure monitorování](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Akce** |  |
| --- | --- |
| * / Číst | Přečtěte si prostředky všech typů, s výjimkou tajných klíčů. |
| Microsoft.AlertsManagement/alerts/* |  |
| Microsoft.AlertsManagement/alertsSummary/* |  |
| Microsoft.Insights/AlertRules/* | Pravidla výstrah pro čtení, zápisu a odstranění. |
| Microsoft.Insights/components/* | Součásti Application Insights pro čtení, zápisu a odstranění. |
| Microsoft.Insights/DiagnosticSettings/* | Nastavení diagnostiky pro čtení, zápisu a odstranění. |
| Microsoft.Insights/eventtypes/* | Zobrazí seznam aktivity protokolu události (události management) v předplatném. Toto oprávnění se vztahuje na portálu i programový přístup k protokolu aktivit. |
| Microsoft.Insights/LogDefinitions/* | Tato oprávnění jsou nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivity prostřednictvím portálu. Seznam kategorií protokolu v protokolu aktivit. |
| Microsoft.Insights/MetricDefinitions/* | Číst definice metrik (seznamu dostupných typů metriky pro prostředek). |
| Microsoft.Insights/Metrics/* | Číst metriky pro prostředek. |
| Microsoft.Insights/Register/Action | Registrovat poskytovatele platformy Microsoft Insights |
| Microsoft.Insights/webtests/* | Application Insights pro čtení, zápisu a odstranění webové testy. |
| Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Balíčky řešení analýzy protokolů pro čtení, zápisu a odstranění. |
| Microsoft.OperationalInsights/workspaces/savedSearches/* | Analýzy protokolů pro čtení, zápisu a odstraňování uložená hledání. |
| Microsoft.OperationalInsights/workspaces/search/action | Provede vyhledávací dotaz. |
| Microsoft.OperationalInsights/workspaces/sharedKeys/action | Načte se sdílené klíče pro pracovní prostor. Tyto klíče se používají k připojení agentů statistice provozu Microsoft do pracovního prostoru. |
| Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Konfigurace přehledy úložiště analýzy protokolů pro čtení, zápisu a odstranění. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
| Microsoft.WorkloadMonitor/workloads/* |  |

## <a name="monitoring-reader"></a>Čtenář monitorování
Může číst veškerá monitorování data (metriky, protokoly, atd.). Viz také [začít pracovat s rolí, oprávnění a zabezpečení pomocí Azure monitorování](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Akce** |  |
| --- | --- |
| * / Číst | Přečtěte si prostředky všech typů, s výjimkou tajných klíčů. |
| Microsoft.OperationalInsights/workspaces/search/action | Provede vyhledávací dotaz. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="network-contributor"></a>Přispěvatel sítě
Umožňuje správu sítí, ale ne přístup k nim.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení role a role přiřazení |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
| Microsoft.Network/* | Vytvoření a Správa sítě |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="new-relic-apm-account-contributor"></a>Přispěvatel účtů New Relic APM
Umožňuje správu účtů a aplikací New Relic Application Performance Management, ale ne přístup k nim.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
| NewRelic.APM/accounts/* |  |

## <a name="redis-cache-contributor"></a>Přispěvatel služby Redis Cache
Umožňuje správu mezipamětí Redis, ale ne přístup k nim.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení role a role přiřazení |
| Microsoft.Cache/redis/* | Vytváření a správě mezipaměti Redis |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="scheduler-job-collections-contributor"></a>Přispěvatel kolekcí úloh Scheduleru
Umožňuje správu kolekcí úloh Scheduleru, ale ne přístup k nim.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení role a role přiřazení |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Scheduler/jobcollections/* | Vytvoření a správa kolekcí úloh |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="search-service-contributor"></a>Přispěvatel služby Search
Umožňuje správu služeb Search, ale ne přístup k nim.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení role a role přiřazení |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Search/searchServices/* | Vytvoření a Správa služby vyhledávání |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="security-admin"></a>Správce zabezpečení
V Centru zabezpečení pouze: můžete zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, upravit zásady zabezpečení, zobrazení výstrah a doporučení, zavření výstrahy a doporučení

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Authorization/policyAssignments/* | Vytvoření a správa přiřazení zásad. |
| Microsoft.Authorization/policyDefinitions/* | Vytvářet a spravovat definice zásady |
| Microsoft.Authorization/policySetDefinitions/* | Vytvoření a Správa zásad skupiny |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
| Microsoft.operationalInsights/workspaces/*/read | Zobrazit data analýzy protokolů |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Security/*/read | Čtení bezpečnostních komponent a zásad |
| Microsoft.Security/locations/alerts/dismiss/action | Zamítnutí výstrahy zabezpečení |
| Microsoft.Security/locations/tasks/dismiss/action | Zavření doporučení zabezpečení |
| Microsoft.Security/policies/write | Aktualizace nastavení zásad zabezpečení |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="security-manager"></a>Správce zabezpečení
Umožňuje správu součástí zabezpečení, zásad zabezpečení a virtuálních počítačů.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.ClassicCompute/*/read | Přečtěte si informace o konfiguraci klasické virtuální počítače |
| Microsoft.ClassicCompute/virtualMachines/*/write | Zápis konfigurace pro klasické virtuální počítače |
| Microsoft.ClassicNetwork/*/read | Přečtěte si informace o konfiguraci o classic sítě |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Security/* | Vytvořit a spravovat zabezpečení komponenty a zásady |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="security-reader"></a>Čtecí zařízení pro zabezpečení
V Centru zabezpečení pouze: můžete zobrazit doporučení a výstrahy, zobrazení zásady zabezpečení, zobrazit stavy zabezpečení, ale nelze provádět změny

| **Akce** |  |
| --- | --- |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.operationalInsights/workspaces/*/read | Zobrazit data analýzy protokolů |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Security/*/read | Čtení bezpečnostních komponent a zásad |

## <a name="site-recovery-contributor"></a>Přispěvatel Site Recovery
Umožňuje spravovat službu Site Recovery, ale ne vytvářet trezory a přiřazovat role.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
| Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp je interní operace, kterou používá služba |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp je interní operace, které používá služba |
| Microsoft.RecoveryServices/Vaults/certificates/write | Operace aktualizace prostředek certifikátu aktualizuje prostředek nebo trezoru certifikát přihlašovacích údajů. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Vytváření a správě rozšířené informace související s trezoru |
| Microsoft.RecoveryServices/Vaults/read | Operace získání trezoru získá objekt, který reprezentuje prostředků Azure typu 'trezoru. |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Vytvářet a spravovat registrovaná identit |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Vytvořit nebo aktualizovat nastavení replikace výstrahy |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Číst všechny události |
| Microsoft.RecoveryServices/vaults/replicationFabrics/* | Vytvoření a Správa prostředků infrastruktury replikace |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Vytvářet a spravovat úlohy replikace |
| Microsoft.RecoveryServices/vaults/replicationPolicies/* | Vytvoření a správě zásad replikace |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Vytváření a správě plánů obnovení |
| Microsoft.RecoveryServices/Vaults/storageConfig/* | Vytvoření a správě konfigurace úložiště z trezoru služeb zotavení |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Vrátí token informace o trezoru služeb zotavení. |
| Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti využití trezoru Recovery Services. |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | Trezor tokenu operaci lze použít k získání tokenu trezoru pro operace trezoru úrovně back-end. |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Načtení výstrah pro trezor služeb zotavení |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/čtení |  |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="site-recovery-operator"></a>Operátor Site Recovery
Umožňuje provádět převzetí služeb při selhání a navrácení služeb po obnovení, ale žádné jiné operace správy služby Site Recovery.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
| Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp je interní operace, kterou používá služba |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp je interní operace, které používá služba |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
| Microsoft.RecoveryServices/Vaults/read | Operace získání trezoru získá objekt, který reprezentuje prostředků Azure typu 'trezoru. |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Výsledky operace Get, lze operace získat stav operace a výsledek asynchronně odeslaná operaci |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Získat kontejnerů, které můžete použít operaci získat kontejnery zaregistrovat pro prostředek. |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Číst všechny nastavení výstrah |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Číst všechny události |
| Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Zkontroluje konzistenci prostředí infrastruktury. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Číst všechny prostředky infrastruktury |
| Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Přidružení brány |
| Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Obnovit certifikát pro prostředky infrastruktury |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Přečtěte si žádné sítě. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/čtení | Číst veškerá jeho mapování sítě |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/čtení | Číst všechny kontejnery ochrany |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/čtení | Číst všechny položky, které jsou předmětem ochrany |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint nebo akce | Použít bod obnovení |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/failoverCommit nebo akce | Potvrzení převzetí služeb při selhání |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/plannedFailover nebo akce | Plánované převzetí služeb při selhání |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/čtení | Číst všechny chráněné položky |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/čtení | Číst všechny body obnovení replikace |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/repairReplication nebo akce | Oprava replikace |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems nebo opětovné ochrany nebo akce | Znovu aktivujte ochranu chráněné položky |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailover nebo akce | Testovací převzetí služeb při selhání |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup nebo akce | Vyčistit testovací převzetí služeb při selhání |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/unplannedFailover nebo akce | Převzetí služeb při selhání |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/updateMobilityService nebo akce | Aktualizace služby Mobility |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/čtení | Číst veškerá jeho mapování kontejnerů ochrany |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/čtení | Přečtěte si zprostředkovatelů služby obnovení |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/refreshProvider nebo akce | Aktualizujte zprostředkovatele |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/čtení | Číst všechny klasifikace úložiště |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/čtení | Číst veškerá jeho mapování klasifikace úložiště |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Číst všechny úlohy |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Vytvářet a spravovat úlohy replikace |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Číst všechny zásady |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plán obnovení potvrzení převzetí služeb při selhání |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plán obnovení plánované převzetí služeb při selhání |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Číst všechny plány obnovení |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Znovu nastavte ochranu plánu obnovení |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Testovací převzetí služeb při selhání obnovení plán |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plán obnovení testovací převzetí služeb při selhání čištění |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plán obnovení převzetí služeb při selhání |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Načtení výstrah pro trezor služeb zotavení |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/čtení |  |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Vrátí token informace o trezoru služeb zotavení. |
| Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti využití trezoru Recovery Services. |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | Trezor tokenu operaci lze použít k získání tokenu trezoru pro operace trezoru úrovně back-end. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="site-recovery-reader"></a>Čtenář Site Recovery
Umožňuje zobrazovat stav služby Site Recovery, ale ne provádět jiné operace správy.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp je interní operace, kterou používá služba |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operace Získat rozšířené informace získá rozšířené informace o objektu, který představuje prostředek Azure typu trezor. |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Získá výstrahy pro trezor služeb zotavení. |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/čtení |  |
| Microsoft.RecoveryServices/Vaults/read | Operace získání trezoru získá objekt, který reprezentuje prostředků Azure typu 'trezoru. |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Výsledky operace Get, lze operace získat stav operace a výsledek asynchronně odeslaná operaci |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Získat kontejnerů, které můžete použít operaci získat kontejnery zaregistrovat pro prostředek. |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Číst všechny nastavení výstrah |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Číst všechny události |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Číst všechny prostředky infrastruktury |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Přečtěte si žádné sítě. |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/čtení | Číst veškerá jeho mapování sítě |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/čtení | Číst všechny kontejnery ochrany |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/čtení | Číst všechny položky, které jsou předmětem ochrany |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/čtení | Číst všechny chráněné položky |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/čtení | Číst všechny body obnovení replikace |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/čtení | Číst veškerá jeho mapování kontejnerů ochrany |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/čtení | Přečtěte si zprostředkovatelů služby obnovení |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/čtení | Číst všechny klasifikace úložiště |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/čtení | Číst veškerá jeho mapování klasifikace úložiště |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Číst všechny úlohy |
| Microsoft.RecoveryServices/vaults/replicationJobs/read | Číst všechny úlohy |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Číst všechny zásady |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Číst všechny plány obnovení |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Vrátí token informace o trezoru služeb zotavení. |
| Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti využití trezoru Recovery Services. |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | Trezor tokenu operaci lze použít k získání tokenu trezoru pro operace trezoru úrovně back-end. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="sql-db-contributor"></a>Přispěvatel databází SQL
Umožňuje spravovat databáze SQL, ale ne přístup k nim. Navíc se nedají spravovat jejich zásady zabezpečení nebo jejich nadřazené servery SQL.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení role a role přiřazení |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/databases/* | Vytvářet a spravovat databáze SQL |
| Microsoft.Sql/servers/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Nelze upravit zásady auditu |
| Microsoft.Sql/servers/databases/auditingSettings/* | Nelze upravit nastavení auditu |
| Microsoft.Sql/servers/databases/auditRecords/read | Načtení záznamů auditu databáze objektů blob |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Nelze upravit zásady připojení |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Nelze upravit zásady maskování dat. |
| Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Nelze upravovat zásady, výstrah zabezpečení |
| Microsoft.Sql/servers/databases/securityMetrics/* | Nelze upravit zabezpečení metriky |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |

## <a name="sql-security-manager"></a>Správce zabezpečení SQL
Umožňuje vám spravovat zásady vztahující se k zabezpečení serverů a databází SQL, ale ne přístup k nim.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorizace Microsoft pro čtení |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Spojí prostředků, jako je například účet úložiště nebo SQL database k podsíti. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Sql/servers/auditingPolicies/* | Vytvořit a spravovat zásady auditování SQL serveru |
| Microsoft.Sql/servers/auditingSettings/* | Vytvářet a spravovat nastavení auditování serveru SQL |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Vytvářet a spravovat zásady auditu databáze serveru SQL |
| Microsoft.Sql/servers/databases/auditingSettings/* | Vytvářet a spravovat nastavení auditování databáze SQL serveru |
| Microsoft.Sql/servers/databases/auditRecords/read | Záznamy auditu pro čtení |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Vytvářet a spravovat zásady připojení databáze serveru SQL |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Vytvářet a spravovat zásady maskování dat databáze serveru SQL |
| Microsoft.Sql/servers/databases/read | Vrátí seznam databází nebo získá vlastnosti pro určenou databázi. |
| Microsoft.Sql/servers/databases/schemas/read | Načíst seznam schémat databáze |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Načíst seznam sloupců tabulky. |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/read | Načtení seznamu tabulek databáze |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Vytvoření a Správa výstrah zásady zabezpečení serveru SQL server databáze |
| Microsoft.Sql/servers/databases/securityMetrics/* | Vytvoření a správa metriky zabezpečení databáze serveru SQL |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/firewallRules/* |  |
| Microsoft.Sql/servers/read | Vrátí seznam serverů nebo získá vlastnosti pro zadaný server. |
| Microsoft.Sql/servers/securityAlertPolicies/* | Vytvoření a Správa výstrah zásady zabezpečení SQL serveru |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="sql-server-contributor"></a>Přispěvatel SQL Serveru
Umožňuje vám spravovat servery a databáze SQL, ale ne přístup k nim ani jejich zásady vztahující se k zabezpečení.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/* | Vytvoření a správa serverů SQL |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/auditingPolicies/* | Nelze upravit zásady auditu SQL serveru |
| Microsoft.Sql/servers/auditingSettings/* | Nastavení auditování serveru SQL server nelze upravit. |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Nelze upravit zásady auditu databáze serveru SQL |
| Microsoft.Sql/servers/databases/auditingSettings/* | Nelze upravit nastavení auditování databáze SQL serveru |
| Microsoft.Sql/servers/databases/auditRecords/read | Nelze přečíst záznamů auditu |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Nelze upravit zásady připojení databáze serveru SQL |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Nelze upravit maskování zásady data v databázi SQL serveru |
| Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Nelze upravit výstrahy zásady zabezpečení serveru SQL server databáze |
| Microsoft.Sql/servers/databases/securityMetrics/* | Nelze upravit metriky zabezpečení databáze serveru SQL |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/securityAlertPolicies/* | Nelze upravit výstrahy zásady zabezpečení SQL serveru |

## <a name="storage-account-contributor"></a>Přispěvatel účtů úložiště
Umožňuje správu účtů úložiště, ale ne přístup k nim.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Číst všechny autorizace |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.Insights/diagnosticSettings/* | Spravovat nastavení pro diagnostiku |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Spojí prostředků, jako je například účet úložiště nebo SQL database k podsíti. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Storage/storageAccounts/* | Vytváření a správě účtů úložiště |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="storage-account-key-operator-service-role"></a>Role služby Operátor klíčů účtů úložiště
Operátoři klíčů účtů úložiště můžou vypisovat a znovu generovat klíče pro účty úložiště.

| **Akce** |  |
| --- | --- |
| Microsoft.Storage/storageAccounts/listkeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
| Microsoft.Storage/storageAccounts/regeneratekey/action | Obnoví přístupové klíče pro zadaný účet úložiště. |

## <a name="support-request-contributor"></a>Přispěvatel žádostí o podporu
Umožňuje vytvořit a spravovat žádosti o podporu.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorizace pro čtení |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="traffic-manager-contributor"></a>Přispěvatel Traffic Manageru
Umožňuje spravovat profily Traffic Manageru, ale neumožňuje řídit, kdo k nim má přístup.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Čtení rolí a přiřazení rolí |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.Network/trafficManagerProfiles/* |  |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="user-access-administrator"></a>Správce přístupu uživatelů
Umožňuje správu přístupu uživatelů k prostředkům Azure.

| **Akce** |  |
| --- | --- |
| * / Číst | Přečtěte si prostředky všech typů, s výjimkou tajných klíčů. |
| Microsoft.Authorization/* | Spravovat autorizace |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="virtual-machine-administrator-login"></a>Přihlášení správce virtuálních počítačů
-   Uživatelé s touto rolí mají možnost přihlášení k virtuálnímu počítači pomocí Správce služby Windows nebo Linux oprávnění uživatele root.

| **Akce** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/loginAsAdmin/action |  |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/loginAsAdmin/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="virtual-machine-contributor"></a>Přispěvatel virtuálních počítačů
Umožňuje spravovat virtuální počítače, ale ne přístup k nim a ne virtuální sítě nebo účet úložiště, který jste připojení k.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorizace pro čtení |
| Microsoft.Compute/availabilitySets/* | Vytvářet a spravovat skupiny dostupnosti výpočetní |
| Microsoft.Compute/locations/* | Vytváření a správě výpočetních umístění |
| Microsoft.Compute/virtualMachines/* | Vytvářet a spravovat virtuální počítače |
| Microsoft.Compute/virtualMachineScaleSets/* | Vytvářet a spravovat sady škálování virtuálního počítače |
| Microsoft.DevTestLab/schedules/* |  |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Spojí fond adres back-end brány aplikace |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Spojí fond back-end adresy Vyrovnávání zatížení. |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Spojí Vyrovnávání zatížení fond příchozích pravidel nat |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Spojí pravidlo příchozí nat nástroje pro vyrovnávání zatížení. |
| Microsoft.Network/loadBalancers/probes/join/action | Umožňuje použití sondy nástroje pro vyrovnávání zatížení. S touto vlastností healthProbe oprávnění měřítka virtuálních počítačů sady můžete odkazovat, sonda. |
| Microsoft.Network/loadBalancers/read | Získá definici nástroje pro vyrovnávání zatížení. |
| Microsoft.Network/locations/* | Vytvářet a spravovat síťová umístění |
| Microsoft.Network/networkInterfaces/* | Vytváření a správě síťových rozhraní |
| Microsoft.Network/networkSecurityGroups/join/action | Spojí skupinu zabezpečení sítě |
| Microsoft.Network/networkSecurityGroups/read | Získá definici skupiny zabezpečení sítě |
| Microsoft.Network/publicIPAddresses/join/action | Spojí veřejnou ip adresu. |
| Microsoft.Network/publicIPAddresses/read | Získá definici veřejné ip adresy. |
| Microsoft.Network/virtualNetworks/read | Získat definici virtuální sítě |
| Microsoft.Network/virtualNetworks/subnets/join/action | Připojí virtuální sítě |
| Microsoft.RecoveryServices/locations/* |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems / * / číst |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/čtení | Vrátí podrobnosti o objektu chráněné položky |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems a zápis | Vytvoření zálohy chráněné položky |
| Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Vytvoření zálohy záměr ochrany |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Vrátí všechny zásady ochrany |
| Microsoft.RecoveryServices/Vaults/backupPolicies/write | Vytvoří zásadu ochrany |
| Microsoft.RecoveryServices/Vaults/read | Operace získání trezoru získá objekt, který reprezentuje prostředků Azure typu 'trezoru. |
| Microsoft.RecoveryServices/Vaults/usages/read | Vrátí podrobnosti využití trezoru Recovery Services. |
| Microsoft.RecoveryServices/Vaults/write | Operace Vytvořit trezor vytvoří prostředek Azure typu trezor. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Storage/storageAccounts/listKeys/action | Vrátí přístupové klíče pro zadaný účet úložiště. |
| Microsoft.Storage/storageAccounts/read | Vrátí seznam účtů úložišť nebo načte vlastnosti zadaného účtu. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |

## <a name="virtual-machine-user-login"></a>Přihlášení uživatele virtuálního počítače
Uživatelé s touto rolí mají možnost přihlášení k virtuálnímu počítači jako běžný uživatel.

| **Akce** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="web-plan-contributor"></a>Přispěvatel webových plánů
Umožňuje správu webových plánů pro weby, ale ne přístup k nim.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorizace pro čtení |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
| Microsoft.Web/serverFarms/* | Vytvoření a správa serverové farmy |

## <a name="website-contributor"></a>Přispěvatel webů
Umožňuje správu webů (ne webových plánů), ale ne přístup k nim.

| **Akce** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Autorizace pro čtení |
| Microsoft.Insights/alertRules/* | Vytvářet a spravovat pravidla výstrah statistiky |
| Microsoft.Insights/components/* | Vytvoření a správa přehled komponenty |
| Microsoft.ResourceHealth/availabilityStatuses/read | Získá stavy dostupnosti pro všechny prostředky v zadaném rozsahu. |
| Microsoft.Resources/deployments/* | Vytvářet a spravovat nasazení skupiny prostředků |
| Microsoft.Resources/subscriptions/resourceGroups/read | Načte nebo vypíše skupinu prostředků. |
| Microsoft.Support/* | Vytvářet a spravovat lístky žádostí o podporu |
| Microsoft.Web/certificates/* | Vytvářet a spravovat certifikáty webu |
| Microsoft.Web/listSitesAssignedToHostName/read | Získáte názvy lokalit, které jsou přiřazeny k hostitele. |
| Microsoft.Web/serverFarms/join/action |  |
| Microsoft.Web/serverFarms/read | Umožňuje získat vlastnosti na plán služby App Service |
| Microsoft.Web/sites/* | Vytvoření a správa webů (vytváření webu taky vyžaduje oprávnění k zápisu do přidružené plán služby App Service) |

## <a name="see-also"></a>Další informace najdete v tématech
* [Řízení přístupu na základě role](role-assignments-portal.md): Začínáme s RBAC na portálu Azure.
* [Vlastní role v Azure RBAC](custom-roles.md): Naučte se vytvářet vlastní role podle vašich potřeb přístup.
* [Vytvoření sestavy historie změn přístupu](change-history-report.md): udržování přehledu o změně přiřazení rolí v RBAC.
* [Na základě rolí řešení potíží s řízení přístupu](troubleshooting.md): umožňuje získat návrhy pro řešení běžných problémů.
* [Oprávnění v Azure Security Center](../security-center/security-center-permissions.md)
