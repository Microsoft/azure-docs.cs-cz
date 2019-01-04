---
title: Diganostic protokolování pro službu Azure Analysis Services | Dokumentace Microsoftu
description: Další informace o nastavení protokolování diagnostiky pro Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f2e0767e78119b224f7be9256ab7050deca37e32
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53546976"
---
# <a name="setup-diagnostic-logging"></a>Nastavení protokolování diagnostiky

Důležitou součástí řešení služby Analysis Services je monitorování, jaký výkon vaše servery. S [diagnostické protokoly Azure prostředků](../azure-monitor/platform/diagnostic-logs-overview.md), můžete sledovat a odeslat protokoly s [služby Azure Storage](https://azure.microsoft.com/services/storage/), Streamovat je do [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)a exportovat je do [protokolu Analytics](https://azure.microsoft.com/services/log-analytics/), službě [Azure](https://www.microsoft.com/cloud-platform/operations-management-suite). 

![Protokolování diagnostiky do úložiště, Event Hubs nebo Log Analytics](./media/analysis-services-logging/aas-logging-overview.png)


## <a name="whats-logged"></a>Co je protokolováno?

Můžete vybrat **modul**, **služby**, a **metriky** kategorií.

### <a name="engine"></a>Modul

Výběr **modul** všechny protokoly [xEvents](https://docs.microsoft.com/sql/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Nelze vybrat jednotlivé události. 

|Událost XEvent s kategorií |Název události  |
|---------|---------|
|Audit zabezpečení    |   Auditovat přihlášení      |
|Audit zabezpečení    |   Auditovat odhlášení      |
|Audit zabezpečení    |   Audit serveru spustí a zastaví      |
|Sestavy o průběhu     |   Začátek sestavy průběhu      |
|Sestavy o průběhu     |   Konec sestavy průběhu      |
|Sestavy o průběhu     |   Aktuální sestava průběhu      |
|Dotazy     |  Začátek dotazu       |
|Dotazy     |   Konec dotazu      |
|Příkazy     |  Příkaz Begin       |
|Příkazy     |  Příkaz End       |
|Chyby a upozornění     |   Chyba      |
|Informace     |   Objevte koncové      |
|Oznámení     |    Oznámení     |
|Relace     |  Inicializace relace       |
|Zámky    |  Vzájemné zablokování       |
|Zpracování dotazů     |   VertiPaq SE Query Begin      |
|Zpracování dotazů     |   VertiPaq SE Query End      |
|Zpracování dotazů     |   VertiPaq SE Query mezipaměti shody      |
|Zpracování dotazů     |   Začátek Directquery      |
|Zpracování dotazů     |  Konec dotazu s přímým přístupem       |

### <a name="service"></a>Služba

|Název operace  |Nastane, když  |
|---------|---------|
|ResumeServer     |    Obnovení serveru     |
|SuspendServer    |   Pozastavení serveru      |
|DeleteServer     |    Odstranění serveru     |
|RestartServer    |     Uživatel restartuje server prostřednictvím aplikace SSMS nebo Powershellu    |
|GetServerLogFiles    |    Uživatel exportuje protokolu serveru pomocí prostředí PowerShell     |
|ExportModel     |   Uživatel vyexportuje model na portálu s použitím otevřít v sadě Visual Studio     |

### <a name="all-metrics"></a>Všechny metriky

Kategorie metriky protokoly stejné [metrik serveru](analysis-services-monitor.md#server-metrics) zobrazí v metrikách.

## <a name="setup-diagnostics-logging"></a>Nastavení protokolování diagnostiky

### <a name="azure-portal"></a>portál Azure

1. V [webu Azure portal](https://portal.azure.com) > server, klikněte na tlačítko **diagnostické protokoly** v levém navigačním panelu a pak klikněte na tlačítko **zapnout diagnostiku**.

    ![Zapnutí protokolování diagnostiky pro Azure Cosmos DB na webu Azure Portal](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. V **nastavení diagnostiky**, určete následující možnosti: 

    * **Název**. Zadejte název pro protokoly a vytvořit.

    * **Archivovat do účtu úložiště**. Pokud chcete použít tuto možnost, musíte se připojit k existující účet úložiště. Zobrazit [vytvořit účet úložiště](../storage/common/storage-create-storage-account.md). Postupujte podle pokynů k vytvoření správce prostředků, účet pro obecné účely, pak vyberte svůj účet úložiště tak, že vrací na tuto stránku na portálu. Může trvat několik minut, než se nově vytvořené účty se zobrazí v rozevírací nabídce.
    * **Stream do centra událostí**. Pokud chcete použít tuto možnost, budete potřebovat existující centrum událostí oboru názvů a Centrum událostí pro připojení k. Další informace najdete v tématu [vytvořit obor názvů služby Event Hubs a centra událostí pomocí webu Azure portal](../event-hubs/event-hubs-create.md). Pak se vraťte na tuto stránku na portálu vyberte název oboru názvů a zásad centra událostí.
    * **Odeslání do Log Analytics**. Pokud chcete použít tuto možnost, použít stávající pracovní prostor nebo vytvořit nový pracovní prostor Log Analytics pomocí následujících kroků na [vytvořit nový pracovní prostor](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace) na portálu. Další informace o prohlížení protokolů v Log Analytics najdete v tématu [zobrazení protokolů v Log Analytics](#view-logs-in-log-analytics) v tomto článku.

    * **Modul**. Tato možnost protokolování xEvents. Pokud jste archivovat do účtu úložiště, můžete vybrat dobu uchování diagnostických protokolů. Protokoly jsou autodeleted po vypršení platnosti doby uchování.
    * **Služba**. Tuto možnost, do protokolu událostí na úrovni služby. Pokud jsou archivovat do účtu úložiště, můžete vybrat dobu uchování diagnostických protokolů. Protokoly jsou autodeleted po vypršení platnosti doby uchování.
    * **Metriky**. Tuto možnost použijte k ukládání podrobné údaje v [metriky](analysis-services-monitor.md#server-metrics). Pokud jsou archivovat do účtu úložiště, můžete vybrat dobu uchování diagnostických protokolů. Protokoly jsou autodeleted po vypršení platnosti doby uchování.

3. Klikněte na **Uložit**.

    Pokud se zobrazí chybová zpráva "nepovedlo se aktualizovat diagnostiku pro \<název pracovního prostoru >. Předplatné \<id předplatného > není zaregistrované k používání microsoft.insights. " postupujte podle [Poradce při potížích s Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) pokyny k registraci účtu, a poté opakujte tento postup.

    Pokud chcete změnit, jak diagnostické protokoly jsou uloženy v libovolném okamžiku v budoucnu, můžete vrátit na tuto stránku a upravit nastavení.

### <a name="powershell"></a>PowerShell

Tady jsou základní příkazy pro vám pomůže se zorientovat. Pokud chcete podrobné informace o nastavení protokolování do účtu úložiště pomocí prostředí PowerShell, najdete v kurzu dále v tomto článku.

Chcete-li povolit protokolování pomocí prostředí PowerShell Diagnostika a metriky, použijte následující příkazy:

- Pokud chcete povolit úložiště pro diagnostické protokoly v účtu úložiště, použijte tento příkaz:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   ID účtu úložiště je ID prostředku účtu úložiště, ve které chcete odeslat protokoly.

- Pokud chcete povolit streamování protokolů diagnostiky do centra událostí, použijte tento příkaz:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   ID pravidla služby Azure Service Bus je řetězec v tomto formátu:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Povolení odesílání protokolů diagnostiky k pracovnímu prostoru Log Analytics, použijte tento příkaz:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- ID prostředku pracovního prostoru Log Analytics můžete získat pomocí následujícího příkazu:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Tyto parametry pro povolení více možností výstupu můžete kombinovat.

### <a name="rest-api"></a>REST API

Zjistěte, jak [změnit nastavení diagnostiky pomocí REST API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Šablona Resource Manageru

Zjistěte, jak [povolení nastavení diagnostiky při vytváření prostředků pomocí šablony Resource Manageru](../azure-monitor/platform/diagnostic-logs-stream-template.md). 

## <a name="manage-your-logs"></a>Správa protokolů

Protokoly jsou obvykle dostupné během pár hodin od nastavení protokolování. Správa protokolů v účtu úložiště je pouze na vás:

* Zabezpečte protokoly pomocí standardních metod řízení přístupu Azure a určete, kdo k nim má přístup.
* Odstraňte protokoly, které už nechcete uchovávat v účtu úložiště.
* Ujistěte se, že nastavíte takovou dobu uchování tak, aby se staré protokoly odstraní z vašeho účtu úložiště.

## <a name="view-logs-in-log-analytics"></a>Zobrazení protokolů ve službě Log Analytics

Události metriky a serveru jsou integrované s xEvents v Log Analytics k analýze vedle sebe. Log Analytics můžete nakonfigurovat také přijímat události z dalších služeb Azure poskytuje holistické zobrazení diagnostických dat protokolování napříč vaší architektuře.

Zobrazení diagnostických dat v Log Analytics, otevřete stránku prohledávání protokolů z levé nabídky nebo oblasti správy, jak je znázorněno níže.

![Možnosti protokolu hledání na webu Azure Portal](./media/analysis-services-logging/aas-logging-open-log-search.png)

Teď, když jste povolili shromažďování dat v **prohledávání protokolů**, klikněte na tlačítko **všechna data shromážděná**.

V **typ**, klikněte na tlačítko **AzureDiagnostics**a potom klikněte na tlačítko **použít**. AzureDiagnostics zahrnuje události stroj a služby. Všimněte si, že dotaz Log Analytics se vytvoří v chodu. EventClass\_s pole obsahuje událost xEvent s názvy, které může vypadat povědomě, pokud jste použili xEvents pro místní protokolování.

Klikněte na tlačítko **EventClass\_s** nebo jeden z názvů událostí a Log Analytics bude pokračovat sestavování dotazu. Nezapomeňte uložit své dotazy pro pozdější použití.

Ujistěte se, že najdete v Log Analytics, která poskytuje web s mnoha, výstrah možnostmi shromážděných dat a dotazování rozšířené.

### <a name="queries"></a>Dotazy

Existují stovky dotazy, které můžete použít. Tady je pár vám pomůžou začít.
Další informace o používání nového dotazovacího jazyka prohledávání protokolů, najdete v článku [Principy prohledávání protokolů v Log Analytics](../log-analytics/log-analytics-log-search-new.md). 

* Dotaz vrátí dotazy odeslané do služby Azure Analysis Services pro dokončení trvalo víc než pět minut (300 000 milisekund).

    ```
    search * | where ( Type == "AzureDiagnostics" ) | where ( EventClass_s == "QUERY_END" ) | where toint(Duration_s) > 300000
    ```

* Identifikujte horizontální navýšení kapacity replik.

    ```
    search * | summarize count() by ServerName_s
    ```
    Při použití horizontální navýšení kapacity, můžete identifikovat repliky jen pro čtení, protože název_serveru\_mají hodnoty pole s připojeným k názvu číslo instance repliky. Pole prostředků obsahuje název prostředku Azure, který by odpovídal názvu serveru, který uživatelé uvidí. Pole IsQueryScaleoutReadonlyInstance_s rovná se true pro repliky.



> [!TIP]
> Máte skvělý dotazu Log Analytics, který chcete sdílet? Pokud máte účet Githubu, přidáte ji k tomuto článku. Stačí kliknout na **upravit** v pravé horní části této stránky.


## <a name="tutorial---turn-on-logging-by-using-powershell"></a>Kurz – zapnout protokolování s využitím Powershellu
V tomto rychlém kurzu vytvoříte účet úložiště ve stejném předplatném a skupině prostředků jako váš server Analysis Service. Pak pomocí Set-AzureRmDiagnosticSetting zapnete diagnostiku protokolování, odesílání výstupu pro nový účet úložiště.

### <a name="prerequisites"></a>Požadavky
K dokončení tohoto kurzu musíte mít následující prostředky:

* Stávající server Azure Analysis Services. Pokyny k vytvoření prostředku serveru, najdete v článku [vytvoření serveru na webu Azure portal](analysis-services-create-server.md), nebo [vytvoření serveru Azure Analysis Services pomocí prostředí PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Připojení k předplatným

Spusťte relaci Azure PowerShellu a přihlaste se k účtu Azure pomocí následujícího příkazu:  

```powershell
Connect-AzureRmAccount
```

V automaticky otevřeném okně prohlížeče zadejte svoje uživatelské jméno a heslo k účtu Azure. Azure PowerShell získá všechna předplatná přidružená k tomuto účtu a ve výchozím nastavení použije první předplatné.

Máte-li více předplatných, možná budete muset zadat předplatné, které jste použili pro vytvoření Azure Key Vault. Chcete-li zobrazit předplatná vašeho účtu, zadejte následující:

```powershell
Get-AzureRmSubscription
```

Chcete-li specifikovat předplatné přidružené k účtu služby Azure Analysis Services, které se přihlašujete, zadejte:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Pokud máte více předplatných, které jsou spojené s vaším účtem, je potřeba zadat předplatné.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Vytvořit pro svoje protokoly nový účet úložiště

Pro svoje protokoly můžete použít existující účet úložiště, pokud je ve stejném předplatném jako váš server. Pro účely tohoto kurzu vytvoříte nový účet úložiště vyhrazený pro protokoly služby Analysis Services. Abyste usnadnili snadnou, ukládáte podrobnosti o účtu úložiště do proměnné s názvem **sa**.

Rovněž použijte stejnou skupinu prostředků jako ten, který obsahuje váš server Analysis Services. Nahraďte hodnoty `awsales_resgroup`, `awsaleslogs`, a `West Central US` vlastními hodnotami:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identifikovat účet, který server pro svoje protokoly

Nastavte název účtu do proměnné s názvem **účet**, kde ResourceName je název účtu.

```powershell
$account = Get-AzureRmResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Povolit protokolování

Povolit protokolování, použijte rutinu Set-AzureRmDiagnosticSetting spolu s proměnnými pro nový účet úložiště, účet serveru a kategorii. Spuštěním následujícího příkazu nastavení **-povoleno** příznak **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

Výstup by měl vypadat přibližně takto:

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

Tím potvrdíte, že je nyní povoleno protokolování pro server ukládá informace do účtu úložiště.

Můžete také nastavit zásady uchovávání informací pro svoje protokoly, tak starší protokoly automaticky odstraněny. Například nastavení zásad uchovávání informací pomocí **- RetentionEnabled** příznak **$true**a nastavte **- RetentionInDays** parametr **90**. Protokoly starší než 90 dnů automaticky odstraněny.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Další postup

Další informace o [protokolování diagnostiky prostředků Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

Zobrazit [Set-AzureRmDiagnosticSetting](https://docs.microsoft.com/powershell/module/azurerm.insights/Set-AzureRmDiagnosticSetting) v nápovědě k prostředí PowerShell.
