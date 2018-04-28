---
title: Protokolování diagnostiky Azure Cosmos DB | Microsoft Docs
description: Pomocí tohoto kurzu můžete začít pracovat s Azure Cosmos DB protokolování.
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
tags: azure-resource-manager
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: sngun
ms.openlocfilehash: 6bad6c7cd641a2ef5461ee37b89c0bdc6a221017
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="azure-cosmos-db-diagnostic-logging"></a>Protokolování diagnostiky Azure Cosmos DB

Jakmile začnete používat jednu nebo více databází Azure Cosmos DB, můžete chtít sledovat jak a kdy se k nim přistupuje vaší databáze. Tento článek obsahuje přehled protokolů, které jsou dostupné na platformě Azure. Informace o povolení protokolování diagnostiky pro účely k odeslání protokolů k monitorování [Azure Storage](https://azure.microsoft.com/services/storage/), jak k vysílání datového proudu protokolů [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)a exportování protokoly [Azure Log Analytics ](https://azure.microsoft.com/services/log-analytics/).

## <a name="logs-available-in-azure"></a>Protokoly, které jsou k dispozici v Azure

Před mluvíme o tom, jak sledovat účtu Azure Cosmos DB, můžeme vysvětlení pár věcí o protokolování a monitorování. Existují různé typy protokolů na platformě Azure. Existují [protokoly aktivity Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [diagnostických protokolů Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), [Azure metriky](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), události, monitorování prezenčního signálu, protokoly operací a tak dále. Není nadbytku protokoly. Zobrazí seznam všech protokolů v [Azure Log Analytics](https://azure.microsoft.com/en-us/services/log-analytics/) na portálu Azure. 

Následující obrázek ukazuje různé druhy Azure protokoly, které jsou k dispozici:

![Různé druhy Azure protokoly](./media/logging/azurelogging.png)

Na obrázku **výpočetní prostředky** představují prostředky Azure, pro které je přístupné Microsoft hostovaného operačního systému. Například virtuální počítače Azure, virtuální počítač škálování sad, Azure Container Service a atd., jsou považovány výpočetní prostředky. Výpočetní prostředky generovat protokoly aktivity, diagnostické protokoly a protokoly aplikací. Další informace naleznete [Azure Monitoring: výpočetní prostředky](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#azure-monitor-sources---compute-subset) článku.

**Non-výpočetní prostředky** jsou prostředky, ve kterých nelze získat přístup k podkladové operačního systému a pracovat přímo s prostředku. Například skupiny zabezpečení sítě, Logic Apps a tak dále. Azure Cosmos DB je jiný výpočetní prostředek. Můžete zobrazit protokoly pro jiný výpočetní prostředky v protokolu aktivit nebo povolte možnost diagnostické protokoly na portálu. Další informace naleznete [Azure Monitoring: bez výpočetní prostředky](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#azure-monitor-sources---everything-else) článku.

Protokol aktivit zaznamenává operace na úrovni předplatného pro Azure Cosmos DB. Operace, jako je ListKeys, zápis DatabaseAccounts a další přihlášeni. Diagnostické protokoly poskytují podrobnější protokolování a umožňují DataPlaneRequests (vytvořit, číst, dotazů a tak dále) a MongoRequests protokolu.


V tomto článku se zaměříme na protokol činnosti Azure, Azure diagnostické protokoly a Azure metriky. Jaký je rozdíl mezi tyto tři protokoly? 

### <a name="azure-activity-log"></a>Protokol činnosti Azure

Protokol činnosti Azure je protokol odběru, který poskytuje přehled o události na úrovni předplatného, k nimž došlo v Azure. Protokol aktivit hlásí události rovině řízení pro vaše předplatná v administrativní kategorii. Protokol aktivit můžete použít k určení ", kdo a kdy" pro všechny zapsání operace (PUT, POST, DELETE) s prostředky v rámci vašeho předplatného. Můžete také chápou stav operace a další relevantní vlastnosti. 

Protokol aktivity se liší od diagnostické protokoly. Protokol aktivit poskytuje data o operacích na prostředku z vnějšku ( _rovině řízení_). V Azure Cosmos DB kontextu řízení plochy, ve které operace zahrnují vytvořit kolekci, výpis klíčů, odstranění klíče, seznamu databáze a tak dále. Diagnostické protokoly jsou vygenerované prostředek a poskytují informace o operaci prostředku ( _datové roviny_). Některé příklady operace roviny data v protokolů diagnostiky jsou ReadFeed, odstranění a vložení.

Protokoly aktivity (operace ovládacího prvku roviny) může být bohatší ve své podstatě a může obsahovat úplnou e-mailovou adresu volající, volající IP adresu, název prostředku, název operace, TenantId a další. Protokol aktivity obsahuje několik [kategorie](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) dat. Úplné podrobnosti na schémata z těchto kategorií najdete v tématu [schématu události protokol činnosti Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema). Diagnostické protokoly však může být omezující ve své podstatě jako PII dat je často odstraní z těchto protokolů. Můžete mít IP adresu volajícího, ale je odebrán poslední octant.

### <a name="azure-metrics"></a>Azure metriky

[Azure metriky](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-metrics) nejdůležitější typ Azure telemetrická data (také nazývané _čítače výkonu_) je vysílaných prostředků nejvíce Azure. Metriky umožňují zobrazit informace o propustnosti, úložiště, konzistence, dostupnosti a latence vašich prostředků Azure Cosmos DB. Další informace najdete v tématu [sledování a ladění pomocí metriky v Azure Cosmos DB](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Azure diagnostických protokolů

Azure diagnostické protokoly jsou vygenerované prostředek a poskytují bohatou a často data o operaci prostředku. Obsah tyto protokoly se liší podle typu prostředku. Diagnostické protokoly na úrovni prostředků se také liší od diagnostické protokoly na úrovni operačního systému hosta. Diagnostické protokoly hostovaného operačního systému se shromažďují pomocí agenta, který běží uvnitř virtuálního počítače nebo jiné podporované typ prostředku. Diagnostické protokoly na úrovni prostředků vyžadují specifické pro zdroje dat z Azure k samotné platformě nástroje žádné agenta a zachycení. Diagnostické protokoly úrovni operačního systému hosta zaznamenání dat z operačního systému a aplikací, které jsou spuštěny na virtuálním počítači.

![Protokolování diagnostiky úložišť, Event Hubs nebo analýzy protokolů](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Co je protokolováno diagnostické protokoly Azure?

* Přihlášení se všechny ověřené back-end požadavky (TCP/REST) v rámci všechna rozhraní API, včetně neúspěšných požadavků v důsledku oprávnění k přístupu, systémových chyb nebo chybných požadavků. Podpora pro uživatel spustil grafu, Cassandra a Table API požadavky nejsou aktuálně k dispozici.
* Operací na sama, databáze, které zahrnují operace CRUD na všechny dokumenty, kontejnery a databází.
* Operace na klíče účtu, které zahrnují vytváření, úpravy nebo odstranění klíče.
* Neověřené požadavky, které skončí odpovědí 401 – Neoprávněno. Například požadavky, které nemají nosný token, nebo jsou poškozené nebo jejichž platnost vypršela nebo mají neplatný token.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Zapnout protokolování na portálu Azure

Pokud chcete povolit protokolování diagnostiky, musíte mít následující prostředky:

* Existující databázi Cosmos Azure účet, databázi a kontejneru. Pokyny k vytváření těchto prostředků najdete v tématu [vytvoření databázového účtu pomocí portálu Azure](create-sql-api-dotnet.md#create-a-database-account), [ukázky rozhraní příkazového řádku Azure](cli-samples.md), nebo [ukázky PowerShell](powershell-samples.md).

Chcete-li povolit protokolování diagnostiky na portálu Azure, proveďte následující kroky:

1. V [portál Azure](https://portal.azure.com), ve vašem Azure Cosmos DB účtu, vyberte **diagnostické protokoly** v levém navigačním panelu a potom vyberte **zapněte diagnostiku**.

    ![Zapnutí protokolování diagnostiky pro Azure Cosmos DB na portálu Azure](./media/logging/turn-on-portal-logging.png)

2. V **nastavení pro diagnostiku** proveďte následující kroky: 

    * **Název**: Zadejte název pro protokoly vytvořit.

    * **Archiv na účet úložiště**: Chcete-li použít tuto možnost, musíte stávající účet úložiště pro připojení k. Pokud chcete vytvořit nový účet úložiště na portálu, najdete v části [vytvořit účet úložiště](../storage/common/storage-create-storage-account.md) a postupujte podle pokynů pro vytvoření správce prostředků Azure pro obecné účely účtu. Pak se vraťte na tuto stránku na portálu vyberte svůj účet úložiště. Může trvat několik minut pro účty nově vytvořené úložiště zobrazí v rozevírací nabídce.
    * **Datový proud do centra událostí**: Chcete-li použít tuto možnost, musíte existující Event Hubs obor názvů a události rozbočovače pro připojení k. Vytvoření oboru názvů Event Hubs naleznete v tématu [na obor názvů služby Event Hubs a centra událostí vytvořit pomocí portálu Azure](../event-hubs/event-hubs-create.md). Pak se vraťte na tuto stránku v portálu a zvolte název oboru názvů a zásady služby Event Hubs.
    * **Odeslat k analýze protokolů**: Chcete-li použít tuto možnost, buď použijte existujícímu pracovnímu prostoru nebo vytvořte nový pracovní prostor analýzy protokolů podle pokynů k [vytvořit nový pracovní prostor](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) na portálu. Další informace o prohlížení protokolů v analýzy protokolů najdete v tématu [zobrazení přihlásí analýzy protokolů](#view-in-loganalytics).
    * **Přihlaste se DataPlaneRequests**: Tato možnost protokolování back-end požadavků od základní distribuované platformy Azure Cosmos DB pro SQL, grafu, MongoDB, Cassandra a Table API účty. Pokud jste se archivace na účet úložiště, můžete vybrat dobu uchování diagnostické protokoly. Protokoly jsou automaticky odstraněna po uplynutí období uchovávání informací.
    * **Přihlaste se MongoRequests**: Tato možnost protokolování požadavků zahájená uživatelem z Azure Cosmos DB front-end pro obsluhující MongoDB API účty. Pokud jste se archivace na účet úložiště, můžete vybrat dobu uchování diagnostické protokoly. Protokoly jsou automaticky odstraněna po uplynutí období uchovávání informací.
    * **Metriky požadavky**: tuto možnost vyberte pro uložení podrobné dat v [Azure metriky](../monitoring-and-diagnostics/monitoring-supported-metrics.md). Pokud jste se archivace na účet úložiště, můžete vybrat dobu uchování diagnostické protokoly. Protokoly jsou automaticky odstraněna po uplynutí období uchovávání informací.

3. Vyberte **Uložit**.

    Pokud obdržíte chybu, která uvádí, že "se nepodařilo aktualizovat diagnostiku pro \<název pracovního prostoru >. Předplatné \<id předplatného > není zaregistrované pro používání microsoft.insights, "postupujte podle [řešení potíží s Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) pokyny k registraci účtu a opakujte tento postup.

    Pokud chcete změnit, jak diagnostické protokoly jsou uloženy v libovolném bodě v budoucnu, vraťte se na této stránce můžete upravit nastavení protokolů diagnostiky pro váš účet.

## <a name="turn-on-logging-by-using-azure-cli"></a>Zapnutí protokolování pomocí rozhraní příkazového řádku Azure

Povolení metrik a protokolování diagnostiky pomocí rozhraní příkazového řádku Azure, použijte následující příkazy:

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

## <a name="turn-on-logging-by-using-powershell"></a>Zapnutí protokolování pomocí prostředí PowerShell

Zapnutí protokolování diagnostiky pomocí prostředí PowerShell, musíte na minimální verzi 1.0.1 prostředí Azure Powershell.

Chcete-li nainstalovat Azure PowerShell a přidružit ho ke svému předplatnému Azure, prohlédněte si téma [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

Pokud jste již Azure PowerShell nainstalovali a neznáte jeho verzi, z typu konzole PowerShell `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Připojení k předplatným
Spusťte relaci Azure PowerShellu a přihlaste se k účtu Azure pomocí následujícího příkazu:  

```powershell
Connect-AzureRmAccount
```

V automaticky otevřeném okně prohlížeče zadejte svoje uživatelské jméno a heslo k účtu Azure. Prostředí Azure PowerShell získá všechna předplatná, která jsou přidružená k tomuto účtu a ve výchozím nastavení, použije první.

Pokud máte více než jedno předplatné, můžete chtít zadat konkrétní předplatné, která byla použita k vytvoření trezoru klíčů Azure. K zobrazení předplatných pro váš účet, zadejte následující příkaz:

```powershell
Get-AzureRmSubscription
```

Poté zadejte předplatné, který je spojen s Azure Cosmos DB účet, který jste protokolování, zadejte následující příkaz:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Pokud máte více než jedno předplatné, který je spojen s vaším účtem, je potřeba zadat předplatné, které chcete použít.
>
>

Další informace o tom, jak nakonfigurovat Azure PowerShell najdete v tématu [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Vytvoření nového účtu úložiště pro protokoly
Přestože je možné použít existující účet úložiště pro svoje protokoly v tomto kurzu, vytvoříme nový účet úložiště, který je vyhrazen pro Azure Cosmos DB protokoly. Pro usnadnění práce, uložíme podrobnosti o účtu úložiště v proměnné s názvem **sa**.

Pro další usnadnění správy, v tomto kurzu používáme stejné skupině prostředků jako ten, který obsahuje databázi Azure Cosmos DB. Dosaďte svoje hodnoty **ContosoResourceGroup**, **contosocosmosdblogs**, a **– Sever střední USA** parametry, jako je použít:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Pokud se rozhodnete použít existující účet úložiště, účet musí používat stejné předplatné jako vaše předplatné Azure Cosmos DB. Účet musíte taky použít v modelu nasazení Resource Manager, nikoli modelu nasazení classic.
>
>

### <a id="identify"></a>Identifikace účtu Azure Cosmos DB pro svoje protokoly
Nastavte název účtu Azure Cosmos DB do proměnné s názvem **účet**, kde **ResourceName** je název účtu Azure Cosmos DB.

```powershell
$account = Get-AzureRmResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Povolení protokolování
Chcete-li povolit protokolování pro Azure Cosmos DB, použijte `Set-AzureRmDiagnosticSetting` rutiny s proměnných pro nový účet úložiště, účet Azure Cosmos DB a kategorii, kterou chcete povolit pro protokolování. Spusťte následující příkaz a nastavte **-povolit** příznak, který **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

Výstup příkazu by měl vypadat následující ukázka:

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

Výstup z tohoto příkazu potvrdí, že je nyní povoleno protokolování pro vaši databázi a informace se ukládají do vašeho účtu úložiště.

Volitelně můžete také nastavit zásady uchovávání informací pro svoje protokoly tak, aby starší protokoly automaticky odstraněny. Například nastavit zásady uchovávání informací pomocí **- RetentionEnabled** příznak nastaven na hodnotu **$true**. Nastavte **- RetentionInDays** parametru **90** tak, aby se protokoly, které jsou starší než 90 dní automaticky odstraní.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Přístup k protokolům
Azure Cosmos DB v protokolech **DataPlaneRequests** kategorie jsou uložené v **insights protokoly data roviny žádosti** kontejneru v účtu úložiště, který jste zadali. 

Nejprve vytvořte proměnnou pro název kontejneru. Proměnná se používá napříč návodu.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Seznam všech objektů BLOB v tomto kontejneru, zadejte:

```powershell
Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Výstup příkazu by měl vypadat následující ukázka:

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

Protože stejný účet úložiště je možné použít ke shromažďování protokolů více prostředků, můžete použít ID prostředku plně kvalifikovaný v názvu objektu blob a přístup k určité objekty BLOB, které je nutné stáhnout. Než to, jak stáhnout všechny objekty BLOB nabídneme.

Nejprve vytvořte složku, kam stáhnete objekty blob. Příklad:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Poté získejte seznam všech objektů blob:  

```powershell
$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Tento seznam prostřednictvím kanálu `Get-AzureStorageBlobContent` příkaz ke stažení objektů BLOB do cílové složky:

```powershell
$blobs | Get-AzureStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

Když spustíte tento druhý příkaz, **/** oddělovač v názvech objektů blob vytvoří úplnou strukturu složek v cílové složce. Tato struktura složky se používá ke stažení a uložení objektů BLOB jako soubory.

Chcete-li stahovat objekty blob selektivně, použijte zástupné znaky. Příklad:

* Pokud máte více databází a chcete ke stažení protokolů pro právě jednu databázi s názvem **CONTOSOCOSMOSDB3**, použijte příkaz:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Pokud máte více skupin prostředků a chcete stáhnout protokoly pro jenom jedné skupiny prostředků, použijte příkaz `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Pokud chcete stáhnout všechny protokoly pro měsíc 2017 července, použijte příkaz `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Můžete taky spustit následující příkazy:

* Dotaz na stav nastavení diagnostiky pro prostředek vaší databáze, použijte příkaz `Get-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`.
* Chcete-li zakázat protokolování **DataPlaneRequests** kategorie pro váš účet prostředek databáze, použijte příkaz `Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`.


Objekty BLOB, které jsou vráceny v každé z těchto dotazů jsou uloženy jako text a formátu JSON blob, jak je znázorněno v následujícím kódu:

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

## <a name="manage-your-logs"></a>Správa protokolů

Diagnostické protokoly jsou k dispozici ve vašem účtu pro dvou hodin od času, která byla provedena operace Azure Cosmos DB. Správa protokolů v účtu úložiště je pouze na vás:

* Pomocí standardních metod řízení přístupu Azure Zabezpečte protokoly a můžete omezit, kteří k nim měli přístup.
* Odstraňte protokoly, které už nechcete uchovávat v účtu úložiště.
* Doba uchování dat roviny požadavků, které jsou archivovány na účet úložiště je nakonfigurován na portálu při **DataPlaneRequests protokolu** je vybráno nastavení. Chcete-li změnit toto nastavení, [zapnout protokolování na portálu Azure](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-log-analytics"></a>Zobrazit protokoly v analýzy protokolů

Pokud jste vybrali **odeslat k analýze protokolů** možnost při zapnutí protokolování diagnostiky diagnostických dat z kolekce se předají k analýze protokolů během dvou hodin. Když se podíváte na analýzy protokolů ihned po zapnutí protokolování, můžete se nezobrazí žádná data. Právě dvě hodiny počkejte a zkuste to znovu. 

Můžete zobrazit protokoly, zkontrolujte a v tématu, pokud pracovní prostor analýzy protokolů byl upgradován na použití nového dotazu jazyka analýzy protokolů. Chcete-li zkontrolovat, otevřete [portál Azure](https://portal.azure.com), vyberte **analýzy protokolů** vkládá zcela vlevo, pak vyberte název pracovního prostoru, viz následující obrázek. **Pracovním prostorem OMS** zobrazí se stránka:

![Analýzy protokolů na portálu Azure](./media/logging/azure-portal.png)

Pokud se zobrazí následující zprávu na **pracovním prostorem OMS** stránky, pracovní prostor nebyla upgradována, aby použití nového jazyka. Další informace o tom, jak upgradovat na nový dotaz jazyka najdete v tématu [upgradu pracovní prostor analýzy protokolů Azure na nové hledání protokolu](../log-analytics/log-analytics-log-search-upgrade.md). 

![Upgrade zpráva analýzy protokolů](./media/logging/upgrade-notification.png)

Chcete-li zobrazit diagnostických dat v analýzy protokolů, otevřete **hledání protokolů** stránky v levé nabídce nebo **správy** oblast stránky, jak je znázorněno na následujícím obrázku:

![Možnosti hledání protokolu na portálu Azure](./media/logging/log-analytics-open-log-search.png)

Teď, když jste povolili shromažďování dat, spusťte následující příklad vyhledávání protokolu pomocí nové dotazovací jazyk zobrazíte 10 nejnovější protokoly `AzureDiagnostics | take 10`.

![Ukázka protokolu hledat 10 nejnovější protokoly](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Dotazy

Tady jsou některé další dotazy, které můžete zadat do **hledání protokolů** pole, které vám pomohou monitorovat kontejnerů Azure Cosmos DB. Tyto dotazy pracovat [nový jazyk](../log-analytics/log-analytics-log-search-upgrade.md). 

Další informace o význam data, která se vrátí po hledání jednotlivých protokolů najdete v tématu [interpretace protokolů služby Azure Cosmos DB](#interpret).

* Dotaz pro všechny diagnostické protokoly z Azure Cosmos databáze pro zadané časové období:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* K dotazování 10 nejvíce nedávno protokolují události:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Dotaz pro všechny operace, seskupené podle typ operace:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Dotaz pro všechny operace, seskupené podle **prostředků**:

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Dotaz pro všechny aktivity uživatelů, seskupené podle prostředků:

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
    > [!NOTE]
    > Tento příkaz je pro aktivitu protokolu, není protokolů diagnostiky.

* K dotazování, pro kterou operace trvat déle než 3 milisekundách:

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* K dotazování, pro kterého agenta je spuštěn operace:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Dotaz pro, pokud byly provedeny dlouhotrvající operace:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

Další informace o tom, jak používat nový jazyk hledání protokolů najdete v tématu [Rady pro pochopení protokolu hledání v analýzy protokolů](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>Interpretace protokolů služby

Diagnostická data, která je uložená v Azure Storage a analýzy protokolů používá podobné schéma. 

Následující tabulka popisuje obsah každé položky protokolu.

| Azure Storage pole nebo vlastnost | Vlastnosti analýzy protokolů | Popis |
| --- | --- | --- |
| **Čas** | **TimeGenerated** | Datum a čas (UTC) při operaci došlo k chybě. |
| **ID prostředku** | **Prostředek** | Účet Azure Cosmos DB, pro které jsou povolené protokoly.|
| **Kategorie** | **Kategorie** | Pro Azure Cosmos DB protokoly **DataPlaneRequests** je k dispozici pouze hodnota. |
| **OperationName** | **OperationName** | Název operace. Tato hodnota může být libovolná z následujících operací: vytvoření, aktualizace, čtení, ReadFeed, odstranění, nahraďte, spouštění, SqlQuery, dotazu, JSQuery, Head, HeadFeed nebo Upsert.   |
| **Vlastnosti** | neuvedeno | Obsah tohoto pole jsou popsané v řádky, které následují. |
| **ID aktivity** | **activityId_g** | Jedinečný identifikátor GUID pro protokolovaných operací. |
| **userAgent** | **userAgent_s** | Řetězec, který určuje uživatelský agent klienta, který provádí požadavek. Formát je {uživatelské jméno agenta} / {version}.|
| **Typ prostředku** | **Typ prostředku** | Typ prostředku, u níž. Tato hodnota může být jakýkoli z následujících typů prostředků: databáze, kolekce, dokument, přílohy, uživatele, oprávnění, StoredProcedure, aktivační události, UserDefinedFunction nebo nabídku. |
| **statusCode** | **statusCode_s** | Stav odpovědi operace. |
| **requestResourceId** | **ID prostředku** | ID prostředku, které se vztahují na žádost. Hodnota může vést k databaseRid, collectionRid nebo documentRid v závislosti na operaci provést.|
| **clientIpAddress** | **clientIpAddress_s** | IP adresa klienta. |
| **requestCharge** | **requestCharge_s** | Počet RUs, které jsou používány operaci |
| **collectionRid** | **collectionId_s** | Jedinečné ID pro kolekci.|
| **Doba trvání** | **duration_s** | Doba trvání operace v rysky. |
| **requestLength** | **requestLength_s** | Délka požadavku v bajtech. |
| **responseLength** | **responseLength_s** | Délka odpovědi v bytech.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Tato hodnota je prázdný, když [prostředků tokeny](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) slouží k ověřování. Hodnota se odkazuje na prostředek ID uživatele. |

## <a name="next-steps"></a>Další postup

- Pochopit, jak povolit protokolování a metriky a protokolu kategorií, které podporuje různé služby Azure, přečíst i [přehled metriky v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) a [přehled o Azure diagnostických protokolů ](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) články.
- Přečtěte si další informace o službě event hubs tyto články:
   - [Co je Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Začínáme s Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Čtení [stáhnout metriky a diagnostické protokoly ze služby Azure Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
- Čtení [Rady pro pochopení protokolu hledání v analýzy protokolů](../log-analytics/log-analytics-log-search-new.md).
