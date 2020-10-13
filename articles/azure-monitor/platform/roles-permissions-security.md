---
title: Role, oprávnění a zabezpečení v Azure Monitor
description: Naučte se používat předdefinované role a oprávnění Azure Monitor k omezení přístupu k prostředkům monitorování.
author: johnkemnetz
services: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: 7d92cbc25411f5cc2d528ccf6ecec4539494d380
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87533270"
---
# <a name="roles-permissions-and-security-in-azure-monitor"></a>Role, oprávnění a zabezpečení v Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Mnoho týmů potřebuje výhradně regulovat přístup k datům a nastavením monitorování. Například pokud máte členy týmu, kteří pracují výhradně na monitorování (technici podpory, DevOps technici) nebo pokud používáte poskytovatele spravované služby, můžete jim udělit přístup pouze k monitorování dat a omezit tak jejich schopnost vytvářet, upravovat nebo odstraňovat prostředky. Tento článek ukazuje, jak rychle použít integrovanou roli monitorování Azure pro uživatele v Azure nebo vytvořit vlastní roli pro uživatele, který potřebuje omezená oprávnění monitorování. Pak popisuje bezpečnostní požadavky pro vaše prostředky související s Azure Monitor a způsob, jakým můžete omezit přístup k datům, která obsahují.

## <a name="built-in-monitoring-roles"></a>Předdefinované role monitorování
Předdefinované role Azure Monitor jsou navržené tak, aby lépe omezily přístup k prostředkům v rámci předplatného, a zároveň umožňují uživatelům, kteří zodpovídají za monitorování infrastruktury, získat a nakonfigurovat potřebná data. Azure Monitor poskytuje dvě předem připravené role: čtečku monitorování a přispěvatel monitorování.

### <a name="monitoring-reader"></a>Čtečka monitorování
Lidé, kteří mají přiřazenou roli Čtenář monitorování, můžou zobrazit všechna data monitorování v rámci předplatného, ale nemůžou upravovat žádné prostředky ani upravovat žádná nastavení související s monitorováním prostředků. Tato role je vhodná pro uživatele v organizaci, jako je například podpora nebo provozní technici, kteří potřebují mít tyto možnosti:

* Zobrazení řídicích panelů pro monitorování na portálu a vytváření vlastních privátních řídicích panelů pro monitorování.
* Zobrazit pravidla výstrah definovaná v [upozorněních Azure](alerts-overview.md)
* Dotaz na metriky pomocí [Azure Monitor REST API](/rest/api/monitor/metrics), [rutin prostředí PowerShell](../samples/powershell-samples.md)nebo rozhraní [příkazového řádku pro více platforem](../samples/cli-samples.md).
* Pomocí portálu Azure Monitor REST API, rutiny PowerShellu nebo rozhraní příkazového řádku pro více platforem dotazování protokolu aktivit.
* Zobrazit [nastavení diagnostiky](diagnostic-settings.md) pro prostředek.
* Zobrazit [Profil protokolu](./activity-log.md#legacy-collection-methods) pro předplatné.
* Zobrazit nastavení automatického škálování.
* Zobrazit aktivitu a nastavení výstrah
* Přístup k datům Application Insights a zobrazení dat v nástroji AI Analytics.
* Vyhledejte Log Analytics data pracovního prostoru, včetně údajů o využití pro pracovní prostor.
* Zobrazení Log Analytics skupin pro správu.
* Načtěte schéma hledání v pracovním prostoru Log Analytics.
* Vypíše sady monitorování v pracovním prostoru Log Analytics.
* Načte a spustí uložená hledání v pracovním prostoru Log Analytics.
* Načtěte konfiguraci úložiště Log Analytics pracovního prostoru.

> [!NOTE]
> Tato role neuděluje oprávnění ke čtení pro data protokolu, která byla streamovaná do centra událostí nebo uložená v účtu úložiště. Informace o konfiguraci přístupu k těmto prostředkům [najdete níže](#security-considerations-for-monitoring-data) .
> 
> 

### <a name="monitoring-contributor"></a>Přispěvatel monitorování
Lidé, kteří mají přiřazenou roli Přispěvatel monitorování, můžou zobrazit všechna data monitorování v rámci předplatného a vytvářet nebo upravovat nastavení monitorování, ale nemůžou upravovat žádné další prostředky. Tato role je nadmnožinou role čtenář monitorování a je vhodná pro členy týmového týmu nebo poskytovatelů spravovaných služeb, kteří kromě výše uvedených oprávnění potřebují také:

* Publikujte řídicí panely monitorování jako sdílený řídicí panel.
* Nastavení [diagnostiky](diagnostic-settings.md) pro prostředek.\*
* Nastavte [Profil protokolu](./activity-log.md#legacy-collection-methods) pro předplatné.\*
* Nastavení aktivity a nastavení pravidel upozornění prostřednictvím [výstrah Azure](alerts-overview.md)
* Vytvoření Application Insights webové testy a součásti.
* Vypíše Log Analytics sdílené klíče pracovního prostoru.
* Povolte nebo zakažte sady monitorování v pracovním prostoru Log Analytics.
* Vytváření a odstraňování a spouštění uložených hledání v pracovním prostoru Log Analytics.
* Vytvořte a odstraňte konfiguraci úložiště Log Analytics pracovního prostoru.

\*uživatel musí také samostatně udělit oprávnění klíče listkey pro cílový prostředek (účet úložiště nebo obor názvů centra událostí), aby mohl nastavit profil protokolu nebo nastavení diagnostiky.

> [!NOTE]
> Tato role neuděluje oprávnění ke čtení pro data protokolu, která byla streamovaná do centra událostí nebo uložená v účtu úložiště. Informace o konfiguraci přístupu k těmto prostředkům [najdete níže](#security-considerations-for-monitoring-data) .
> 
> 

## <a name="monitoring-permissions-and-azure-custom-roles"></a>Oprávnění monitorování a vlastní role Azure
Pokud výše uvedené předdefinované role nevyhovují přesně vašim potřebám vašeho týmu, můžete [vytvořit vlastní roli Azure](../../role-based-access-control/custom-roles.md) s podrobnějšími oprávněními. Níže jsou uvedené běžné operace Azure Monitor RBAC s jejich popisy.

| Operace | Description |
| --- | --- |
| Microsoft. Insights/ActionGroups/[čtení, zápis, odstranění] |Skupiny akcí čtení, zápisu a odstranění. |
| Microsoft. Insights/Upozorněníprotokoluaktivit/[čtení, zápis, odstranění] |Čtení, zápis a odstraňování výstrah protokolu aktivit. |
| Microsoft. Insights/AlertRules/[čtení, zápis, odstranění] |Pravidla upozornění pro čtení, zápis a odstranění (z klasických výstrah). |
| Microsoft. Insights/AlertRules/incidenty/číst |Vypíše incidenty (historii aktivovaného pravidla výstrahy) pro pravidla upozornění. To platí jenom pro portál. |
| Microsoft. Insights/AutoscaleSettings/[čtení, zápis, odstranění] |Nastavení automatického škálování pro čtení, zápis a odstranění |
| Microsoft. Insights/DiagnosticSettings/[čtení, zápis, odstranění] |Nastavení diagnostiky pro čtení, zápis a odstranění |
| Microsoft. Insights/EventCategories/Read |Umožňuje zobrazit výčet všech kategorií v protokolu aktivit. Použito Azure Portal. |
| Microsoft. Insights/EventType/digestevents/Read |Toto oprávnění je nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivit prostřednictvím portálu. |
| Microsoft. Insights/EventTypes/Values/Read |Vypíše události protokolu aktivit (události správy) v předplatném. Toto oprávnění platí pro programový i portálový přístup k protokolu aktivit. |
| Microsoft. Insights/ExtendedDiagnosticSettings/[čtení, zápis, odstranění] | Nastavení diagnostiky pro čtení, zápis a odstranění pro protokoly toku sítě. |
| Microsoft. Insights/LogDefinitions/Read |Toto oprávnění je nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivit prostřednictvím portálu. |
| Microsoft. Insights/LogProfiles/[čtení, zápis, odstranění] |Profily protokolů pro čtení, zápis a odstranění (streamování protokolů aktivit do centra událostí nebo účtu úložiště). |
| Microsoft. Insights/MetricAlerts/[čtení, zápis, odstranění] |Čtení, zápis a odstraňování pro výstrahy metriky téměř v reálném čase |
| Microsoft. Insights/MetricDefinitions/Read |Čtení definic metriky (seznam dostupných typů metrik pro prostředek). |
| Microsoft. Insights/metriky/číst |Načte metriky pro prostředek. |
| Microsoft. Insights/registr/Action |Zaregistrujte poskytovatele prostředků Azure Monitor. |
| Microsoft. Insights/ScheduledQueryRules/[čtení, zápis, odstranění] |Výstrahy protokolu čtení, zápisu a odstraňování v Azure Monitor. |



> [!NOTE]
> Přístup k výstrahám, nastavení diagnostiky a metrikám prostředku vyžaduje, aby měl uživatel oprávnění ke čtení pro daný typ prostředku a rozsah daného prostředku. Vytvoření ("zápisu") nastavení diagnostiky nebo profil protokolu, který archivuje do účtu úložiště nebo datových proudů do centra událostí vyžaduje, aby uživatel měl také oprávnění klíče listkey k cílovému prostředku.
> 
> 

Například pomocí výše uvedené tabulky můžete vytvořit vlastní roli Azure pro "čtecí modul protokolu aktivit" takto:

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
Data monitorování – zejména soubory protokolů – můžou obsahovat citlivé informace, jako jsou IP adresy nebo uživatelská jména. Data monitorování z Azure se nachází ve třech základních formulářích:

1. Protokol aktivit, který popisuje všechny akce na úrovni ovládacího prvku na vašem předplatném Azure.
2. protokoly prostředků, které jsou protokoly emitované prostředkem.
3. Metriky, které jsou vygenerovány prostředky.

Všechny tři tyto datové typy můžou být uložené v účtu úložiště nebo streamované do centra událostí, z nichž obě jsou prostředky Azure pro obecné účely. Vzhledem k tomu, že se jedná o prostředky pro obecné účely, vytváření, odstraňování a přístup k nim jsou privilegované operace rezervované pro správce. Doporučujeme použít následující postupy pro prostředky související se sledováním, abyste zabránili zneužití:

* Pro data monitorování použijte jeden vyhrazený účet úložiště. Pokud potřebujete data monitorování rozdělit do několika účtů úložiště, nikdy nesdílejte použití účtu úložiště mezi monitorováním a nemonitorovanými daty, protože to může nechtěně poskytnout uživatelům, kteří potřebují přístup k datům monitorování (například SIEM třetí strany) přístup k datům, která nejsou sledována.
* V rámci všech nastavení diagnostiky použijte jeden vyhrazený Service Bus nebo obor názvů centra událostí.
* Omezte přístup k účtům úložiště nebo centrům událostí souvisejícím s monitorováním, a to tak, že je zachováte v samostatné skupině prostředků, a pokud chcete omezit přístup jenom na tuto skupinu prostředků, [použijte rozsah](../../role-based-access-control/overview.md#scope) rolí monitorování.
* Nikdy udělte oprávnění klíče listkey pro účty úložiště nebo centra událostí v oboru předplatného, když uživatel potřebuje přístup k datům monitorování. Místo toho udělte uživatelům tato oprávnění v rámci prostředku nebo skupiny prostředků (Pokud máte vyhrazený rozsah skupiny prostředků monitorování).

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Omezení přístupu k účtům úložiště souvisejícím s monitorováním
Pokud uživatel nebo aplikace potřebuje přístup k datům monitorování v účtu úložiště, měli byste [vygenerovat SAS účtu](/rest/api/storageservices/create-account-sas) v účtu úložiště, který obsahuje data monitorování s přístupem jen pro čtení na úrovni služby k úložišti objektů BLOB. V PowerShellu to může vypadat takto:

```powershell
$context = New-AzStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Token pak můžete předat entitě, která musí číst z daného účtu úložiště, a může vypisovat a číst ze všech objektů BLOB v tomto účtu úložiště.

Případně, pokud potřebujete řídit toto oprávnění pomocí RBAC, můžete této entitě udělit oprávnění Microsoft. Storage/storageAccounts/klíče listkey/Action na daném účtu úložiště. To je nezbytné pro uživatele, kteří potřebují mít možnost nastavit diagnostické nastavení nebo profil protokolu k archivaci na účet úložiště. Můžete například vytvořit následující vlastní roli Azure pro uživatele nebo aplikaci, které musí číst pouze z jednoho účtu úložiště:

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
> Oprávnění klíče listkey umožňuje uživateli vypsat primární a sekundární klíč účtu úložiště. Tyto klíče udělují uživateli všechna podepsaná oprávnění (čtení, zápis, vytváření objektů blob, odstraňování objektů BLOB atd.) napříč všemi podepsanými službami (BLOB, Queue, Table, File) v tomto účtu úložiště. Doporučujeme použít výše popsané přidružení zabezpečení účtu, pokud je to možné.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Omezení přístupu k centrům událostí souvisejícím s monitorováním
Podobný vzor může následovat s centry událostí, ale nejdřív je potřeba vytvořit vyhrazené autorizační pravidlo pro naslouchání. Pokud chcete udělit přístup k aplikaci, která potřebuje jenom naslouchat centrům událostí souvisejících s monitorováním, udělejte toto:

1. Vytvořte zásady sdíleného přístupu v prostředcích událostí, které byly vytvořeny pro streamování dat monitorování pouze pomocí deklarací naslouchání. To se dá udělat na portálu. Například můžete zavolat "monitoringReadOnly". Pokud je to možné, budete chtít předat tento klíč přímo příjemci a přeskočit další krok.
2. Pokud je příjemce schopný získat klíčovou ad hoc, udělte uživateli akci klíče listkey pro toto centrum událostí. To je nezbytné taky pro uživatele, kteří potřebují mít možnost nastavit diagnostické nastavení nebo profil protokolu pro streamování na centra událostí. Můžete například vytvořit pravidlo RBAC:
   
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

Azure Monitor potřebuje přístup k prostředkům Azure, abyste mohli poskytovat služby, které povolíte. Pokud chcete monitorovat prostředky Azure a zároveň je zabezpečit před přístupem k veřejnému Internetu, můžete povolit následující nastavení.

### <a name="secured-storage-accounts"></a>Zabezpečené účty úložiště 

Data monitorování se často zapisují do účtu úložiště. Možná budete chtít zajistit, aby data zkopírovaná do účtu úložiště nezískal přístup neautorizovaných uživatelů. Pro zvýšení zabezpečení můžete uzamknout síťový přístup a povolit přístup jenom autorizovaným prostředkům a důvěryhodným službám Microsoftu k účtu úložiště tím, že omezíte účet úložiště tak, aby používal vybrané sítě.
![Dialog nastavení Azure Storage ](./media/roles-permissions-security/secured-storage-example.png) Azure monitor se považuje za jednu z těchto "důvěryhodných služeb Microsoftu", pokud povolíte důvěryhodným službám Microsoftu přístup k zabezpečenému úložišti, bude mít Azure monitor přístup k vašemu účtu zabezpečeného úložiště. Díky těmto chráněným podmínkám povolíte zápis Azure Monitorch protokolů o prostředcích, protokolu aktivit a metrik do svého účtu úložiště. Umožní vám taky Log Analytics číst protokoly ze zabezpečeného úložiště.   


Další informace najdete v tématu [zabezpečení sítě a Azure Storage](../../storage/common/storage-network-security.md)

## <a name="next-steps"></a>Další kroky
* [Přečtěte si o RBAC a oprávněních v Správce prostředků](../../role-based-access-control/overview.md)
* [Přečtěte si přehled monitorování v Azure.](../overview.md)

