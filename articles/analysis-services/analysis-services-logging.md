---
title: Protokolování diagnostiky pro službu Azure Analysis Services | Dokumenty společnosti Microsoft
description: Popisuje, jak nastavit protokolování diagnostiky prostředků Azure pro monitorování serveru Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0f13f297facedceb50920c0f6afca63fe1df0b48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266179"
---
# <a name="setup-diagnostic-logging"></a>Nastavení protokolování diagnostiky

Důležitou součástí každého řešení Analysis Services je sledování výkonu serverů. Pomocí [protokolů prostředků Azure](../azure-monitor/platform/platform-logs-overview.md)můžete protokoly monitorovat a odesílat do Azure [Storage](https://azure.microsoft.com/services/storage/), streamovat je do Center [událostí Azure](https://azure.microsoft.com/services/event-hubs/)a exportovat je do [protokolů Azure Monitoru](../azure-monitor/azure-monitor-log-hub.md).

![Diagnostické protokolování do protokolů úložiště, centra událostí nebo Azure Monitoru](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Co se protokoluje?

Můžete vybrat kategorie **Motor**, **Služba**a **Metriky.**

### <a name="engine"></a>Modul

Výběr **modulu** zaznamená všechny [xEvents](https://docs.microsoft.com/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Nelze vybrat jednotlivé události. 

|Kategorie XEvent |Název události  |
|---------|---------|
|Audit zabezpečení    |   Protokol auditu      |
|Audit zabezpečení    |   Odhlášení auditu      |
|Audit zabezpečení    |   Server auditu se spouští a zastavuje      |
|Zprávy o průběhu     |   Zahájení zprávy o průběhu      |
|Zprávy o průběhu     |   Konec zprávy o průběhu      |
|Zprávy o průběhu     |   Aktuální zpráva o průběhu      |
|Dotazy     |  Začátek dotazu       |
|Dotazy     |   Konec dotazu      |
|Příkazy     |  Začátek příkazu       |
|Příkazy     |  Konec příkazu       |
|Chyby & varování     |   Chyba      |
|Nenechte si ujít     |   Objevte konec      |
|Oznámení     |    Oznámení     |
|Relace     |  Inicializace relace       |
|Zámky    |  Zablokování       |
|Zpracování dotazů     |   VertiPaq SE začíná dotaz      |
|Zpracování dotazů     |   Konec dotazu VertiPaq SE      |
|Zpracování dotazů     |   Shoda mezipaměti dotazu VertiPaq SE      |
|Zpracování dotazů     |   Zahájení přímého dotazu      |
|Zpracování dotazů     |  Konec přímého dotazu       |

### <a name="service"></a>Služba

|Název operace  |Nastane, když  |
|---------|---------|
|ResumeServer     |    Obnovení serveru     |
|Suspendserver    |   Pozastavení serveru      |
|Odstranit server     |    Odstranění serveru     |
|Restartovat server    |     Uživatel restartuje server prostřednictvím služby SSMS nebo PowerShell    |
|GetServerLogFiles    |    Uživatel exportuje protokol serveru prostřednictvím prostředí PowerShell.     |
|Exportmodel     |   Uživatel exportuje model na portálu pomocí otevřít v sadě Visual Studio     |

### <a name="all-metrics"></a>Všechny metriky

Kategorie Metriky protokoluje stejné [metriky serveru](analysis-services-monitor.md#server-metrics) do tabulky AzureMetrics. Pokud používáte [horizontální navýšení kapacity dotazu](analysis-services-scale-out.md) a potřebujete oddělit metriky pro každou repliku pro čtení, použijte místo toho tabulku AzureDiagnostics, kde se **OperationName** rovná **LogMetric**.

## <a name="setup-diagnostics-logging"></a>Nastavení protokolování diagnostiky

### <a name="azure-portal"></a>portál Azure

1. Na [serveru Azure Portal](https://portal.azure.com) > klikněte v levé navigaci na Diagnostické **protokoly** a potom klikněte **na Zapnout diagnostiku**.

    ![Zapnutí protokolování diagnostiky pro Azure Cosmos DB na webu Azure Portal](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. V **Nastavení diagnostiky** určete následující možnosti: 

    * **Název**. Zadejte název protokolů, které chcete vytvořit.

    * **Archivujte do účtu úložiště**. Chcete-li použít tuto možnost, budete potřebovat existující účet úložiště pro připojení. Viz [Vytvoření účtu úložiště](../storage/common/storage-create-storage-account.md). Podle pokynů vytvořte správce prostředků, účet pro obecné účely, a pak vyberte účet úložiště tak, že se vrátíte na tuto stránku na portálu. Může trvat několik minut, než se nově vytvořené účty zobrazí v rozevírací nabídce.
    * **Streamujte do centra událostí**. Chcete-li použít tuto možnost, potřebujete existující obor názvů event hub a centrum událostí pro připojení. Další informace najdete v tématu [Vytvoření oboru názvů služby Event Hubs a centra událostí pomocí webu Azure Portal](../event-hubs/event-hubs-create.md). Pak se vraťte na tuto stránku na portálu a vyberte obor názvů centra událostí a název zásady.
    * **Odeslat do Azure Monitor (log Analytics pracovní prostor)**. Chcete-li použít tuto možnost, použijte existující pracovní prostor nebo vytvořte nový zdroj [pracovního prostoru](../azure-monitor/learn/quick-create-workspace.md) na portálu. Další informace o zobrazení protokolů naleznete v [tématu Zobrazení protokolů v pracovním prostoru Log Analytics](#view-logs-in-log-analytics-workspace) v tomto článku.

    * **Motor**. Tuto možnost vyberte, chcete-li protokolovat xEvents. Pokud archivujete účet úložiště, můžete vybrat dobu uchování diagnostických protokolů. Protokoly jsou automaticky odstraněny po uplynutí doby uchování.
    * **Služba**. Tuto možnost vyberte, chcete-li protokolovat události na úrovni služby. Pokud archivujete data do účtu úložiště, můžete vybrat dobu uchovávání diagnostických protokolů. Protokoly jsou automaticky odstraněny po uplynutí doby uchování.
    * **Metriky**. Tuto možnost vyberte, chcete-li ukládat podrobná data do [metriky](analysis-services-monitor.md#server-metrics). Pokud archivujete data do účtu úložiště, můžete vybrat dobu uchovávání diagnostických protokolů. Protokoly jsou automaticky odstraněny po uplynutí doby uchování.

3. Klikněte na **Uložit**.

    Pokud se zobrazí chyba s nápisem "Aktualizace diagnostiky pro \<název pracovního prostoru> se nezdařila. Id \<předplatného> není registrováno pro použití microsoft.insights." Postupujte [podle pokynů poradce při potížích s diagnostikou Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) a zaregistrujte účet a opakujte tento postup.

    Pokud chcete změnit způsob ukládání diagnostických protokolů v libovolném okamžiku v budoucnu, můžete se vrátit na tuto stránku a upravit nastavení.

### <a name="powershell"></a>PowerShell

Zde jsou základní příkazy, které vás rozjedou. Pokud chcete podrobné nápovědy k nastavení protokolování k účtu úložiště pomocí Prostředí PowerShell, přečtěte si kurz dále v tomto článku.

Pokud chcete povolit protokolování metriky a diagnostiky pomocí prostředí PowerShell, použijte následující příkazy:

- Pokud chcete povolit úložiště pro diagnostické protokoly v účtu úložiště, použijte tento příkaz:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   ID účtu úložiště je ID prostředku účtu úložiště, kam chcete posílat protokoly.

- Pokud chcete povolit streamování diagnostických protokolů do centra událostí, použijte tento příkaz:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   ID pravidla služby Azure Service Bus je řetězec v tomto formátu:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Pokud chcete povolit posílání diagnostických protokolů do pracovního prostoru Log Analytics, použijte tento příkaz:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- ID prostředku pracovního prostoru Log Analytics můžete získat pomocí následujícího příkazu:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Tyto parametry můžete kombinovat a povolit tak různé možnosti výstupu.

### <a name="rest-api"></a>REST API

Zde zjistíte, jak [změnit nastavení diagnostiky pomocí rozhraní REST API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Šablona Resource Manageru

Zde zjistíte, jak [povolit nastavení diagnostiky při vytváření prostředku pomocí šablony Resource Manageru](../azure-monitor/platform/diagnostic-settings-template.md). 

## <a name="manage-your-logs"></a>Správa protokolů

Protokoly jsou obvykle k dispozici během několika hodin od nastavení protokolování. Správa protokolů v účtu úložiště je pouze na vás:

* Zabezpečte protokoly pomocí standardních metod řízení přístupu Azure a určete, kdo k nim má přístup.
* Odstraňte protokoly, které už nechcete uchovávat v účtu úložiště.
* Nezapomeňte nastavit dobu uchování pro tak staré protokoly jsou odstraněny z vašeho účtu úložiště.

## <a name="view-logs-in-log-analytics-workspace"></a>Zobrazení protokolů v pracovním prostoru Analýzy protokolů

Metriky a události serveru jsou integrovány s xEvents v prostředku pracovního prostoru Log Analytics pro analýzu vedle sebe. Pracovní prostor Log Analytics lze také nakonfigurovat tak, aby přijímala události z jiných služeb Azure a poskytovala ucelené zobrazení diagnostických dat protokolování napříč architekturou.

Chcete-li zobrazit diagnostická data, otevřete v pracovním prostoru Log Analytics **protokoly protokoly** z levé nabídky.

![Možnosti hledání protokolování na webu Azure Portal](./media/analysis-services-logging/aas-logging-open-log-search.png)

V tvůrce dotazů rozbalte **LogManagement** > **AzureDiagnostics**. AzureDiagnostics zahrnuje události modulu a služby. Všimněte si, že dotaz je vytvořen průběžně. Pole EventClass\_s obsahuje názvy xEvent, které mohou vypadat povědomě, pokud jste použili xEvents pro místní protokolování. Klikněte na **EventClass\_s** nebo na jeden z názvů událostí a pracovní prostor Log Analytics pokračuje ve vytváření dotazu. Nezapomeňte si své dotazy uložit pro pozdější použití.

### <a name="example-queries"></a>Ukázkové dotazy

#### <a name="example-1"></a>Příklad 1

Následující dotaz vrátí doby trvání pro každou koncovou událost konce/aktualizace dotazu pro databázi modelu a server. Pokud škálování out, výsledky jsou rozděleny podle repliky, protože číslo repliky je součástí ServerName_s. Seskupení podle RootActivityId_g snižuje počet řádků načtených z rozhraní API REST diagnostiky Azure a pomáhá zůstat v mezích, jak je popsáno v [omezeních míry analýzy protokolů](https://dev.loganalytics.io/documentation/Using-the-API/Limits).

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

Následující dotaz vrátí paměť a qpu spotřeby pro server. Pokud škálování out, výsledky jsou rozděleny podle repliky, protože číslo repliky je součástí ServerName_s.

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

Následující dotaz vrátí čítače výkonu modulu Analysis Services rows/sec Analysis Services pro server.

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

Existují stovky dotazů, které můžete použít. Další informace o dotazech najdete [v tématu Začínáme s dotazy protokolu Azure Monitor](../azure-monitor/log-query/get-started-queries.md).


## <a name="turn-on-logging-by-using-powershell"></a>Zapnutí protokolování pomocí PowerShellu

V tomto rychlém kurzu vytvoříte účet úložiště ve stejné skupině předplatného a prostředků jako server služby Analysis Service. Potom pomocí Set-AzDiagnosticSetting zapnout protokolování diagnostiky, odesílání výstupu do nového účtu úložiště.

### <a name="prerequisites"></a>Požadavky
Chcete-li dokončit tento kurz, musíte mít následující zdroje:

* Existující server Služby Azure Analysis Services. Pokyny k vytvoření serverového prostředku najdete [v tématu Vytvoření serveru na webu Azure Portal](analysis-services-create-server.md)nebo Vytvoření serveru [Služby Azure analysis services pomocí PowerShellu](analysis-services-create-powershell.md).

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

Pokud pak určíte předplatné, které je přidružené k účtu Služby Azure Analysis Services, který právě zaznamenáváte, zadejte:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Pokud máte k vašemu účtu přidruženo více předplatných, je důležité předplatné zadat.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Vytvořit pro svoje protokoly nový účet úložiště

Pro protokoly můžete použít existující účet úložiště za předpokladu, že je ve stejném předplatném jako váš server. V tomto kurzu vytvoříte nový účet úložiště vyhrazený pro protokoly služby Analysis Services. Chcete-li to usnadnit, ukládáte podrobnosti o účtu úložiště do proměnné s názvem **sa**.

Můžete také použít stejnou skupinu prostředků jako ten, který obsahuje server Analysis Services. Nahraďte `awsales_resgroup` `awsaleslogs`hodnoty `West Central US` pro , a vlastními hodnotami:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identifikace účtu serveru pro protokoly

Nastavte název účtu na proměnnou s názvem **účet**, kde ResourceName je název účtu.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Povolit protokolování

Chcete-li povolit protokolování, použijte rutinu Set-AzDiagnosticSetting společně s proměnnými pro nový účet úložiště, účet serveru a kategorii. Spusťte následující příkaz a nastavíte příznak **-Enabled** na **$true**:

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

Tento výstup potvrzuje, že protokolování je nyní povoleno pro server, ukládání informací do účtu úložiště.

Můžete také nastavit zásady uchovávání informací pro protokoly tak, aby starší protokoly byly automaticky odstraněny. Například nastavte zásady uchovávání informací pomocí **příznaku RetentionEnabled** na **$true**a nastavte parametr **-RetentionInDays** na **90**. Protokoly starší než 90 dnů jsou automaticky odstraněny.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Další kroky

Další informace o [protokolování diagnostiky prostředků Azure](../azure-monitor/platform/platform-logs-overview.md).

Viz [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) v nápovědě prostředí PowerShell.
