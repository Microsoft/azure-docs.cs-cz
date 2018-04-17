---
title: Diganostic protokolování pro Azure Analysis Services | Microsoft Docs
description: Informace o nastavení protokolování diagnostiky pro Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 12f745958130e931bc3c8e81a0a61f3c3f4c4e3c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="setup-diagnostic-logging"></a>Nastavení protokolování diagnostiky

Důležitou součást řešení služby Analysis Services je monitorování výkonu vašich serverů. S [diagnostické protokoly prostředků Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md), můžete monitorovat a odeslat protokoly, které [Azure Storage](https://azure.microsoft.com/services/storage/), jejich stream [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)a exportovat je do [protokolu Analýza](https://azure.microsoft.com/services/log-analytics/), služby z [Azure](https://www.microsoft.com/cloud-platform/operations-management-suite). 

![Protokolování diagnostiky úložišť, Event Hubs nebo analýzy protokolů](./media/analysis-services-logging/aas-logging-overview.png)


## <a name="whats-logged"></a>Co je protokolováno?

Můžete vybrat **modul**, **služby**, a **metriky** kategorií.

### <a name="engine"></a>Modul

Výběr **modul** všechny protokoly [xEvents](https://docs.microsoft.com/sql/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Nelze vybrat jednotlivé události. 

|Kategorie XEvent |Název události  |
|---------|---------|
|Audit zabezpečení    |   Audit přihlášení      |
|Audit zabezpečení    |   Odhlášení auditu      |
|Audit zabezpečení    |   Audit Server spuštění a zastavení      |
|Sestavy průběhu     |   Probíhá počáteční sestavy      |
|Sestavy průběhu     |   End sestava průběhu      |
|Sestavy průběhu     |   Aktuální sestavy průběhu      |
|Dotazy     |  Dotaz na začátek       |
|Dotazy     |   End dotazu      |
|Příkazy     |  Příkaz Begin       |
|Příkazy     |  Příkaz End       |
|Chyby a upozornění     |   Chyba      |
|Informace     |   Zjišťovat koncový      |
|Oznámení     |    Oznámení     |
|Relace     |  Inicializace relace       |
|Zámky    |  zablokování       |
|Zpracování dotazu     |   VertiPaq SE Query začátek      |
|Zpracování dotazu     |   VertiPaq SE Query End      |
|Zpracování dotazu     |   VertiPaq SE Query mezipaměti shody      |
|Zpracování dotazu     |   Začátek přímý dotaz      |
|Zpracování dotazu     |  Přímý dotaz End       |

### <a name="service"></a>Služba

|Název operace  |Nastane při  |
|---------|---------|
|CreateGateway     |   Uživatel nakonfiguruje bránu na serveru      |
|ResumeServer     |    Obnovení serveru     |
|SuspendServer    |   Pozastavení serveru      |
|DeleteServer     |    Odstranění serveru     |
|RestartServer    |     Restartuje uživatel serveru přes SSMS nebo prostředí PowerShell    |
|GetServerLogFiles    |    Uživatel exportuje protokolu serveru pomocí prostředí PowerShell     |
|ExportModel     |   Uživatel exportuje model v portálu pomocí otevřete v sadě Visual Studio     |

### <a name="all-metrics"></a>Všechny metriky

Kategorie metrik protokoly stejné [Server metriky](analysis-services-monitor.md#server-metrics) zobrazí v metriky.

## <a name="setup-diagnostics-logging"></a>Nastavení protokolování diagnostiky

### <a name="azure-portal"></a>Azure Portal

1. V [portál Azure](https://portal.azure.com) > server, klikněte na tlačítko **diagnostické protokoly** v levém navigačním panelu a pak klikněte na tlačítko **zapněte diagnostiku**.

    ![Zapnutí protokolování diagnostiky pro Azure Cosmos DB na portálu Azure](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. V **nastavení pro diagnostiku**, určete následující možnosti: 

    * **Název**. Zadejte název pro protokoly vytvořit.

    * **Archiv na účet úložiště**. Pokud chcete použít tuto možnost, musíte se připojit k existující účet úložiště. V tématu [vytvořit účet úložiště](../storage/common/storage-create-storage-account.md). Postupujte podle pokynů pro vytvoření správce prostředků, pro obecné účely účtu, potom vyberte svůj účet úložiště, vraťte se na této stránce na portálu. To může trvat několik minut pro účty nově vytvořené úložiště zobrazí v rozevírací nabídce.
    * **Datový proud do centra událostí**. Chcete-li použít tuto možnost, musíte existující centra událostí obor názvů a události rozbočovače pro připojení k. Další informace najdete v tématu [vytvořit obor názvů Event Hubs a centra událostí pomocí portálu Azure](../event-hubs/event-hubs-create.md). Pak se vraťte na tuto stránku v portálu a zvolte název oboru názvů a zásad centra událostí.
    * **Odeslat k analýze protokolů**. Chcete-li použít tuto možnost, buď použijte existujícímu pracovnímu prostoru nebo vytvořte nový pracovní prostor analýzy protokolů podle pokynů k [vytvořit nový pracovní prostor](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) na portálu. Další informace o prohlížení protokolů v analýzy protokolů, najdete v části [zobrazení přihlásí analýzy protokolů](#view-in-loganalytics).

    * **Modul**. Vyberte tuto možnost, chcete-li protokolu události Xevent. Pokud jste se archivace na účet úložiště, můžete vybrat dobu uchování diagnostické protokoly. Protokoly jsou autodeleted po uplynutí období uchovávání informací.
    * **Služba**. Vyberte tuto možnost, do protokolu událostí na úrovni služby. Pokud vytváříte archivu účet úložiště, můžete vybrat dobu uchování diagnostické protokoly. Protokoly jsou autodeleted po uplynutí období uchovávání informací.
    * **Metriky**. Tuto možnost vyberte pro uložení podrobné dat v [metriky](analysis-services-monitor.md#server-metrics). Pokud vytváříte archivu účet úložiště, můžete vybrat dobu uchování diagnostické protokoly. Protokoly jsou autodeleted po uplynutí období uchovávání informací.

3. Klikněte na **Uložit**.

    Pokud obdržíte chybu, která uvádí, že "se nepodařilo aktualizovat diagnostiku pro \<název pracovního prostoru >. Předplatné \<id předplatného > není zaregistrované pro používání microsoft.insights. " postupujte podle [řešení potíží s Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) pokyny k registraci účtu, opakujte tento postup.

    Pokud chcete změnit, jak diagnostické protokoly jsou uloženy v libovolném bodě v budoucnosti, můžete se vrátit k této stránce můžete upravit nastavení.

### <a name="powershell"></a>PowerShell

Zde jsou základní příkazy si přejdete. Pokud chcete podrobné nápovědu k nastavení protokolování na účet úložiště pomocí prostředí PowerShell, najdete v tomto kurzu později v tomto článku.

Metriky a diagnostiku pomocí prostředí PowerShell protokolování povolit, použijte následující příkazy:

- Pokud chcete povolit úložiště diagnostiky protokolů v účtu úložiště, použijte tento příkaz:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   ID účtu úložiště je ID prostředku pro účet úložiště, kde chcete odeslat protokoly.

- Pokud chcete povolit, streamování protokolů diagnostiky do centra událostí, použijte tento příkaz:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   ID pravidla Azure Service Bus je řetězec s Tento formát:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- K povolení odesílání protokolů diagnostiky do pracovního prostoru analýzy protokolů, použijte tento příkaz:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- ID prostředku pracovní prostor analýzy protokolů můžete získat pomocí následujícího příkazu:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Tyto parametry povolení více možností výstupu můžete kombinovat.

### <a name="rest-api"></a>REST API

Zjistěte, jak [změnit nastavení diagnostiky pomocí rozhraní REST API Azure monitorování](https://msdn.microsoft.com/library/azure/dn931931.aspx). 

### <a name="resource-manager-template"></a>Šablona Resource Manageru

Zjistěte, jak [povolte nastavení diagnostiky při vytváření prostředků pomocí šablony Resource Manageru](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="manage-your-logs"></a>Správa protokolů

Protokoly jsou obvykle dostupné v rámci nastavení protokolování několik hodin. Správa protokolů v účtu úložiště je pouze na vás:

* Zabezpečte protokoly pomocí standardních metod řízení přístupu Azure a určete, kdo k nim má přístup.
* Odstraňte protokoly, které už nechcete uchovávat v účtu úložiště.
* Nezapomeňte nastavit období uchování tak starých protokolů jsou odstraněny z vašeho účtu úložiště.

## <a name="view-logs-in-log-analytics"></a>Zobrazit protokoly v analýzy protokolů

Události metriky a serveru jsou integrované s xEvents v analýzy protokolů pro analýzu vedle sebe. Analýzy protokolů můžete také nakonfigurovat pro příjem událostí z jiných služeb systému Azure poskytuje ucelený přehled o diagnostických protokolování dat napříč vaší architektury.

K zobrazení diagnostických dat v analýzy protokolů, otevřete stránku hledání protokolů z v levé nabídce nebo oblasti Správa, jak je uvedeno níže.

![Možnosti hledání protokolu na portálu Azure](./media/analysis-services-logging/aas-logging-open-log-search.png)

Teď, když jste povolili shromažďování dat v **hledání protokolů**, klikněte na tlačítko **všechny shromážděné data**.

V **typ**, klikněte na tlačítko **AzureDiagnostics**a potom klikněte na **použít**. AzureDiagnostics zahrnuje události modulu a služby. Upozornění analýzy protokolů dotazu se vytvoří v chodu. EventClass\_pole s obsahuje xEvent názvy, které může vypadat povědomě, pokud jste použili xEvents pro místní protokolování.

Klikněte na tlačítko **EventClass\_s** nebo jeden z názvy událostí a analýzy protokolů pokračuje vytváření dotazu. Je nutné uložit vaše dotazy pro pozdější použití.

Ujistěte se, že analýzy protokolů, které poskytuje rozšířené dotazu, dashboarding a v shromážděná data výstrah funkce webu v tématu.

### <a name="queries"></a>Dotazy

Existují stovky dotazy, které můžete použít. Tady je několik které vám pomůžou začít.
Další informace o používání nové dotazovacího jazyka pro hledání protokolů najdete v tématu [principy protokolu vyhledá v analýzy protokolů](../log-analytics/log-analytics-log-search-new.md). 

* Dotaz vrátí dotazy odeslána Azure Analysis Services, kterou trvalo dokončení více než pět minut (300 000 milisekund).

    ```
    search * | where ( Type == "AzureDiagnostics" ) | where ( EventClass_s == "QUERY_END" ) | where toint(Duration_s) > 300000
    ```

* Identifikujte škálování repliky.

    ```
    search * | summarize count() by ServerName_s
    ```
    Při použití Škálováním na více systémů, můžete identifikovat jen pro čtení replik, protože název serveru\_hodnoty pole s mají čísla instance repliky připojeným k názvu. Toto pole prostředků obsahuje název prostředku Azure, který odpovídá názvu serveru, který se zobrazí uživatelům. Pole IsQueryScaleoutReadonlyInstance_s hodnotu PRAVDA pro repliky.



> [!TIP]
> Máte skvělé dotazu analýzy protokolů, které chcete sdílet? Pokud máte účet Githubu, můžete ho přidat do tohoto článku. Stačí kliknout na **upravit** v pravé horní části této stránky.


## <a name="tutorial---turn-on-logging-by-using-powershell"></a>Kurz – zapněte protokolování pomocí prostředí PowerShell
V tomto kurzu rychle vytvořit účet úložiště ve stejném předplatném a skupině prostředků jako server služby Analysis Service. Potom pomocí Set-AzureRmDiagnosticSetting zapnete diagnostiku protokolování, odeslání výstupu do nového účtu úložiště.

### <a name="prerequisites"></a>Požadavky
K dokončení tohoto kurzu, musí mít následující prostředky:

* Existujícího serveru Azure Analysis Services. Pokyny týkající se vytváření prostředků serveru najdete v tématu [na portálu Azure vytvořit server](analysis-services-create-server.md), nebo [vytvoření serveru Azure Analysis Services pomocí prostředí PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Připojení ke svým předplatným

Spusťte relaci Azure PowerShellu a přihlaste se k účtu Azure pomocí následujícího příkazu:  

```powershell
Login-AzureRmAccount
```

V automaticky otevřeném okně prohlížeče zadejte svoje uživatelské jméno a heslo k účtu Azure. Azure PowerShell získá všechna předplatná přidružená k tomuto účtu a ve výchozím nastavení použije první předplatné.

Máte-li více předplatných, možná budete muset zadat předplatné, které jste použili pro vytvoření Azure Key Vault. Chcete-li zobrazit předplatná vašeho účtu, zadejte následující:

```powershell
Get-AzureRmSubscription
```

Poté zadejte předplatné, který je spojen s účet Azure Analysis Services, který se přihlašujete, zadejte:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Pokud máte více předplatných, které jsou spojené s vaším účtem, je důležité pro určení předplatného.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Vytvořit pro svoje protokoly nový účet úložiště

Pro svoje protokoly můžete použít existující účet úložiště, zadaná ve stejném předplatném jako váš server. V tomto kurzu vytvoříte nový účet úložiště vyhrazený pro protokoly služby Analysis Services. Chcete-li snadno, ukládáte podrobnosti o účtu úložiště do proměnné s názvem **sa**.

Můžete také použít stejné skupině prostředků jako ta, která obsahuje váš server služby Analysis Services. Nahraďte hodnoty `awsales_resgroup`, `awsaleslogs`, a `West Central US` vlastními hodnotami:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identifikace účtu serveru pro svoje protokoly

Nastavte název účtu do proměnné s názvem **účet**, kde ResourceName je název účtu.

```powershell
$account = Get-AzureRmResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Povolit protokolování

Protokolování povolit, použijte rutinu Set-AzureRmDiagnosticSetting spolu s proměnnými, pro nový účet úložiště, účet serveru a kategorii. Spusťte následující příkaz, nastavení **-povolit** příznak, který **$true**:

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

Potvrdíte, že je nyní povoleno protokolování pro server, uložení informací k účtu úložiště.

Můžete také nastavit zásady uchovávání informací pro svoje protokoly tak starší protokoly automaticky odstraněny. Například nastavit pomocí zásad uchovávání **- RetentionEnabled** příznak, který **$true**a nastavte **- RetentionInDays** parametru **90**. Protokoly, které jsou starší než 90 dní automaticky odstraněny.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Další postup

Další informace o [protokolování diagnostiky prostředků Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

V tématu [Set-AzureRmDiagnosticSetting](https://docs.microsoft.com/powershell/module/azurerm.insights/Set-AzureRmDiagnosticSetting) v nápovědě k prostředí PowerShell.