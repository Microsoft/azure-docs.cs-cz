---
title: Role, oprávnění a zabezpečení ve službě Azure Monitor
description: Zjistěte, jak používat předdefinované role a oprávnění Azure Monitoru k omezení přístupu k prostředkům monitorování.
author: johnkemnetz
services: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: 81309f0b5781e6302887a5b079ed359e70659834
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658961"
---
# <a name="roles-permissions-and-security-in-azure-monitor"></a>Role, oprávnění a zabezpečení ve službě Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Mnoho týmů musí přísně regulovat přístup k datům a nastavením monitorování. Máte-li například členy týmu, kteří pracují výhradně na monitorování (pracovníci podpory, inženýři společnosti DevOps) nebo pokud používáte poskytovatele spravovaných služeb, můžete jim udělit přístup pouze k datům monitorování a zároveň omezit jejich schopnost vytvářet, upravovat nebo odstranit prostředky. Tento článek ukazuje, jak rychle použít integrovanou roli RBAC monitorování pro uživatele v Azure nebo vytvořit vlastní roli pro uživatele, který potřebuje omezená oprávnění monitorování. Pak popisuje důležité informace o zabezpečení pro vaše prostředky související s Azure Monitor a jak můžete omezit přístup k datům, které obsahují.

## <a name="built-in-monitoring-roles"></a>Předdefinované role monitorování
Integrované role Azure Monitoru jsou navržené tak, aby pomohly omezit přístup k prostředkům v předplatném a zároveň umožnit těm, kteří jsou zodpovědní za monitorování infrastruktury, získat a nakonfigurovat data, která potřebují. Azure Monitor poskytuje dvě out-of-the-box role: čtečka monitorování a přispěvatel monitorování.

### <a name="monitoring-reader"></a>Monitorovací čtečka
Uživatelé přiřazené roli Čtečka monitorování můžete zobrazit všechna data monitorování v předplatném, ale nelze upravit žádný prostředek nebo upravit nastavení související s prostředky monitorování. Tato role je vhodná pro uživatele v organizaci, jako jsou pracovníci podpory nebo provozní inženýři, kteří musí být schopni:

* Zobrazení řídicích panelů monitorování na portálu a vytvoření vlastních soukromých monitorovacích řídicích panelů.
* Zobrazení pravidel výstrah definovaných v [Azure Alerts](alerts-overview.md)
* Dotaz na metriky pomocí [rozhraní AZURE Monitor REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx), [rutinprostředí PowerShell](powershell-quickstart-samples.md)nebo [rozhraní příkazového příkazu pro více platforem](cli-samples.md).
* Dotaz protokol aktivit pomocí portálu, Azure Monitor REST API, Rutiny PowerShell nebo cross-platformní rozhraní příkazového příkazu.
* Zobrazení [nastavení diagnostiky](diagnostic-settings.md) prostředku.
* Zobrazení [profilu protokolu](activity-log-export.md) pro odběr.
* Zobrazení nastavení automatického škálování.
* Zobrazení aktivity a nastavení výstrahy.
* Získejte přístup k datům Application Insights a zobrazte data v AI Analytics.
* Hledat data pracovního prostoru Log Analytics včetně dat o využití pracovního prostoru.
* Zobrazit skupiny pro správu Analýzy protokolů.
* Načtěte schéma hledání v pracovním prostoru Analýzy protokolů.
* Seznam monitorovacích balíčků v pracovním prostoru Log Analytics.
* Načtení a spuštění uložených vyhledávání v pracovním prostoru Log Analytics.
* Načtěte konfiguraci úložiště pracovního prostoru Analýzy protokolů.

> [!NOTE]
> Tato role neposkytuje přístup pro čtení k datům protokolu, která byla streamována do centra událostí nebo uložena v účtu úložiště. Informace o konfiguraci přístupu k těmto prostředkům [naleznete níže.](#security-considerations-for-monitoring-data)
> 
> 

### <a name="monitoring-contributor"></a>Přispěvatel monitorování
Uživatelé, kterým byla přiřazena role Přispěvatel monitorování, mohou zobrazit všechna data monitorování v předplatném a vytvořit nebo upravit nastavení monitorování, ale nemohou měnit žádné jiné prostředky. Tato role je nadmnožinou role monitorovací čtečky a je vhodná pro členy monitorovacího týmu organizace nebo poskytovatele spravovaných služeb, kteří kromě výše uvedených oprávnění musí být také schopni:

* Publikujte řídicí panely monitorování jako sdílený řídicí panel.
* Nastavte [diagnostická nastavení](diagnostic-settings.md) prostředku.\*
* Nastavte [profil protokolu](activity-log-export.md) pro odběr.\*
* Nastavte aktivitu a nastavení pravidel výstrah prostřednictvím [výstrah Azure Alerts](alerts-overview.md).
* Vytvořte webové testy a komponenty Application Insights.
* Seznam Log Analytics sdílené klíče pracovního prostoru.
* Povolení nebo zakázání monitorovacích balíčků v pracovním prostoru Log Analytics.
* Vytvořte a odstraňte a spusťte uložená hledání v pracovním prostoru Log Analytics.
* Vytvořte a odstraňte konfiguraci úložiště pracovního prostoru Analýzy protokolů.

\*Uživateli musí být také samostatně uděleno oprávnění ListKeys pro cílový prostředek (účet úložiště nebo obor názvů centra událostí) pro nastavení profilu protokolu nebo diagnostického nastavení.

> [!NOTE]
> Tato role neposkytuje přístup pro čtení k datům protokolu, která byla streamována do centra událostí nebo uložena v účtu úložiště. Informace o konfiguraci přístupu k těmto prostředkům [naleznete níže.](#security-considerations-for-monitoring-data)
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Oprávnění monitorování a vlastní role RBAC
Pokud výše uvedené předdefinované role nesplňují přesné potřeby vašeho týmu, můžete [vytvořit vlastní roli RBAC](../../role-based-access-control/custom-roles.md) s podrobnějšími oprávněními. Níže jsou společné operace Azure Monitor RBAC s jejich popisy.

| Operace | Popis |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Čtení, zápis, odstranění] |Skupiny akcí pro čtení,zápis/odstranění. |
| Microsoft.Insights/ActivityLogAlerts/[Čtení, zápis, odstranění] |Výstrahy protokolu aktivit pro čtení,zápis/výmaz. |
| Microsoft.Insights/AlertRules/[Čtení, zápis, odstranění] |Pravidla výstrah pro čtení/zápis/odstranění (z klasických výstrah). |
| Microsoft.Insights/AlertRules/Incidenty/Čtení |Seznam incidentů (historie aktivovaného pravidla výstrahy) pro pravidla výstrah. To platí pouze pro portál. |
| Microsoft.Insights/AutoscaleSettings/[Čtení, zápis, odstranění] |Nastavení automatického škálování pro čtení/zápis/odstranění. |
| Microsoft.Insights/DiagnosticSettings/[Čtení, zápis, odstranění] |Nastavení diagnostiky pro čtení/zápis/odstranění. |
| Microsoft.Insights/EventCategories/Read |Výčet všech kategorií možné v protokolu aktivit. Používá portál Azure. |
| Microsoft.Insights/eventtypes/digestevents/Read |Toto oprávnění je nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivit prostřednictvím portálu. |
| Microsoft.Insights/eventtypes/values/Read |Seznam událostí protokolu aktivit (události správy) v předplatném. Toto oprávnění se vztahuje na programový i portálový přístup k protokolu aktivit. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Čtení, zápis, odstranění] | Nastavení diagnostiky pro čtení,zápis/odstranění pro protokoly toku sítě. |
| Microsoft.Insights/LogDefinitions/Read |Toto oprávnění je nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivit prostřednictvím portálu. |
| Microsoft.Insights/LogProfiles/[Čtení, zápis, odstranění] |Čtení/ zápis/odstranění profilů protokolu (streamování protokolu aktivit do centra událostí nebo účtu úložiště). |
| Microsoft.Insights/MetricAlerts/[Čtení, zápis, odstranění] |Upozornění na čtení/zápis/odstranění metrik v reálném čase |
| Microsoft.Insights/MetricDefinitions/Read |Čtení definic metrik (seznam dostupných typů metrik pro prostředek). |
| Microsoft.Insights/Metriky/Čtení |Přečtěte si metriky pro prostředek. |
| Microsoft.Insights/Register/Action |Zaregistrujte poskytovatele prostředků Azure Monitoru. |
| Microsoft.Insights/ScheduledQueryRules/[Čtení, zápis, odstranění] |Výstrahy protokolu pro čtení a zápis a odstranění v Azure Monitoru. |



> [!NOTE]
> Přístup k výstrahám, diagnostickým nastavením a metrikám prostředku vyžaduje, aby měl uživatel přístup pro čtení k typu prostředku a oboru tohoto prostředku. Vytvoření ("zápisu") diagnostickénastavení nebo profil protokolu, který archivuje na účet úložiště nebo datových proudů do centra událostí vyžaduje, aby uživatel také mít ListKeys oprávnění k cílovému prostředku.
> 
> 

Například pomocí výše uvedené tabulky můžete vytvořit vlastní roli RBAC pro "Čtečku protokolů aktivit", jako je tento:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Důležité informace o zabezpečení dat monitorování
Data monitorování – zejména soubory protokolu – mohou obsahovat citlivé informace, například adresy IP nebo uživatelská jména. Data monitorování z Azure jsou k dispozici ve třech základních formách:

1. Protokol aktivit, který popisuje všechny akce roviny ovládacího prvku v rámci předplatného Azure.
2. protokoly prostředků, které jsou protokoly vyzařované prostředek.
3. Metriky, které jsou vydávány prostředky.

Všechny tři tyto datové typy se můžou ukládat v účtu úložiště nebo streamovat do Centra událostí, což jsou obecné prostředky Azure. Vzhledem k tomu, že se jedná o prostředky pro obecné účely, je vytváření, odstranění a přístup k nim privilegovanou operací vyhrazenou pro správce. Doporučujeme, abyste k zabránění zneužití používali následující postupy pro monitorování prostředků:

* Pro monitorování dat použijte jeden vyhrazený účet úložiště. Pokud potřebujete oddělit data monitorování do více účtů úložiště, nikdy nesdílejte využití účtu úložiště mezi daty monitorování a nemonitorování, protože to může nechtěně poskytnout těm, kteří potřebují pouze přístup k datům monitorování (například siem třetí strany) přístup k údajům, které nejsou monitorovacími.
* Použijte jeden vyhrazený obor názvů Service Bus nebo Event Hub ve všech diagnostických nastaveních ze stejného důvodu jako výše.
* Omezte přístup k účtům úložiště souvisejícím s monitorováním nebo centrům událostí tím, že je uchováte v samostatné skupině prostředků, a [použijte obor](../../role-based-access-control/overview.md#scope) v rolích monitorování k omezení přístupu pouze na tuto skupinu prostředků.
* Nikdy neudělte oprávnění ListKeys pro účty úložiště nebo centra událostí v oboru předplatného, když uživatel potřebuje pouze přístup k datům monitorování. Místo toho udělte tato oprávnění uživateli v oboru prostředku nebo prostředků (pokud máte vyhrazenou skupinu prostředků monitorování).

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Omezení přístupu k účtům úložiště souvisejících s monitorováním
Když uživatel nebo aplikace potřebuje přístup k datům monitorování v účtu úložiště, měli byste [vygenerovat sas účtu](https://msdn.microsoft.com/library/azure/mt584140.aspx) na účtu úložiště, který obsahuje data monitorování s přístupem k úložišti objektů blob na úrovni služby. V PowerShellu to může vypadat takto:

```powershell
$context = New-AzStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Potom můžete dát token entitě, která potřebuje číst z tohoto účtu úložiště a může vypsat a číst ze všech objektů BLOB v tomto účtu úložiště.

Případně pokud potřebujete řídit toto oprávnění pomocí RBAC, můžete této entitě udělit oprávnění Microsoft.Storage/storageAccounts/listkeys/action pro tento konkrétní účet úložiště. To je nezbytné pro uživatele, kteří potřebují mít možnost nastavit diagnostické nastavení nebo profil protokolu pro archivaci účtu úložiště. Můžete například vytvořit následující vlastní roli RBAC pro uživatele nebo aplikaci, která potřebuje číst pouze z jednoho účtu úložiště:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzRoleDefinition -Role $role 
```

> [!WARNING]
> Oprávnění ListKeys umožňuje uživateli vypsat seznam klíčů primárního a sekundárního účtu úložiště. Tyto klíče udělují uživateli všechna podepsaná oprávnění (čtení, zápis, vytváření objektů BLOB, odstranění objektů BLOB atd.) ve všech podepsaných službách (objekt blob, fronta, tabulka, soubor) v tomto účtu úložiště. Pokud je to možné, doporučujeme použít účet SAS popsaný výše.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Omezení přístupu k centrům událostí souvisejících s monitorováním
Podobný vzor lze sledovat s centra událostí, ale nejprve je třeba vytvořit vyhrazené pravidlo autorizace naslouchání. Pokud chcete udělit, přístup k aplikaci, která potřebuje pouze naslouchat centra událostí související s monitorováním, postupujte takto:

1. Vytvořte zásady sdíleného přístupu v centru událostí, které byly vytvořeny pro streamování dat monitorování pouze s deklaracemi Naslouchání. To lze provést na portálu. Například můžete volat "monitoringReadOnly." Pokud je to možné, budete chtít dát tento klíč přímo spotřebiteli a přeskočit další krok.
2. Pokud spotřebitel potřebuje získat klíč ad hoc, udělte uživateli akci ListKeys pro toto centrum událostí. To je také nezbytné pro uživatele, kteří potřebují mít možnost nastavit nastavení diagnostiky nebo profilu protokolu pro streamování do centra událostí. Můžete například vytvořit pravidlo RBAC:
   
   ```powershell
   $role = Get-AzRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzRoleDefinition -Role $role 
   ```

## <a name="monitoring-within-a-secured-virtual-network"></a>Monitorování v rámci zabezpečené virtuální sítě

Azure Monitor potřebuje přístup k vašim prostředkům Azure k poskytování služeb, které povolíte. Pokud chcete sledovat prostředky Azure a zároveň je zabezpečit před přístupem k veřejnému Internetu, můžete povolit následující nastavení.

### <a name="secured-storage-accounts"></a>Účty zabezpečeného úložiště 

Data monitorování se často zapisují do účtu úložiště. Můžete se ujistit, že data zkopírovaná do účtu úložiště nemohou přistupovat k neoprávněným uživatelům. Z důvodu dalšího zabezpečení můžete uzamknout přístup k síti, aby povolily pouze autorizované prostředky a důvěryhodné služby Společnosti Microsoft přístup k účtu úložiště omezením účtu úložiště na použití "vybraných sítí".
![Dialogové okno](./media/roles-permissions-security/secured-storage-example.png) Azure Storage Settings Azure Monitor se považuje za jednu z těchto "důvěryhodných služeb Microsoftu", pokud povolíte přístup důvěryhodným službám Microsoftu k přístupu k zabezpečenému úložišti, azure monitor bude mít přístup k vašemu zabezpečenému účtu úložiště. povolení zápisu protokolů prostředků Azure Monitor, protokolu aktivit a metrik do vašeho účtu úložiště za těchto chráněných podmínek. To také umožní Log Analytics číst protokoly ze zabezpečeného úložiště.   


Další informace najdete [v tématu Zabezpečení sítě a Úložiště Azure](../../storage/common/storage-network-security.md)

## <a name="next-steps"></a>Další kroky
* [Přečtěte si o RBAC a oprávnění ve Správci prostředků](../../role-based-access-control/overview.md)
* [Přečtěte si přehled monitorování v Azure](../../azure-monitor/overview.md)


