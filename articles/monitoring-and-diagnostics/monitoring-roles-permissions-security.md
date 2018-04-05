---
title: Začínáme s rolemi, oprávnění a zabezpečení pomocí Azure monitorování | Microsoft Docs
description: Naučte se používat Azure monitorování integrovaných rolí a oprávnění k omezení přístupu k monitorování prostředků.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 2686e53b-72f0-4312-bcd3-3dc1b4a9b912
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: johnkem
ms.openlocfilehash: 81f083b799e359f69605de22c30d3adc4480e44b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Začínáme s rolemi, oprávnění a zabezpečení pomocí Azure monitorování
Mnoha týmy musí striktně regulovat přístup k monitorování data a nastavení. Například pokud máte členové týmu, kteří pracují výhradně na monitorování (pracovníci technické podpory, technici devops) nebo pokud používáte poskytovatel spravované služby, můžete jim udělit přístup k datům monitorování pouze při omezení jejich schopnost vytvářet, upravovat, nebo Odstraňte prostředky. Tento článek ukazuje, jak rychle použít předdefinovaná role RBAC monitorování na uživatele v Azure nebo vytvořit vlastní vlastní role pro uživatele, který potřebuje monitorování omezenými oprávněními. Potom popisuje aspekty zabezpečení vašich prostředků související s monitorování Azure a jak můžete omezit přístup k datům, která obsahují.

## <a name="built-in-monitoring-roles"></a>Vestavěné role, které monitorování
Předdefinované role Azure monitorování jsou navržené tak, abyste omezit přístup k prostředkům v předplatném ale kontrolní infrastrukturu pro získání a konfiguraci údaje, které potřebují. Monitorování Azure poskytuje dvě role se na pole: A monitorování Čtenář a Přispěvatel monitorování.

### <a name="monitoring-reader"></a>Čtenář monitorování
Lidé přiřadit role Čtenář monitorování můžete zobrazit všechna data monitorování v předplatném, ale nelze upravit žádný prostředek nebo upravit nastavení související s monitorování prostředků. Tato role je vhodný pro uživatele v organizaci, například podporu nebo operations technici, kteří musí být schopni:

* Zobrazit řídicí panely monitorování na portálu a vytvořit vlastní privátního sledování řídicí panely.
* Zobrazení výstrah pravidla definovaná v [výstrahách Azure](monitoring-overview-unified-alerts.md)
* Dotaz pro používání metriky [REST API služby Azure monitorování](https://msdn.microsoft.com/library/azure/dn931930.aspx), [rutiny prostředí PowerShell](insights-powershell-samples.md), nebo [rozhraní příkazového řádku a platformy](insights-cli-samples.md).
* Dotaz protokolu aktivit pomocí portálu, rozhraní REST API Azure monitorování, rutiny prostředí PowerShell nebo rozhraní příkazového řádku a platformy.
* Zobrazení [nastavení pro diagnostiku](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) pro prostředek.
* Zobrazení [protokolu profil](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile) pro předplatné.
* Zobrazit nastavení automatického škálování.
* Zobrazení výstrah aktivity a nastavení.
* Přístup k datům Application Insights a zobrazení dat v AI Analytics.
* Vyhledávání dat pracovního prostoru analýzy protokolů, včetně data o využití pro pracovní prostor.
* Zobrazení skupiny pro správu analýzy protokolů.
* Načtěte schéma analýzy protokolů hledání.
* Zobrazí seznam analýzy protokolů intelligence Pack.
* Načtení a provedení analýzy protokolů uložená hledání.
* Načtěte konfiguraci úložiště analýzy protokolů.

> [!NOTE]
> Tato role není poskytnuta přístup pro čtení na data protokolu, která byla streamování do centra událostí nebo uložený v účtu úložiště. [Viz níže](#security-considerations-for-monitoring-data) informace o konfiguraci přístup k těmto prostředkům.
> 
> 

### <a name="monitoring-contributor"></a>Přispěvatel monitorování
Uživatelé přiřazení role přispěvatele monitorování můžete zobrazit všechna data monitorování v předplatném a vytvořte nebo upravte nastavení monitorování, ale nelze změnit žádné další prostředky. Tato role je nadmnožinou role Čtenář monitorování a je vhodný pro členy týmu monitorování nebo poskytovatele spravované služby, kteří kromě výše uvedeného oprávnění také musí být schopni organizace:

* Publikujte jako sdílené řídicího panelu monitorování řídicí panely.
* Nastavit [nastavení pro diagnostiku](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) pro resource.*
* Nastavte [protokolu profil](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile) pro subscription.*
* Nastavit pravidla výstrah aktivity a nastavení prostřednictvím [Azure výstrahy](monitoring-overview-unified-alerts.md).
* Vytvoření služby Application Insights webové testy a součásti.
* Pracovní prostor analýzy protokolů seznamu sdílených klíčů.
* Povolit nebo zakázat analýzy protokolů intelligence Pack.
* Vytvoření a odstranit a provedení analýzy protokolů uložená hledání.
* Vytvořte a odstranit konfiguraci úložiště analýzy protokolů.

* uživatele musí být taky samostatně přidělují ListKeys oprávnění k cílovému prostředku (úložiště účet nebo události rozbočovače obor názvů) k nastavení profilu protokolu nebo nastavení diagnostiky.

> [!NOTE]
> Tato role není poskytnuta přístup pro čtení na data protokolu, která byla streamování do centra událostí nebo uložený v účtu úložiště. [Viz níže](#security-considerations-for-monitoring-data) informace o konfiguraci přístup k těmto prostředkům.
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Monitorování oprávnění a vlastní role RBAC
Pokud uvedené výše uvedené vestavěné role neodpovídají přesný potřebám vašeho týmu, můžete [vytvořit vlastní role RBAC](../active-directory/role-based-access-control-custom-roles.md) s podrobnější oprávnění. Níže jsou uvedeny běžné operace Azure RBAC monitorování s jejich popisy.

| Operace | Popis |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, zápisu, odstraní] |Akce pro čtení, zápisu a odstraňování skupin. |
| Microsoft.Insights/ActivityLogAlerts/[Read, Write, Delete] |Aktivity čtení, zápisu a odstraňování protokolu výstrahy. |
| Microsoft.Insights/AlertRules/[Read, Write, Delete] |Pravidla výstrah pro čtení, zápisu a odstranění (z výstrah classic). |
| Microsoft.Insights/AlertRules/Incidents/Read |Seznam incidentů (historie pravidlo výstrahy se aktivuje) pro pravidla výstrah. Týká se pouze na portálu. |
| Microsoft.Insights/AutoscaleSettings/[Read, Write, Delete] |Nastavení automatického škálování pro čtení, zápisu a odstranění. |
| Microsoft.Insights/DiagnosticSettings/[Read, zápisu, odstraní] |Nastavení diagnostiky pro čtení, zápisu a odstranění. |
| Microsoft.Insights/EventCategories/Read |Výčet všech kategorií možné v protokolu aktivit. Použít na portálu Azure. |
| Microsoft.Insights/eventtypes/digestevents/Read |Tato oprávnění jsou nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivity prostřednictvím portálu. |
| Microsoft.Insights/eventtypes/values/Read |Zobrazí seznam aktivity protokolu události (události management) v předplatném. Toto oprávnění se vztahuje na portálu i programový přístup k protokolu aktivit. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Read, zápisu, odstraní] | Nastavení diagnostiky pro čtení, zápisu a odstranění pro tok protokoly sítě. |
| Microsoft.Insights/LogDefinitions/Read |Tato oprávnění jsou nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivity prostřednictvím portálu. |
| Microsoft.Insights/LogProfiles/[Read, Write, Delete] |Profily pro čtení, zápisu a odstraňování protokolu (streamování aktivity protokolu události rozbočovače nebo úložiště účtu). |
| Microsoft.Insights/MetricAlerts/[Read, Write, Delete] |Čtení, zápisu a odstraňování téměř v reálném čase metriky výstrahy |
| Microsoft.Insights/MetricDefinitions/Read |Číst definice metrik (seznamu dostupných typů metriky pro prostředek). |
| Microsoft.Insights/Metrics/Read |Číst metriky pro prostředek. |
| Microsoft.Insights/Register/Action |Zaregistrujte zprostředkovatele prostředků Azure monitorování. |
| Microsoft.Insights/ScheduledQueryRules/[Read, Write, Delete] |Výstrahy pro čtení, zápisu a odstraňování protokolu pro službu Application Insights. |



> [!NOTE]
> Přístup k výstrahy, nastavení pro diagnostiku a metriky pro prostředek vyžaduje, že uživatel má přístup pro čtení k prostředku typu a rozsahu prostředku. Diagnostické nastavení nebo protokolu profil, který archivuje k účtu úložiště nebo datových proudů do centra událostí vytváření ("zápis") vyžaduje, aby uživatel také ListKeys oprávnění k cílovému prostředku.
> 
> 

Například pomocí výše uvedené tabulce můžete vytvořit vlastní role RBAC pro "Aktivity protokolu čtečku" takto:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzureRmRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Důležité informace o zabezpečení pro monitorování dat.
Data monitorování – zvlášť soubory protokolu – mohou obsahovat citlivé údaje, jako je například IP adresy nebo uživatelské jméno. Monitorování dat z Azure dodává ve formulářích tři základní:

1. Protokol aktivit, která popisuje všechny akce v rovině řízení na vaše předplatné Azure.
2. Diagnostické protokoly, které jsou protokoly vygenerované prostředkem.
3. Metriky, které jsou vysílaných prostředky.

Všechny tři z těchto typů dat můžete uložený v účtu úložiště nebo prostřednictvím datového proudu do centra událostí, které jsou pro obecné účely prostředků Azure. Protože se jedná pro obecné účely prostředky, vytváření, odstraňování a k nim přistupovat je vyhrazena pro správce privilegované operace. Doporučujeme, abyste zabránili zneužití použít následující postupy pro monitorování související prostředky:

* Použijte účet jeden vyhrazený úložiště pro data monitorování. Pokud potřebujete oddělit data monitorování do více účtů úložiště, nikdy sdílet využití účtu úložiště mezi monitorování a ty, kteří potřebují pouze přístup k datům (například SIEM třetích stran) monitorování může poskytnout nechtěně-monitoring data, jako to přístup k-monitoring data.
* Používejte jednu, vyhrazené sběrnice nebo Centrum událostí názvů ve všech nastavení pro diagnostiku ze stejných důvodů jako výš.
* Omezit přístup k účtům související s monitorování úložiště nebo event hubs tím, že jim skupinu samostatné prostředků a [použít obor](../active-directory/role-based-access-control-what-is.md#basics-of-access-management-in-azure) na monitorování role omezit přístup jenom příslušné skupině prostředků.
* Nikdy udělit oprávnění k ListKeys pro účty úložiště nebo služby event hubs v oboru předplatné, když uživatel potřebuje pouze přístup k datům monitorování. Místo toho přidělit tato oprávnění pro uživatele na prostředek nebo skupina prostředků (Pokud máte vyhrazené monitorování skupiny prostředků) oboru.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Omezení přístupu k účtům úložiště související s monitorování
Pokud uživatele nebo aplikace potřebuje přístup k datům v účtu úložiště, monitorování, měli byste [generovat SAS účtu](https://msdn.microsoft.com/library/azure/mt584140.aspx) na účet úložiště, který obsahuje data sledování úrovně služby jen pro čtení přístup k úložišti objektů blob. V prostředí PowerShell může vypadat například:

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Pak můžete udělit token do entity, musí ke čtení z tohoto úložiště účet a můžete zobrazit seznam a číst ze všech objektů BLOB v daném účtu úložiště.

Případně pokud potřebujete řídit toto oprávnění s RBAC, můžete udělit dané entity Microsoft.Storage/storageAccounts/listkeys/action oprávnění na tuto konkrétní účet úložiště. To je nezbytné pro uživatele, kteří potřebují být schopni nastavit nastavení diagnostiky nebo protokolu profil k archivaci na účet úložiště. Můžete například vytvořit následující vlastní role RBAC pro uživatele nebo aplikace, které stačí ke čtení z jeden účet úložiště:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzureRmRoleDefinition -Role $role 
```

> [!WARNING]
> Oprávnění ListKeys umožňuje uživatelům seznam klíče účtu úložiště primární a sekundární. Tyto klíče udělit všechny podepsané oprávnění (čtení, zápis, vytvořit objekty BLOB, odstranit objekty BLOB, atd.) napříč všemi podepsané služby (objekt blob, fronty, tabulce, soubor) v daném účtu úložiště. Doporučujeme používat SAS účtu popsané výše, pokud je to možné.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Omezení přístupu do centra událostí související s monitorování
Podobný princip platí službou event hubs, ale nejdřív je potřeba vytvořit vyhrazený autorizační pravidlo naslouchání. Pokud chcete udělit přístup k aplikaci, která stačí pro naslouchání na centra událostí související s monitorování, postupujte takto:

1. Vytvořte zásady sdíleného přístupu na události rozbočovače, které byly vytvořeny pro streamování dat monitorování s jenom deklarace identity naslouchání. To lze provést na portálu. Například můžete ho může volat "monitoringReadOnly." Pokud je to možné můžete poskytnout klíči přímo k příjemce a přejděte na další krok.
2. Pokud příjemce musí být možné získat klíče ad-hoc, udělte ListKeys akce pro tuto centra událostí. To je nezbytné pro uživatele, kteří potřebují být schopni nastavit nastavení diagnostiky nebo protokolu profilu do datového proudu do centra událostí. Například může vytvořit pravidlo RBAC:
   
   ```powershell
   $role = Get-AzureRmRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzureRmRoleDefinition -Role $role 
   ```

## <a name="next-steps"></a>Další postup
* [Přečtěte si o RBAC a oprávnění ve službě Správce prostředků](../active-directory/role-based-access-control-what-is.md)
* [Naleznete v přehledu monitorování v Azure](monitoring-overview.md)

