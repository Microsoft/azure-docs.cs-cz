---
title: Protokolování diagnostiky pro Azure Analysis Services | Microsoft Docs
description: Popisuje, jak nastavit protokolování pro monitorování Azure Analysis Services serveru.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2bee856adef1208aabbe65ecd5fd11235579bb82
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582705"
---
# <a name="setup-diagnostic-logging"></a>Nastavení protokolování diagnostiky

Důležitou součástí jakéhokoli řešení Analysis Services je monitorování, jak vaše servery provádějí. Služba Azure Analysis Services je integrovaná s Azure Monitor. Pomocí [Azure Monitorch protokolů prostředků](../azure-monitor/essentials/platform-logs-overview.md)můžete protokoly monitorovat a odesílat [Azure Storage](https://azure.microsoft.com/services/storage/), streamovat je do [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)a exportovat je do [protokolů Azure monitor](../azure-monitor/overview.md).

![Protokolování prostředků do protokolů úložiště, Event Hubs nebo Azure Monitor](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Co se protokoluje?

Můžete vybrat kategorie **modul**, **služby** a **metriky** .

### <a name="engine"></a>Modul

Výběr **modulu** protokoluje všechny [xEvents](/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Nemůžete vybrat jednotlivé události. 

|Kategorie XEvent |Název události  |
|---------|---------|
|Audit zabezpečení    |   Protokol auditu      |
|Audit zabezpečení    |   Odhlášení auditu      |
|Audit zabezpečení    |   Spustí se a zastaví se audit serveru.      |
|Sestavy průběhu     |   Začátek sestavy průběhu      |
|Sestavy průběhu     |   Konec sestavy průběhu      |
|Sestavy průběhu     |   Aktuální sestava průběhu      |
|Dotazy     |  Začátek dotazu       |
|Dotazy     |   Konec dotazu      |
|Příkazy     |  Začátek příkazu       |
|Příkazy     |  Konec příkazu       |
|Chyby & upozornění     |   Chyba      |
|Zjišťování     |   Zjistit konec      |
|Notification (Oznámení)     |    Notification (Oznámení)     |
|Relace     |  Inicializace relace       |
|Zámky    |  Vzájemné zablokování       |
|Zpracování dotazů     |   VertiPaq SE – začátek dotazu      |
|Zpracování dotazů     |   VertiPaq SE – konec dotazu      |
|Zpracování dotazů     |   VertiPaq SE – shoda mezipaměti dotazů      |
|Zpracování dotazů     |   Začátek přímého dotazu      |
|Zpracování dotazů     |  Přímý dotaz na konec       |

### <a name="service"></a>Služba

|Název operace  |Vyvolá se v případě, že  |
|---------|---------|
|ResumeServer     |    Obnovení serveru     |
|SuspendServer    |   Pozastavení serveru      |
|DeleteServer     |    Odstranění serveru     |
|RestartServer    |     Uživatel restartuje server prostřednictvím SSMS nebo PowerShellu.    |
|GetServerLogFiles    |    Uživatel exportuje protokol serveru přes PowerShell.     |
|ExportModel     |   Uživatel exportuje model na portálu pomocí otevřít v aplikaci Visual Studio.     |

### <a name="all-metrics"></a>Všechny metriky

Kategorie metriky zapisuje stejné [metriky serveru](analysis-services-monitor.md#server-metrics) do tabulky AzureMetrics. Pokud používáte [škálování](analysis-services-scale-out.md) dotazů a potřebujete pro každou repliku čtení oddělit metriky, použijte místo toho tabulku AzureDiagnostics, kde **OperationName** se rovná **LogMetric**.

## <a name="setup-diagnostics-logging"></a>Nastavení protokolování diagnostiky

### <a name="azure-portal"></a>portál Azure

1. V [Azure Portal](https://portal.azure.com) > serveru klikněte v levém navigačním panelu na **nastavení diagnostiky** a pak klikněte na **zapnout diagnostiku**.

    ![Zapnout protokolování prostředků pro Azure Cosmos DB v Azure Portal](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. V **Nastavení diagnostiky** určete následující možnosti: 

    * **Název:** Zadejte název protokolů, které chcete vytvořit.

    * **Archivujte do účtu úložiště**. Pokud chcete použít tuto možnost, budete potřebovat existující účet úložiště, ke kterému se chcete připojit. Viz [Vytvoření účtu úložiště](../storage/common/storage-account-create.md). Podle pokynů vytvořte Správce prostředků účet pro obecné účely a pak vyberte svůj účet úložiště tak, že se vrátíte na tuto stránku na portálu. Může trvat několik minut, než se nově vytvořené účty zobrazí v rozevírací nabídce.
    * **Streamování do centra událostí**. Pokud chcete použít tuto možnost, budete potřebovat existující obor názvů centra událostí a centrum událostí, ke kterým se chcete připojit. Další informace najdete v tématu [Vytvoření oboru názvů služby Event Hubs a centra událostí pomocí webu Azure Portal](../event-hubs/event-hubs-create.md). Pak se vraťte na tuto stránku na portálu a vyberte obor názvů a název zásady centra událostí.
    * **Odeslat do Azure monitor (pracovní prostor Log Analytics)**. Pokud chcete tuto možnost použít, buď použijte existující pracovní prostor, nebo na portálu [vytvořte nový prostředek pracovního prostoru](../azure-monitor/logs/quick-create-workspace.md) . Další informace o zobrazení protokolů najdete v části [zobrazení protokolů v Log Analytics pracovním prostoru](#view-logs-in-log-analytics-workspace) v tomto článku.

    * **Modul**. Tuto možnost vyberte, pokud chcete protokolovat xEvents. Pokud provádíte archivaci na účet úložiště, můžete vybrat dobu uchování pro protokoly prostředků. Protokoly se po vypršení doby uchování odstraní znovu.
    * **Služba**. Tuto možnost vyberte, pokud chcete protokolovat události na úrovni služby. Pokud provádíte archivaci na účet úložiště, můžete vybrat dobu uchování pro protokoly prostředků. Protokoly se po vypršení doby uchování odstraní znovu.
    * **Metriky**. Tuto možnost vyberte, pokud chcete ukládat podrobná data do [metrik](analysis-services-monitor.md#server-metrics). Pokud provádíte archivaci na účet úložiště, můžete vybrat dobu uchování pro protokoly prostředků. Protokoly se po vypršení doby uchování odstraní znovu.

3. Klikněte na **Uložit**.

    Pokud se zobrazí chyba s informacemi o tom, že se nepovedlo aktualizovat diagnostiku pro \<workspace name> . Předplatné \<subscription id> není zaregistrované pro používání Microsoft. Insights. pomocí pokynů [Azure Diagnostics Poradce při potížích](../azure-monitor/essentials/resource-logs.md) zaregistrujte účet a potom tento postup opakujte.

    Pokud chcete změnit způsob, jakým se protokoly prostředků ukládají do libovolného bodu v budoucnu, můžete se na tuto stránku vrátit a změnit nastavení.

### <a name="powershell"></a>PowerShell

Tady jsou základní příkazy, které vám pomohou. Pokud potřebujete podrobné informace o nastavení protokolování do účtu úložiště pomocí PowerShellu, přečtěte si kurz dále v tomto článku.

Pokud chcete povolit metriky a protokolování prostředků pomocí PowerShellu, použijte následující příkazy:

- Pokud chcete povolit úložiště protokolů prostředků v účtu úložiště, použijte tento příkaz:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   ID účtu úložiště je ID prostředku účtu úložiště, kam chcete posílat protokoly.

- Pokud chcete povolit streamování protokolů prostředků do centra událostí, použijte tento příkaz:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   ID pravidla služby Azure Service Bus je řetězec v tomto formátu:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Pokud chcete povolit odesílání protokolů prostředků do Log Analytics pracovního prostoru, použijte tento příkaz:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- ID prostředku pracovního prostoru Log Analytics můžete získat pomocí následujícího příkazu:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Tyto parametry můžete kombinovat a povolit tak různé možnosti výstupu.

### <a name="rest-api"></a>Rozhraní REST API

Zde zjistíte, jak [změnit nastavení diagnostiky pomocí rozhraní REST API služby Azure Monitor](/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Šablona Resource Manageru

Zde zjistíte, jak [povolit nastavení diagnostiky při vytváření prostředku pomocí šablony Resource Manageru](../azure-monitor/essentials/resource-manager-diagnostic-settings.md). 

## <a name="manage-your-logs"></a>Správa protokolů

Protokoly jsou obvykle k dispozici během několika hodin od nastavení protokolování. Správa protokolů ve vašem účtu úložiště záleží na vás:

* Protokoly můžete zabezpečit, když k nim omezíte přístup pomocí standardních metod pro řízení přístupu Azure.
* Odstraňujte protokoly, které už nechcete uchovávat v účtu úložiště.
* Nezapomeňte nastavit dobu uchování, aby se staré protokoly odstranily z účtu úložiště.

## <a name="view-logs-in-log-analytics-workspace"></a>Zobrazit protokoly v pracovním prostoru Log Analytics

Metriky a události serveru jsou integrovány do xEvents v prostředku pracovního prostoru Log Analytics pro souběžnou analýzu. Log Analytics pracovní prostor je taky možné nakonfigurovat tak, aby přijímal události z jiných služeb Azure, které poskytují holistický zobrazení dat protokolování diagnostiky napříč vaší architekturou.

Pokud chcete zobrazit diagnostická data, v Log Analytics pracovním prostoru otevřete **protokoly**  v nabídce vlevo.

![Možnosti prohledávání protokolu v Azure Portal](./media/analysis-services-logging/aas-logging-open-log-search.png)

V Tvůrci dotazů rozbalte položku **LogManagement**  >  **AzureDiagnostics**. AzureDiagnostics zahrnuje události modulu a služby. Všimněte si, že je dotaz vytvořen průběžně. Pole EventClass \_ s obsahuje názvy xEvent, které mohou vypadat dobře, pokud jste použili xEvents pro místní protokolování. Klikněte na **EventClass \_ s** nebo na jeden z názvů událostí a Log Analytics pracovní prostor stále sestavuje dotaz. Nezapomeňte si své dotazy uložit pro pozdější použití.

### <a name="example-queries"></a>Ukázky dotazů

#### <a name="example-1"></a>Příklad 1

Následující dotaz vrátí dobu trvání každé události konce a aktualizace dotazu pro modelovou databázi a Server. Při horizontálním navýšení kapacity jsou výsledky rozdělené replikou, protože číslo repliky je zahrnuté v ServerName_s. Seskupení podle RootActivityId_g zmenší počet řádků načtený z Azure Diagnosticsho REST API a pomůže zůstat v rámci limitů, jak je popsáno v [omezeních sazeb Log Analytics](https://dev.loganalytics.io/documentation/Using-the-API/Limits).

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName" and DatabaseName_s =~ "MyDatabaseName" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs
| order by StartTime_t asc
```

#### <a name="example-2"></a>Příklad 2

Následující dotaz vrátí paměťovou a QPU spotřebu pro server. Při horizontálním navýšení kapacity jsou výsledky rozdělené replikou, protože číslo repliky je zahrnuté v ServerName_s.

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where name_s == "memory_metric" or name_s == "qpu_metric"
| project ServerName_s, TimeGenerated, name_s, value_s
| summarize avg(todecimal(value_s)) by ServerName_s, name_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

#### <a name="example-3"></a>Příklad 3

Následující dotaz vrátí čítače výkonu Analysis Services pro daný server v řádcích.

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where parse_json(tostring(parse_json(perfobject_s).counters))[0].name == "Rows read/sec" 
| extend Value = tostring(parse_json(tostring(parse_json(perfobject_s).counters))[0].value) 
| project ServerName_s, TimeGenerated, Value
| summarize avg(todecimal(Value)) by ServerName_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

Existují stovky dotazů, které můžete použít. Další informace o dotazech najdete v tématu [Začínáme s Azure monitor dotazy protokolu](../azure-monitor/logs/get-started-queries.md).


## <a name="turn-on-logging-by-using-powershell"></a>Zapnutí protokolování pomocí PowerShellu

V tomto rychlém kurzu vytvoříte účet úložiště ve stejném předplatném a skupině prostředků jako server služby Analysis Service. Pak použijete Set-AzDiagnosticSetting k zapnutí protokolování diagnostiky a odeslání výstupu do nového účtu úložiště.

### <a name="prerequisites"></a>Požadavky
K dokončení tohoto kurzu musíte mít následující prostředky:

* Existující server Azure Analysis Services. Pokyny k vytvoření prostředku serveru najdete v tématu [vytvoření serveru v Azure Portal](analysis-services-create-server.md)nebo [Vytvoření Azure Analysis Servicesho serveru pomocí PowerShellu](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Připojení k předplatným

Spusťte relaci Azure PowerShellu a přihlaste se k účtu Azure pomocí následujícího příkazu:  

```powershell
Connect-AzAccount
```

V automaticky otevřeném okně prohlížeče zadejte svoje uživatelské jméno a heslo k účtu Azure. Azure PowerShell získá všechna předplatná přidružená k tomuto účtu a ve výchozím nastavení použije první předplatné.

Máte-li více předplatných, možná budete muset zadat předplatné, které jste použili pro vytvoření Azure Key Vault. Chcete-li zobrazit předplatná vašeho účtu, zadejte následující:

```powershell
Get-AzSubscription
```

Potom zadejte následující příkaz, abyste určili předplatné přidružené k účtu Azure Analysis Services, který jste nahlásili:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Pokud máte k vašemu účtu přidruženo několik předplatných, je důležité zadat předplatné.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Vytvořit pro svoje protokoly nový účet úložiště

Pro svoje protokoly můžete použít existující účet úložiště, pokud je ve stejném předplatném jako váš server. V tomto kurzu vytvoříte nový účet úložiště vyhrazený pro Analysis Services protokoly. Pokud chcete usnadnit ukládání podrobností účtu úložiště do proměnné s názvem **SA**.

Použijete také stejnou skupinu prostředků jako ta, která obsahuje váš server Analysis Services. Nahraďte hodnoty pro `awsales_resgroup` , `awsaleslogs` a `West Central US` vlastními hodnotami:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identifikujte účet serveru pro vaše protokoly

Nastavte název účtu na proměnnou s názvem **account**, kde resourceName je název účtu.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Povolit protokolování

Pokud chcete povolit protokolování, použijte rutinu Set-AzDiagnosticSetting společně s proměnnými pro nový účet úložiště, účet serveru a kategorii. Spusťte následující příkaz, nastavením příznaku **-Enabled** na **$true**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

Výstup by měl vypadat přibližně jako v tomto příkladu:

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

Tento výstup potvrdí, že protokolování je teď pro server povolené, a ukládá informace do účtu úložiště.

Pro protokoly můžete také nastavit zásady uchovávání informací, aby se automaticky odstranily staré protokoly. Například nastavte zásady uchovávání informací pomocí příznaku **-RetentionEnabled** na **$true** a nastavte parametr **-RetentionInDays** na **90**. Protokoly starší než 90 dní se odstraní automaticky.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [Azure monitor protokolování prostředků](../azure-monitor/essentials/platform-logs-overview.md).

Viz rutina [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) v nápovědě PowerShellu.