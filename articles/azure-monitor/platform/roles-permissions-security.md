---
title: Začínáme s rolemi, oprávnění a zabezpečení prostřednictvím služby Azure Monitor
description: Další informace o použití Azure monitoru předdefinované role a oprávnění k omezení přístupu k monitorování prostředků.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: 4ca5803ca410e3250e025eb60b5c1ff9fc7216b1
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54465237"
---
# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Začínáme s rolemi, oprávnění a zabezpečení prostřednictvím služby Azure Monitor
Mnoho týmů potřebuje pro výhradně regulovat přístup k monitorování data a nastavení. Například pokud jste členy týmu, kteří pracují výhradně na monitorování (techniky podpory, technikům devops) nebo pokud používáte poskytovatel spravované služby, můžete jim udělit přístup k datům monitorování pouze při omezení jejich schopnost vytvářet, upravovat, nebo Odstraňte prostředky. Tento článek ukazuje, jak rychle použít předdefinované role RBAC monitorování na uživatele v Azure nebo vytvářet vlastní vlastní role pro uživatele, který potřebuje monitorování omezená oprávnění. Pak popisuje aspekty zabezpečení pro vaše prostředky související s Azure Monitor a jak můžete omezit přístup k datům, které obsahují.

## <a name="built-in-monitoring-roles"></a>Předdefinované role monitorování
Azure Monitor předdefinované role jsou účelem je pomoci omezit přístup k prostředkům v rámci předplatného, ale osoby zodpovědné za monitorování infrastruktury pro získání a konfigurace dat potřebují. Platforma Azure Monitor poskytuje dvě role out-of-the-box: Monitorování Čtenář a Přispěvatel monitorování.

### <a name="monitoring-reader"></a>Čtenář monitorování
Lidem přiřadit role Čtenář monitorování můžete zobrazit všechna data monitorování v rámci předplatného, ale nelze upravit prostředek nebo upravit nastavení související s monitorování prostředků. Tato role je vhodný pro uživatele v organizaci, jako je například podpora nebo operace technici, kteří potřebují mít možnost:

* Zobrazit řídicí panely monitorování na portálu a vytvořit svoje vlastní privátní monitorování řídicí panely.
* Zobrazit upozornění pravidel definovaných v [Azure Alerts](../../azure-monitor/platform/alerts-overview.md)
* Dotaz pro používání metrik [REST API služby Azure Monitor](https://msdn.microsoft.com/library/azure/dn931930.aspx), [rutin prostředí PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md), nebo [– multiplatformního rozhraní příkazového řádku](../../azure-monitor/platform/cli-samples.md).
* Dotaz protokolu aktivit pomocí portálu, rozhraní REST API služby Azure Monitor, rutin Powershellu nebo CLI pro různé platformy.
* Zobrazení [nastavení diagnostiky](../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) pro prostředek.
* Zobrazení [profil protokolu](../../azure-monitor/platform/activity-logs-overview.md#export-the-activity-log-with-a-log-profile) pro odběr.
* Zobrazit nastavení automatického škálování.
* Zobrazit výstrahy aktivitu a nastavení.
* Přístup k datům služby Application Insights a zobrazení dat v AI Analytics.
* Vyhledávání dat pracovního prostoru Log Analytics, včetně data o využití pro pracovní prostor.
* Zobrazení skupin pro správu Log Analytics.
* Načtěte schéma vyhledávání Log Analytics.
* Vypsat sady intelligence Pack Log Analytics.
* Načíst a spustit uložené výsledky hledání Log Analytics.
* Získat konfiguraci úložiště analýzy protokolů.

> [!NOTE]
> Tato role vám není udělena oprávnění ke čtení k data protokolu, která byla Streamovat do centra událostí nebo uložený v účtu úložiště. [Viz níže](#security-considerations-for-monitoring-data) pro informace o tom, jak nakonfigurovat přístup k těmto prostředkům.
> 
> 

### <a name="monitoring-contributor"></a>Přispěvatel monitorování
Uživatelé se přiřadila role Přispěvatel monitorování můžete zobrazit všechna data monitorování v rámci předplatného a vytvoření nebo upravte nastavení monitorování, ale nelze změnit všechny další prostředky. Tato role je nadstavbou jazyka roli Čtenář monitorování a je vhodný pro členy týmu monitorování nebo poskytovatelům spravovaných služeb, kteří kromě výše uvedeného oprávnění také musí být schopni v organizaci:

* Publikujte jako sdílený řídicí panel monitorování řídicí panely.
* Nastavte [nastavení diagnostiky](../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) pro resource.*
* Nastavte [profil protokolu](../../azure-monitor/platform/activity-logs-overview.md#export-the-activity-log-with-a-log-profile) pro subscription.*
* Nastavení prostřednictvím pravidel upozornění aktivity a [Azure Alerts](../../azure-monitor/platform/alerts-overview.md).
* Vytvořte webové testy Application Insights a komponenty.
* Sdílené klíče pracovního prostoru Log Analytics seznamu.
* Povolí nebo zakáže sad intelligence Pack Log Analytics.
* Vytvářet a odstraňovat a spouštět uložené výsledky hledání Log Analytics.
* Vytvořit a odstranit konfiguraci úložiště analýzy protokolů.

* uživatele musí být taky samostatně přidělují oprávnění klíče Listkey na cílový prostředek (úložiště účtu nebo událost obor názvů centra) k nastavení profilu protokolu nebo nastavení diagnostiky.

> [!NOTE]
> Tato role vám není udělena oprávnění ke čtení k data protokolu, která byla Streamovat do centra událostí nebo uložený v účtu úložiště. [Viz níže](#security-considerations-for-monitoring-data) pro informace o tom, jak nakonfigurovat přístup k těmto prostředkům.
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Monitorování oprávnění a vlastní role RBAC
Pokud výše uvedené vestavěné role nevyhovují přesné potřebám vašeho týmu, můžete si [vytvořit vlastní roli RBAC](../../role-based-access-control/custom-roles.md) s odstupňovaných oprávnění. Níže jsou uvedeny běžné operace Azure RBAC monitorování s jejich popisy.

| Operace | Popis |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, zápis, odstranění] |Skupiny akcí pro čtení/zápis/delete. |
| Microsoft.Insights/ActivityLogAlerts/[Read, zápis, odstranění] |Upozornění protokolu aktivit čtení, zápisu a odstranění. |
| Microsoft.Insights/AlertRules/[Read, zápis, odstranění] |Čtení, zápisu a odstranění pravidla výstrah (upozornění classic). |
| Microsoft.Insights/AlertRules/Incidents/Read |Seznam incidentů (historie pravidlo upozornění se aktivuje) pro pravidla upozornění. To platí jenom do portálu. |
| Microsoft.Insights/AutoscaleSettings/[Read, zápis, odstranění] |Nastavení automatického škálování pro čtení/zápis/delete. |
| Microsoft.Insights/DiagnosticSettings/[Read, zápis, odstranění] |Nastavení diagnostiky pro čtení/zápis/delete. |
| Microsoft.Insights/EventCategories/Read |Vytvořit výčet všech kategorií v protokolu aktivit. Používat na webu Azure portal. |
| Microsoft.Insights/eventtypes/digestevents/Read |Toto oprávnění je nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivit na portálu. |
| Microsoft.Insights/eventtypes/values/Read |Vypsat události protokolu aktivit (události správy) v rámci předplatného. Toto oprávnění se vztahuje na programování a portálu přístup k protokolům aktivit. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Read, zápis, odstranění] | Nastavení diagnostiky pro čtení/zápis/delete pro protokoly toku network. |
| Microsoft.Insights/LogDefinitions/Read |Toto oprávnění je nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivit na portálu. |
| Microsoft.Insights/LogProfiles/[Read, zápis, odstranění] |Profily protokolů pro čtení/zápis/delete (streamování protokolu aktivit do event hub nebo účet úložiště). |
| Microsoft.Insights/MetricAlerts/[Read, zápis, odstranění] |Čtení/zápis/delete téměř v reálném čase upozornění na metriku |
| Microsoft.Insights/MetricDefinitions/Read |Přečíst definice metrik (seznam dostupných typů metriky pro prostředek). |
| Microsoft.Insights/Metrics/Read |Číst metriky pro prostředek. |
| Microsoft.Insights/Register/Action |Registrace poskytovatele prostředků Azure monitoru. |
| Microsoft.Insights/ScheduledQueryRules/[Read, zápis, odstranění] |Upozornění pro čtení/zápis/delete protokolu ve službě Azure Monitor. |



> [!NOTE]
> Přístup k oznámení, nastavení diagnostiky a metriky pro prostředek vyžaduje, že uživatel má přístup pro čtení k prostředku typu a rozsahu tohoto prostředku. Diagnostický profil nastavení nebo protokolu, který archivuje do účtu úložiště nebo datových proudů do služby event hubs vytváření ("zápisu") vyžaduje, aby uživatel má také oprávnění klíče Listkey na cílový prostředek.
> 
> 

Například použití výše uvedené tabulky, můžete vytvořit vlastní roli RBAC pro "aktivity protokolů Čtenář, následujícím způsobem:

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
Data monitorování – zejména soubory protokolů, mohou obsahovat citlivé údaje, jako je například IP adresy nebo uživatelské jméno. Monitorování dat z Azure k dispozici ve třech založeného na základních formulářích:

1. Protokol aktivit, které popisuje všechny akce rovina řízení v rámci předplatného Azure.
2. Diagnostické protokoly, které jsou protokoly generované prostředek.
3. Metriky, které jsou prostředky emitovány.

Všechny tři typy dat lze uložit v účtu úložiště nebo Streamovat do centra událostí, které jsou pro obecné účely prostředků Azure. Protože tyto prostředky pro obecné účely, vytváření, odstraňování a přístupu k nim je Privilegovaná operace vyhrazena pro správce. Doporučujeme použít následující postupy související s monitorování prostředků k zabránění zneužití:

* Použijte účet úložiště jediné, vyhrazené pro data monitorování. Pokud potřebujete oddělit data monitorování do více účtů úložiště, nikdy sdílet použití účtu služby storage mezi monitorování a -monitorování dat, jako to může nechtěně dát uživatelům, kteří potřebují jenom přístup k datům (například SIEM třetích stran) monitorování přístup k monitorování jiných data.
* Použití jedné, vyhrazené služby Service Bus nebo Event Hub oboru názvů ve všech nastavení diagnostiky ze stejného důvodu, jak je uvedeno výše.
* Omezit přístup k účtům úložiště související s monitorování nebo služby event hubs tím, že jim do samostatné skupiny prostředků, a [použít obor](../../role-based-access-control/overview.md#scope) na vaše monitorování role pro omezit přístup jenom příslušné skupině prostředků.
* Když se uživatel potřebuje pouze přístup k datům monitorování nikdy udělit oprávnění klíče Listkey pro účty úložiště nebo služba event hubs v oboru předplatného. Místo toho udělit tato oprávnění pro uživatele na prostředek nebo skupinu prostředků (Pokud máte vyhrazené sledování skupiny prostředků) oboru.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Omezení přístupu k účtům úložiště související s monitorování
Když uživatel nebo aplikace potřebuje přístup k datům v účtu úložiště monitorování, měli byste [generovat SAS účtu](https://msdn.microsoft.com/library/azure/mt584140.aspx) v účtu úložiště, který obsahuje data monitorování s přístupem jen pro čtení úrovně služeb do úložiště objektů blob. V prostředí PowerShell to může vypadat:

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Pak můžete udělit token k entitě, musí přečíst z úložiště účtu a můžete seznam a číst ze všech objektů BLOB v tomto účtu úložiště.

Případně pokud potřebujete řídit pomocí RBAC toto oprávnění, můžete udělit dané entitě Microsoft.Storage/storageAccounts/listkeys/action oprávnění v této konkrétní účet úložiště. To je nezbytné pro uživatele, kteří potřebují mít možnost nastavit nastavení diagnostiky nebo profil k archivaci protokolu na účet úložiště. Můžete například vytvořit následující vlastní roli RBAC pro uživatele nebo aplikaci, která potřebuje pouze ke čtení z jednoho účtu úložiště:

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
> Oprávnění klíče Listkey umožňuje uživateli seznam klíčů účtu primárního a sekundárního úložiště. Tyto klíče udělit uživateli všechny podepsané oprávnění (čtení, zápis, vytvořte objekty BLOB, odstranit objekty BLOB, atd.) napříč všemi podepsané služby (objekt blob, fronty, tabulky, souboru) v tomto účtu úložiště. Doporučujeme použít SAS účtu je popsáno výše, pokud je to možné.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Omezení přístupu k související s monitorování event hubs
Platí podobný vzorec s event hubs, ale nejdřív je potřeba vytvořit vyhrazený autorizační pravidlo naslouchání. Pokud budete chtít udělit, přístup k aplikaci, která potřebuje pouze k naslouchání služby související s monitorování event hubs, postupujte takto:

1. Vytvořte zásady sdíleného přístupu na události rozbočovače, které byly vytvořeny pro streamování dat monitorování s jenom deklarace identity naslouchání. To můžete udělat na portálu. Například může říkat "monitoringReadOnly." Pokud je to možné můžete udělit tomuto klíči přímo pro příjemce a přejděte na další krok.
2. Pokud příjemce musí být schopni získat klíče ad-hoc, udělte uživateli klíče Listkey akce pro tohoto centra událostí. To je nezbytné pro uživatele, kteří potřebují mít možnost nastavit nastavení diagnostiky nebo profil protokolu pro streamování do event hubs. Například může vytvořit pravidlo RBAC:
   
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

## <a name="monitoring-within-a-secured-virtual-network"></a>Monitorování v rámci zabezpečené virtuální síti

Azure Monitor potřebuje přístup k prostředkům Azure k poskytování služeb, které povolíte. Pokud chcete monitorovat prostředky Azure při stále je zabezpečení před přístupem k veřejnému Internetu, můžete povolit následující nastavení.

### <a name="secured-storage-accounts"></a>Účty zabezpečené úložiště 

Sledování dat je často zapsána do účtu úložiště. Můžete chtít Ujistěte se, že data kopírovaná do účtu úložiště nemůže být přístup neoprávnění uživatelé. Za účelem zvýšení zabezpečení můžete omezit jenom přístup k síti a Povolit jenom autorizovaným prostředkům a důvěryhodné služby Microsoftu přístup k účtu úložiště tak, že omezíte účet úložiště, který chcete použít "vybraných sítí".
![Dialogové okno nastavení služby Azure Storage](./media/roles-permissions-security/secured-storage-example.png) Azure Monitor se považuje za jednu z těchto "důvěryhodné služby Microsoftu" Pokud povolit důvěryhodným službám Microsoftu přístup k zabezpečené úložiště, Azure monitor budou mít přístup k zabezpečené účet úložiště, povolením zápis do účtu úložiště za těchto podmínek chráněné diagnostické protokoly Azure monitoru, protokolu aktivit a metrik. Díky tomu také Log Analytics pro čtení protokolů ze zabezpečeného úložiště.   


Další informace najdete v tématu [Network security a Azure Storage](../../storage/common/storage-network-security.md)

## <a name="next-steps"></a>Další postup
* [Přečtěte si informace o RBAC a oprávnění v Resource Manageru](../../role-based-access-control/overview.md)
* [Přečíst Přehled monitorování v Azure](../../azure-monitor/overview.md)


