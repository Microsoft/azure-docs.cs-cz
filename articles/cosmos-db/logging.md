---
title: "Protokolování diagnostiky Azure Cosmos DB | Microsoft Docs"
description: "Pomocí tohoto kurzu můžete začít pracovat s Azure Cosmos DB protokolování."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: mimig
ms.openlocfilehash: f647387b4e80c36339a456b8e9a2cfade7ac8102
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="azure-cosmos-db-diagnostic-logging"></a>Protokolování diagnostiky Azure Cosmos DB

Po zahájení pomocí jedné nebo více databází Azure Cosmos DB, můžete chtít sledovat jak a kdy se k nim přistupuje vaší databáze. Tento článek obsahuje přehled všechny protokoly, které jsou k dispozici na platformě Azure, pak vysvětluje, jak povolit protokolování diagnostiky pro účely k odeslání protokolů k monitorování [Azure Storage](https://azure.microsoft.com/services/storage/), jejich stream [Azure Event Hubs ](https://azure.microsoft.com/services/event-hubs/), a exportovat je do [analýzy protokolů](https://azure.microsoft.com/services/log-analytics/), který je součástí [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite).

## <a name="logs-available-in-azure"></a>Protokoly, které jsou k dispozici v Azure

Předtím, než se nám získat do monitorování účtu Azure Cosmos DB, umožňuje vysvětlení pár věcí o protokolování a monitorování. Existují různé typy protokolů na platformě Azure. Existují [protokoly aktivity Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [diagnostických protokolů Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), [metriky](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), události, prezenčního signálu monitorování protokolů operations atd. Existují nadbytku protokoly. Zobrazí úplný seznam protokolů v [Azure Log Analytics](https://azure.microsoft.com/en-us/services/log-analytics/) na portálu Azure. 

Následující obrázek ukazuje různé druhy Azure k dispozici protokoly.

![Různé druhy Azure protokoly](./media/logging/azurelogging.png)

Na obrázku výše **výpočetní prostředky** představují prostředky Azure, pro které je přístupné hostovaného operačního systému. Například sadách škálování virtuálních počítačů k virtuální počítače Azure, Azure Container Service atd se považuje za výpočetní prostředky. Výpočetní prostředky generovat protokoly aktivity, diagnostické protokoly a protokoly aplikací. Další informace naleznete [Azure Monitoring – výpočetní prostředky](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#azure-monitor-sources---compute-subset) článku.

**Bez výpočetní prostředky** jsou prostředky, kde nelze získat přístup k podkladové operačního systému a pracovat přímo s prostředků. Například skupiny zabezpečení sítě, logiku aplikace atd. **Cosmos DB** je jiný výpočtový prostředek. Protokoly pro jiný výpočetní prostředky můžete zobrazit v protokolu aktivit nebo když zapnete možnost diagnostické protokoly na portálu. Další informace naleznete [Azure Monitoring – bez výpočetní prostředky](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#azure-monitor-sources---everything-else) článku.

Protokol aktivit zaznamenává operace na úrovni předplatného pro Cosmos databáze, operace, jako jsou protokolovány ListKeys, zápis DatabaseAccounts atd. Zadejte podrobnější protokolování diagnostických protokolů a umožňuje protokolu DataPlaneRequests (vytvořit, číst, dotazu... ) a MongoRequests.


Tato diskuse umožňuje zaměřit se na aktivita služby Azure, Azure Diagnotic a metriky. Jaký je rozdíl mezi tyto tři protokoly? 

### <a name="azure-activity-log"></a>Protokol činnosti Azure

Protokol činnosti Azure je protokol odběru, který poskytuje přehled o události na úrovni předplatného, k nimž došlo v Azure. Protokol aktivit hlásí události rovině řízení pro vaše předplatná v administrativní kategorii. Pomocí protokolu činnosti, můžete určit ', kdo a kdy se pro všechny zápisu operace (PUT, POST, DELETE) na prostředky v rámci vašeho předplatného. Můžete také chápou stav operace a další relevantní vlastnosti. 

Protokol aktivity se liší od diagnostické protokoly. Protokoly aktivity poskytují data týkající se operací na prostředku z vnějšku ("řízení rovinou"). V rámci Azure Cosmos DB některé z plochy ovládacího prvku, ve které operace zahrnují vytvořit kolekci, výpis klíčů, odstranění klíče, seznamu databáze atd. Diagnostické protokoly jsou vygenerované prostředek a poskytnout informace o operaci prostředku ("data rovinou"). Některé příklady protokolů diagnostiky roviny data budou odstranění, vložení, operace readfeed atd.

Protokoly aktivity (operace ovládacího prvku roviny) může být mnohem víc bohatší ve své podstatě, mohou zahrnovat úplné e-mailovou adresu volajícího, volající IP adresu, název prostředku, název operace, a TenantId, atd. Protokol aktivity obsahuje několik [kategorie](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) dat. Úplné podrobnosti na schémata z těchto kategorií najdete v tématu [schématu události protokol činnosti Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema).  Diagnostické protokoly však může být omezující ve své podstatě, jak často se odstraní PII data a z nich. Ano může mít IP adresu volajícího, ale je odebrán poslední octent.

### <a name="azure-metrics"></a>Azure metriky

[Azure metriky](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-metrics), nejdůležitější typ Azure telemetrická data (také nazývané čítače výkonu) vysílaných prostředků nejvíce Azure. Metriky umožňují zobrazit informace o propustnosti, úložiště, konzistence, dostupnosti a latence vašich prostředků Azure Cosmos DB. Další informace najdete v části [sledování a ladění pomocí metriky v Azure Cosmos DB](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Azure diagnostických protokolů

Azure diagnostické protokoly jsou protokoly vygenerované prostředek a poskytují bohatou a často data o operaci prostředku. Obsah tyto protokoly se liší podle typu prostředku. Diagnostické protokoly na úrovni prostředků se také liší od hostovaného operačního systému na úrovni diagnostických protokolů. Diagnostické protokoly hostovaného operačního systému jsou tyto shromažďují agenta spuštěného v rámci virtuálního počítače nebo jiné podporované typ prostředku. Diagnostické protokoly na úrovni prostředků vyžadují specifické pro zdroje dat z Azure k samotné platformě nástroje, žádné agenta a zachycení, zatímco hostovaného operačního systému na úrovni diagnostických protokolů zaznamenání dat z operačního systému a aplikací, které běží na virtuálním počítači.

![Protokolování diagnostiky úložišť, Event Hubs nebo Operations Management Suite prostřednictvím analýzy protokolů](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Co je protokolováno diagnostické protokoly Azure?

* Všechny žádosti o ověření back-end (TCP/REST), mezi všechna rozhraní API, přihlášení, které zahrnuje i neúspěšné požadavky v důsledku oprávnění k přístupu, systémových chyb nebo chybných požadavků. Graf, Cassandra, zahájí se podpora pro uživatele a žádostí o rozhraní API tabulky nejsou momentálně k dispozici.
* Operace na samotná databáze, která zahrnuje operace CRUD na všechny dokumenty, kontejnery a databází.
* Operace na klíče účtu, které zahrnují vytváření, upravování a odstraňování těchto klíčů.
* Neověřené požadavky, které skončí odpovědí 401 – Neoprávněno. Například požadavky, které nemají nosný token, jsou poškozené nebo jejichž platnost vypršela, nebo mají neplatný token.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Zapnout protokolování na portálu Azure

Pokud chcete povolit protokolování diagnostiky, musíte mít následující prostředky:

* Existující databázi Cosmos Azure účet, databázi a kontejneru. Pokyny k vytváření těchto prostředků najdete v tématu [vytvoření databázového účtu pomocí portálu Azure](create-sql-api-dotnet.md#create-a-database-account), [ukázky rozhraní příkazového řádku](cli-samples.md), nebo [ukázky PowerShell](powershell-samples.md).

Pokud chcete povolit protokolování diagnostiky na portálu Azure, postupujte takto:

1. V [portál Azure](https://portal.azure.com), ve vašem Azure Cosmos DB účet, klikněte na **diagnostické protokoly** v levém navigačním panelu a pak klikněte na tlačítko **zapněte diagnostiku**.

    ![Zapnutí protokolování diagnostiky pro Azure Cosmos DB na portálu Azure](./media/logging/turn-on-portal-logging.png)

2. V **nastavení pro diagnostiku** proveďte následující: 

    * **Název**. Zadejte název pro protokoly vytvořit.

    * **Archiv na účet úložiště**. Pokud chcete použít tuto možnost, musíte se připojit k existující účet úložiště. Pokud chcete vytvořit nový účet úložiště na portálu, najdete v části [vytvořit účet úložiště](../storage/common/storage-create-storage-account.md) a postupujte podle pokynů pro vytvoření správce prostředků, účtu pro obecné účely. Pak se vraťte na tuto stránku na portálu vyberte svůj účet úložiště. To může trvat několik minut pro účty nově vytvořené úložiště zobrazí v rozevírací nabídce.
    * **Datový proud do centra událostí**. Chcete-li použít tuto možnost, musíte existující centra událostí obor názvů a události rozbočovače pro připojení k. Vytvoření oboru názvů Event Hubs naleznete v tématu [vytvořit obor názvů Event Hubs a centra událostí pomocí portálu Azure](../event-hubs/event-hubs-create.md). Pak se vraťte na tuto stránku v portálu a zvolte název oboru názvů a zásad centra událostí.
    * **Odeslat k analýze protokolů**.     Chcete-li použít tuto možnost, buď použijte existujícímu pracovnímu prostoru nebo vytvořte nový pracovní prostor analýzy protokolů podle pokynů k [vytvořit nový pracovní prostor](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) na portálu. Další informace o prohlížení protokolů v analýzy protokolů, najdete v části [zobrazení přihlásí analýzy protokolů](#view-in-loganalytics).
    * **Přihlaste se DataPlaneRequests**. Vyberte tuto možnost, chcete-li protokolovat požadavky na back-end ze základní distribuované platformy Azure Cosmos DB pro SQL, grafu, MongoDB, Cassandra a Table API účty. Pokud vytváříte archivu účet úložiště, můžete vybrat dobu uchování diagnostické protokoly. Protokoly jsou autodeleted po uplynutí období uchovávání informací.
    * **Přihlaste se MongoRequests**. Vyberte tuto možnost, chcete-li protokolovat požadavky na iniciované uživatelem z front-endu pro obsluhující MongoDB API účty Azure Cosmos DB.  Pokud vytváříte archivu účet úložiště, můžete vybrat dobu uchování diagnostické protokoly. Protokoly jsou autodeleted po uplynutí období uchovávání informací.
    * **Metriky požadavky**. Tuto možnost vyberte pro uložení podrobné dat v [metrik Azure](../monitoring-and-diagnostics/monitoring-supported-metrics.md). Pokud vytváříte archivu účet úložiště, můžete vybrat dobu uchování diagnostické protokoly. Protokoly jsou autodeleted po uplynutí období uchovávání informací.

3. Klikněte na **Uložit**.

    Pokud obdržíte chybu, která uvádí, že "se nepodařilo aktualizovat diagnostiku pro \<název pracovního prostoru >. Předplatné \<id předplatného > není zaregistrované pro používání microsoft.insights. " postupujte podle [řešení potíží s Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) pokyny k registraci účtu, opakujte tento postup.

    Pokud chcete změnit, jak diagnostické protokoly jsou uloženy v libovolném bodě v budoucnosti, můžete se vrátit k této stránce na kdykoli změnit nastavení diagnostiky protokolu pro váš účet.

## <a name="turn-on-logging-using-cli"></a>Zapnout protokolování pomocí rozhraní příkazového řádku

Chcete-li povolit metrik a protokolování diagnostiky pomocí rozhraní příkazového řádku Azure, použijte následující příkazy:

- Pokud chcete povolit ukládání diagnostických protokolů v účtu úložiště, použijte tento příkaz:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   `resourceId` Je název účtu Azure Cosmos DB. `storageId` Je název účtu úložiště, do kterého chcete odeslat protokoly.

- Pokud chcete povolit vysílání datového proudu diagnostických protokolů do centra událostí, použijte tento příkaz:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   `resourceId` Je název účtu Azure Cosmos DB. `serviceBusRuleId` Je řetězec s Tento formát:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Pokud chcete povolit odesílání diagnostických protokolů do pracovního prostoru analýzy protokolů, použijte tento příkaz:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Tyto parametry povolení více možností výstupu můžete kombinovat.

## <a name="turn-on-logging-using-powershell"></a>Zapnout protokolování pomocí prostředí PowerShell

Zapnutí protokolování diagnostiky pomocí prostředí PowerShell, musíte na minimální verzi 1.0.1 prostředí Azure Powershell.

Chcete-li nainstalovat Azure PowerShell a přidružit ho ke svému předplatnému Azure, prohlédněte si téma [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

Pokud jste již Azure PowerShell nainstalovali ale neznáte jeho verzi, z konzoly prostředí PowerShell, zadejte `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Připojení k předplatným
Spusťte relaci Azure PowerShellu a přihlaste se k účtu Azure pomocí následujícího příkazu:  

```powershell
Login-AzureRmAccount
```

V automaticky otevřeném okně prohlížeče zadejte svoje uživatelské jméno a heslo k účtu Azure. Azure PowerShell získá všechna předplatná přidružená k tomuto účtu a ve výchozím nastavení použije první předplatné.

Máte-li více předplatných, možná budete muset zadat předplatné, které jste použili pro vytvoření Azure Key Vault. Chcete-li zobrazit předplatná vašeho účtu, zadejte následující:

```powershell
Get-AzureRmSubscription
```

Poté zadejte předplatné, který je spojen s Azure Cosmos DB účet, kterým se přihlašujete, zadejte:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Pokud máte více předplatných, které jsou spojené s vaším účtem je důležité pro určení předplatného.
>
>

Další informace o konfiguraci prostředí Azure PowerShell najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Vytvoření nového účtu úložiště pro protokoly
Přestože je možné použít existující účet úložiště pro svoje protokoly, v tomto kurzu vytvoříme nový účet úložiště vyhrazený pro protokoly Azure Cosmos DB. Pro usnadnění práce jsme jsou ukládání podrobnosti o účtu úložiště do proměnné s názvem **sa**.

Pro další usnadnění správy v tomto kurzu používáme stejné skupině prostředků jako ten, který obsahuje databázi naše Azure Cosmos DB. Nahraďte hodnoty pro ContosoResourceGroup, contosocosmosdblogs a '– Sever střední USA, pro vlastní hodnoty, podle vhodnosti:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Pokud se rozhodnete použít existující účet úložiště, musí používat stejné předplatné jako vaše předplatné Azure Cosmos DB a musí používat model nasazení Resource Manager, nikoli model nasazení Classic.
>
>

### <a id="identify"></a>Identifikace účtu Azure Cosmos DB pro svoje protokoly
Nastavte název účtu Azure Cosmos DB do proměnné s názvem **účet**, kde ResourceName je název účtu Azure Cosmos DB.

```powershell
$account = Get-AzureRmResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Povolení protokolování
Povolení protokolování pro Azure Cosmos DB, použijte rutinu Set-AzureRmDiagnosticSetting spolu s proměnnými pro nový účet úložiště, účet Azure Cosmos DB a kategorie, pro který chcete povolit protokoly. Spusťte následující příkaz, nastavení **-povolit** příznak, který **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

Výstup příkazu by měl vypadat takto:

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

Tím potvrdí, že protokolování je teď povolené pro vaši databázi, uložení informací k vašemu účtu úložiště.

Volitelně můžete pro své protokoly nastavit také zásady uchovávání informací tak, aby se starší protokoly automaticky odstraňovaly. Například po nastavení zásad uchovávání informací pomocí příznaku **-RetentionEnabled** nastaveného na **$true** a parametru **-RetentionInDays** nastaveného na **90** budou protokoly starší než 90 dnů automaticky odstraněny.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Přístup k protokolům
Azure Cosmos DB protokolech **DataPlaneRequests** kategorie jsou uložené v **insights protokoly data roviny žádosti** kontejneru v účtu úložiště, který jste zadali. 

Nejprve vytvořte proměnnou pro název kontejneru. Bude se používat po celý zbytek návodu.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Chcete-li vypsat všechny objekty blob v tomto kontejneru, zadejte:

```powershell
Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Výstup bude vypadat nějak takto:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

Jak je vidět na výpisu, objekty BLOB podle zásady vytváření názvů: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Hodnoty data a času používají UTC.

Protože je možné použít stejný účet úložiště k shromažďování protokolů více zdrojů, je velmi užitečná chcete získat nebo stáhnout pouze určité objekty BLOB, které je třeba ID plně kvalifikovaný prostředku v názvu objektu blob. Ale předtím se podíváme na to, jak stáhnout všechny objekty blob.

Nejprve vytvořte složku, kam stáhnete objekty blob. Příklad:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Poté získejte seznam všech objektů blob:  

```powershell
$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Předejte tento seznam prostřednictvím „GetAzureStorageBlobContent“ pro stažení objektů blob do naší cílové složky:

```powershell
$blobs | Get-AzureStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

Když spustíte tento druhý příkaz,  **/**  oddělovač v názvech objektů blob vytvoří úplnou strukturu složek v cílové složce. Tato struktura složky se používá ke stažení a uložení objektů BLOB jako soubory.

Chcete-li stahovat objekty blob selektivně, použijte zástupné znaky. Příklad:

* Pokud máte více databází a chcete ke stažení protokolů pro právě jednu databázi, s názvem CONTOSOCOSMOSDB3:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Máte-li více skupin prostředků a chcete stáhnout pouze protokoly pro jednu skupinu prostředků, použijte `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Pokud chcete stáhnout všechny protokoly pro měsíc 2017 července, použijte `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Navíc platí:

* Dotaz na stav nastavení diagnostiky pro prostředek databáze: `Get-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
* Chcete-li zakázat protokolování **DataPlaneRequests** kategorie prostředku účet databáze: `Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`


Objekty BLOB, které jsou vráceny v každé z těchto dotazů jsou uloženy jako text ve formátu JSON blob, jak je znázorněno v následujícím kódu. 

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

Další informace o data v jednotlivých objektů blob JSON najdete v tématu [interpretace protokolů služby Azure Cosmos DB](#interpret).

## <a name="managing-your-logs"></a>Správa protokolů

Diagnostické protokoly jsou k dispozici ve vašem účtu dvou hodin od okamžiku, kdy byla provedena operace Azure Cosmos DB. Správa protokolů v účtu úložiště je pouze na vás:

* Zabezpečte protokoly pomocí standardních metod řízení přístupu Azure a určete, kdo k nim má přístup.
* Odstraňte protokoly, které už nechcete uchovávat v účtu úložiště.
* Doba uchování dat roviny požadavky archivovat na účet úložiště je nakonfigurován na portálu při **DataPlaneRequests protokolu** je vybrána. Chcete-li změnit toto nastavení, [zapnout protokolování na portálu Azure](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-log-analytics"></a>Zobrazit protokoly v analýzy protokolů

Pokud jste vybrali **odeslat k analýze protokolů** možnost při zapnutí protokolování diagnostiky diagnostických dat z kolekce se předají k analýze protokolů během dvou hodin. To znamená, že se podíváte na analýzy protokolů bezprostředně po zapnutí protokolování, neuvidíte žádná data. Právě dvě hodiny počkejte a zkuste to znovu. 

Před zobrazením protokolů, budete chtít zkontrolovat, pokud pracovní prostor analýzy protokolů byl upgradován na použití nového dotazu jazyka analýzy protokolů. Zaškrtněte toto políčko, otevřete [portál Azure](https://portal.azure.com), klikněte na tlačítko **analýzy protokolů** na daleko levou stranu a pak vyberte název pracovního prostoru jak je znázorněno na následujícím obrázku. **Pracovním prostorem OMS** stránky se zobrazí, jak je znázorněno na následujícím obrázku.

![Analýzy protokolů na portálu Azure](./media/logging/azure-portal.png)

Pokud se zobrazí následující zprávu na **pracovním prostorem OMS** stránky, pracovní prostor nebyla upgradována, aby použití nového jazyka. Další informace o upgradu na novou dotazovací jazyk, najdete v části [upgradu pracovní prostor analýzy protokolů Azure na nové hledání protokolu](../log-analytics/log-analytics-log-search-upgrade.md). 

![Log analytics upgradu oznámení](./media/logging/upgrade-notification.png)

K zobrazení diagnostických dat v analýzy protokolů, otevřete stránku hledání protokolů z v levé nabídce nebo oblasti Správa stránky, jak je znázorněno na následujícím obrázku.

![Možnosti hledání protokolu na portálu Azure](./media/logging/log-analytics-open-log-search.png)

Teď, když povolíte shromažďování dat, spusťte následující příklad vyhledávání protokolu pomocí nového dotazu jazyka, zobrazíte deset nejnovější protokoly `AzureDiagnostics | take 10`.

![Ukázka trvat 10 hledání protokolů](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Dotazy

Tady jsou některé další dotazy, můžete zadat do **hledání protokolů** pole, které vám pomohou monitorovat kontejnerů Azure Cosmos DB. Tyto dotazy pracovat [nový jazyk](../log-analytics/log-analytics-log-search-upgrade.md). 

Další informace o význam dat vrácených hledání jednotlivých protokolů najdete v tématu [interpretace protokolů služby Azure Cosmos DB](#interpret).

* Všechny diagnostické protokoly z Azure Cosmos databáze pro zadané časové období.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Deset většinu poslední protokolované události.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Všechny operace seskupená podle typu operace.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Všechny operace, seskupené podle prostředků.

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Všechny aktivity uživatelů, seskupené podle prostředků. Všimněte si, že se jedná o aktivitu protokolu, není protokolů diagnostiky.

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Které operace trvat déle než 3 milisekundách.

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Které agenta je spuštěna operace.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Pokud byly provedeny dlouhotrvající operace.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

Další informace o použití nového jazyka hledání protokolů najdete v tématu [principy protokolu vyhledá v analýzy protokolů](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>Interpretace protokolů služby

Diagnostická data uložená v Azure Storage a analýzy protokolů pomocí velmi podobné schématu. 

Následující tabulka popisuje obsah každé položky protokolu.

| Úložiště Azure pole nebo vlastnost | Vlastnosti analýzy protokolů | Popis |
| --- | --- | --- |
| time | TimeGenerated | Datum a čas (UTC) při operaci došlo k chybě. |
| resourceId | Prostředek | Účet Azure Cosmos DB, pro které jsou povolené protokoly.|
| category | Kategorie | Pro Azure Cosmos DB protokoly DataPlaneRequests je k dispozici pouze hodnota. |
| operationName | OperationName | Název operace. Tato hodnota může být libovolná z následujících operací: vytvoření, aktualizace, čtení, ReadFeed, odstranění, nahraďte, spouštění, SqlQuery, dotazu, JSQuery, Head, HeadFeed nebo Upsert.   |
| properties | neuvedeno | Obsah tohoto pole jsou popsané v následující řádky. |
| ID aktivity | activityId_g | Jedinečný identifikátor GUID pro protokolovaných operací. |
| UserAgent | userAgent_s | Řetězec, který určuje uživatelský agent klienta provádění požadavku. Formát je {uživatelské jméno agenta} / {version}.|
| resourceType | ResourceType | Typ prostředku, u níž. Tato hodnota může být jakýkoli z následujících typů prostředků: databáze, kolekce, dokument, přílohy, uživatele, oprávnění, StoredProcedure, aktivační události, UserDefinedFunction nebo nabídku. |
| statusCode |statusCode_s | Stav odpovědi operace. |
| requestResourceId | ID prostředku | ResourceId týkající se žádosti, mohou odkazovat databaseRid, collectionRid nebo documentRid v závislosti na operaci provést.|
| clientIpAddress | clientIpAddress_s | IP adresa klienta. |
| requestCharge | requestCharge_s | Počet RUs používané operaci |
| collectionRid | collectionId_s | Jedinečné ID pro kolekci.|
| Doba trvání | duration_s | Doba trvání operace v rysky. |
| requestLength | requestLength_s | Délka požadavku v bajtech. |
| responseLength | responseLength_s | Délka odpovědi v bytech.|
| resourceTokenUserRid | resourceTokenUserRid_s | Toto je neprázdný při [prostředků tokeny](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) slouží k ověřování a bodů pro ID prostředku uživatele. |

## <a name="next-steps"></a>Další postup

- Získat představu o není pouze to, jak povolit protokolování, ale také podporuje různé Azure kategorie metrik a protokolu služby přečíst, i [přehled metriky v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) a [přehled Azure Diagnostické protokoly](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) články.
- Přečtěte si další informace o službě event hubs tyto články:
   - [Co je Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Začínáme s Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Čtení [stáhnout metriky a diagnostické protokoly ze služby Azure Storage](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)
- Čtení [principy protokolu vyhledá v analýzy protokolů](../log-analytics/log-analytics-log-search-new.md)
