---
title: Protokolování diagnostiky ve službě Azure Cosmos DB
description: Přečtěte si o různých způsobech, jak protokolovat a monitorovat data uložená v Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: bdbc50983708327cf5d3857282c92fcab1c28b09
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930551"
---
# <a name="diagnostic-logging-in-azure-cosmos-db"></a>Protokolování diagnostiky ve službě Azure Cosmos DB 

Až začnete používat jednu nebo více databází Azure Cosmos, můžete chtít monitorovat, jak a kdy se k databázím dostanete. Tento článek poskytuje přehled protokolů, které jsou k dispozici na platformě Azure. Naučíte se, jak povolit diagnostické protokolování pro účely monitorování, aby se odesílaly protokoly do [Azure Storage](https://azure.microsoft.com/services/storage/), jak streamovat protokoly do [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)a jak exportovat protokoly do [protokolů Azure monitor](https://azure.microsoft.com/services/log-analytics/).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="logs-available-in-azure"></a>Protokoly dostupné v Azure

Než budeme pohovořit o tom, jak váš účet Azure Cosmos DB monitorovat, Pojďme si vyjasnit pár věcí o protokolování a monitorování. Existují různé typy protokolů na platformě Azure. K dispozici jsou [protokoly aktivit Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [diagnostické protokoly Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), [metriky Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), události, monitorování prezenčního signálu, protokoly operací atd. Existuje spoustu protokolů. Zobrazí se úplný seznam protokolů v [Azure monitor protokoly](https://azure.microsoft.com/services/log-analytics/) v Azure Portal. 

Následující obrázek znázorňuje různé druhy protokolů Azure, které jsou k dispozici:

![Různé druhy protokolů Azure](./media/logging/azurelogging.png)

Na obrázku **výpočetní prostředky** reprezentují prostředky Azure, pro které můžete získat přístup k HOSTOVANÉmu operačnímu systému Microsoftu. Například Azure Virtual Machines, sada škálování pro virtuální počítače, Azure Container Service a tak dále, se považují za výpočetní prostředky. Výpočetní prostředky generují protokoly aktivit, diagnostické protokoly a protokoly aplikací. Další informace najdete [v článku zdroje dat monitorování v Azure](../azure-monitor/platform/data-sources.md) .

Prostředky, které **nejsou výpočetními prostředky** , jsou prostředky, ve kterých nemůžete získat přístup k ZÁKLADNÍmu operačnímu systému a pracovat přímo s prostředkem. Například skupiny zabezpečení sítě, Logic Apps a tak dále. Azure Cosmos DB je nevýpočetní prostředek. Protokoly pro nevýpočetní prostředky můžete zobrazit v protokolu aktivit nebo povolit možnost diagnostické protokoly na portálu. Další informace najdete v článku o [zdrojích dat v Azure monitor](../azure-monitor/platform/data-sources.md) .

Protokol aktivit zaznamenává operace na úrovni předplatného pro Azure Cosmos DB. Do protokolu se zaznamenávají operace jako klíče listkey, zápis DatabaseAccounts a další. Diagnostické protokoly poskytují přesnější protokolování a umožňují protokolovat DataPlaneRequests (vytváření, čtení, dotazování a tak dále) a MongoRequests.


V tomto článku se zaměříme na protokol aktivit Azure, diagnostické protokoly Azure a metriky Azure. Jaký je rozdíl mezi těmito třemi protokoly? 

### <a name="azure-activity-log"></a>Protokol aktivit Azure

Protokol aktivit Azure je protokol předplatného, který poskytuje přehled o událostech na úrovni předplatného, ke kterým došlo v Azure. Protokol aktivit zaznamenává události v úrovni ovládacího prvku pro vaše předplatná v kategorii správy. Protokol aktivit můžete použít k určení "co, kdo a kdy" pro všechny operace zápisu (PUT, POST, DELETE) u prostředků v rámci vašeho předplatného. Můžete také pochopit stav operace a dalších relevantních vlastností. 

Protokol aktivit se liší od diagnostických protokolů. Protokol aktivit poskytuje data o operacích na prostředku z vnějšku ( _ovládací rovina ovládacího prvku_). V kontextu Azure Cosmos DB řídicí operace roviny zahrnují vytváření kontejnerů, klíčů seznamů, odstraňování klíčů, výpis databáze a tak dále. Diagnostické protokoly jsou vydávány prostředkem a poskytují informace o provozu daného prostředku ( _rovina dat_). Některé příklady operací s rovinou dat v diagnostickém protokolu jsou odstranit, vložit a ReadFeed.

Protokoly aktivit (operace řízení roviny) můžou mít bohatou povahu a můžou obsahovat úplnou e-mailovou adresu volajícího, IP adresy volajícího, názvu prostředku, názvu operace, TenantId a dalších. Protokol aktivit obsahuje několik [kategorií](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) dat. Úplné podrobnosti o schémat těchto kategorií najdete v tématu [schéma událostí protokolu aktivit Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema). Diagnostické protokoly ale můžou být omezující, protože osobní údaje jsou z těchto protokolů často odstraněny. Může se jednat o IP adresu volajícího, ale poslední octant se odebere.

### <a name="azure-metrics"></a>Metriky Azure

[Metriky Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) mají nejdůležitější typ dat telemetrie Azure (označuje se taky jako _čítače výkonu_) vysílané většinou prostředků Azure. Metriky umožňují zobrazit informace o propustnosti, úložišti, konzistenci, dostupnosti a latenci vašich Azure Cosmos DBch prostředků. Další informace najdete v tématu [monitorování a ladění pomocí metrik v Azure Cosmos DB](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Diagnostické protokoly Azure

Diagnostické protokoly Azure jsou vydávány prostředkem a poskytují bohatou a častou data o provozu daného prostředku. Tyto protokoly jsou zachyceny na základě požadavku. Obsah těchto protokolů se liší podle typu prostředku. Diagnostické protokoly na úrovni prostředku se také liší od protokolů diagnostiky na úrovni operačního systému hosta. Diagnostické protokoly hostovaného operačního systému shromažďuje agent, který běží ve virtuálním počítači nebo jiném podporovaném typu prostředku. Diagnostické protokoly na úrovni prostředku nevyžadují žádného agenta a zaznamenání dat specifických pro konkrétní prostředek ze samotné platformy Azure. Diagnostické protokoly na úrovni operačního systému hostují data z operačního systému a aplikací, které běží na virtuálním počítači.

![Protokolování diagnostiky do protokolů úložiště, Event Hubs nebo Azure Monitor](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Co protokoluje diagnostické protokoly Azure?

* Všechny ověřené back-endové požadavky (TCP/REST) napříč všemi rozhraními API se protokolují, včetně neúspěšných žádostí v důsledku přístupových oprávnění, systémových chyb nebo chybných požadavků. Podpora pro uživatelem iniciované požadavky na graf, Cassandra a rozhraní API pro tabulky není aktuálně k dispozici.
* Operace na samotné databázi, které zahrnují operace CRUD na všech dokumentech, kontejnerech a databázích.
* Operace s klíči účtu, mezi které patří vytváření, úpravy nebo odstraňování klíčů.
* Neověřené požadavky, které skončí odpovědí 401 – Neoprávněno. Například požadavky, které nemají nosný token, jsou poškozené nebo jejichž platnost vypršela, nebo mají neplatný token.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Zapnout protokolování v Azure Portal

K povolení diagnostického protokolování v Azure Portal použijte následující postup:

1. Přihlaste se k [Azure Portal](https://portal.azure.com). 

1. Přejděte k účtu Azure Cosmos. Otevřete podokno **nastavení diagnostiky** a pak vyberte **Přidat možnost nastavení diagnostiky** .

    ![Zapnout protokolování diagnostiky pro Azure Cosmos DB v Azure Portal](./media/logging/turn-on-portal-logging.png)

1. Na stránce **nastavení diagnostiky** vyplňte formulář následujícími podrobnostmi: 

    * **Název**: zadejte název protokolů, které chcete vytvořit.

    * Protokoly můžete ukládat do následujících služeb:

      * **Archivovat do účtu úložiště**: Pokud chcete použít tuto možnost, budete potřebovat existující účet úložiště, ke kterému se chcete připojit. Informace o vytvoření nového účtu úložiště na portálu najdete v článku o [Vytvoření účtu úložiště](../storage/common/storage-create-storage-account.md) . Pak se vraťte do podokna nastavení diagnostiky služby Azure Cosmos DB na portálu a vyberte svůj účet úložiště. Může trvat několik minut, než se nově vytvořené účty úložiště zobrazí v rozevírací nabídce.

      * **Streamování do centra událostí**: Pokud chcete použít tuto možnost, potřebujete existující Event Hubs obor názvů a centrum událostí, ke kterým se chcete připojit. Pokud chcete vytvořit obor názvů Event Hubs, přečtěte si téma [Vytvoření oboru názvů Event Hubs a centra událostí pomocí Azure Portal](../event-hubs/event-hubs-create.md). Pak se vraťte na tuto stránku na portálu a vyberte obor názvů a název zásady centra událostí.

      * **Odeslat do Log Analytics**: Chcete-li použít tuto možnost, buď použijte existující pracovní prostor, nebo vytvořte nový pracovní prostor Log Analytics podle postupu podle pokynů k [Vytvoření nového pracovního prostoru](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace) na portálu. 

   * Můžete protokolovat následující data:

      * **DataPlaneRequests**: tuto možnost vyberte, pokud chcete protokolovat požadavky back-endu na všechna rozhraní API, která zahrnují účty SQL, Graph, MongoDB, Cassandra a rozhraní API pro tabulky v Azure Cosmos DB. Pokud provádíte archivaci na účet úložiště, můžete vybrat dobu uchování diagnostických protokolů. Protokoly se po vypršení doby uchování automaticky odstraní. Následující data JSON jsou ukázkový výstup podrobností protokolovaných pomocí DataPlaneRequests. Všimněte si klíčových vlastností: Requestcharge, statusCode, clientIPaddress a partitionID:

       ```
       { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
       ```

      * **MongoRequests**: tuto možnost vyberte, pokud chcete protokolovat požadavky iniciované uživateli z front-endu, aby sloužily požadavky na rozhraní API služby Azure Cosmos DB pro MongoDB. Žádosti MongoDB se zobrazí v MongoRequests a také v DataPlaneRequests. Pokud provádíte archivaci na účet úložiště, můžete vybrat dobu uchování diagnostických protokolů. Protokoly se po vypršení doby uchování automaticky odstraní. Následující data JSON jsou ukázkový výstup podrobností protokolovaných pomocí MongoRequests. Všimněte si klíčových vlastností: Requestcharge, opCode:

       ```
       { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
       ```

      * **QueryRuntimeStatistics**: tuto možnost vyberte, pokud chcete protokolovat text dotazu, který se spustil.  Následující data JSON jsou příklad výstupu podrobností protokolovaných pomocí QueryRuntimeStatistics:

       ```
       { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
       ```

      * **PartitionKeyStatistics**: Tento protokol oznamuje statistiku klíčů oddílů. V současné době se Statistika reprezentuje s velikostí úložiště (KB) klíčů oddílů. Protokol je vygenerován proti prvním třem klíčům oddílu, které zabírají většinu úložiště dat.

       ```
       { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
       ```

      * **Požadavky na metriky**: tuto možnost vyberte, pokud chcete ukládat podrobná data do [metrik Azure](../azure-monitor/platform/metrics-supported.md). Pokud provádíte archivaci na účet úložiště, můžete vybrat dobu uchování diagnostických protokolů. Protokoly se po vypršení doby uchování automaticky odstraní.

3. Vyberte **Uložit**.

    Pokud se zobrazí chyba oznamující, že se nepovedlo aktualizovat diagnostiku pro \<název pracovního prostoru >. Předplatné \<ID předplatného > není zaregistrované pro použití Microsoft. Insights, pomocí pokynů [Azure Diagnostics Poradce při potížích](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) s registrací účet a pak zkuste tento postup zopakovat.

    Chcete-li změnit způsob, jakým jsou protokoly diagnostiky uloženy kdykoli v budoucnu, vraťte se na tuto stránku a upravte nastavení diagnostického protokolu pro váš účet.

## <a name="turn-on-logging-by-using-azure-cli"></a>Zapnutí protokolování pomocí rozhraní příkazového řádku Azure

Pokud chcete povolit metriky a protokolování diagnostiky pomocí Azure CLI, použijte následující příkazy:

- Pokud chcete povolit ukládání diagnostických protokolů v účtu úložiště, použijte tento příkaz:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name DiagStorage --resource <resourceId> --storage-account <storageAccountName> --logs '[{"category": "QueryRuntimeStatistics", "enabled": true, "retentionPolicy": {"enabled": true, "days": 0}}]'
   ```

   `resource` je název účtu Azure Cosmos DB. Prostředek má formát "/Subscriptions/`<subscriptionId>`/resourceGroups/`<resource_group_name>`/providers/Microsoft.DocumentDB/databaseAccounts/< Azure_Cosmos_account_name >" `storage-account` je název účtu úložiště, do kterého chcete protokoly odesílat. Další protokoly můžete protokolovat tak, že aktualizujete hodnoty parametrů kategorie na "MongoRequests" nebo "DataPlaneRequests". 

- Pokud chcete povolit streamování diagnostických protokolů do centra událostí, použijte tento příkaz:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --event-hub-rule <eventHubRuleID> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

   `resource` je název účtu Azure Cosmos DB. `event-hub-rule` je ID pravidla centra událostí. 

- Pokud chcete povolit odesílání diagnostických protokolů do Log Analytics pracovního prostoru, použijte tento příkaz:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --workspace <resource id of the log analytics workspace> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

Tyto parametry můžete kombinovat, chcete-li povolit více možností výstupu.

## <a name="turn-on-logging-by-using-powershell"></a>Zapnutí protokolování pomocí PowerShellu

Pokud chcete zapnout protokolování diagnostiky pomocí PowerShellu, potřebujete Azure PowerShell s minimální verzí 1.0.1.

Chcete-li nainstalovat Azure PowerShell a přidružit ho ke svému předplatnému Azure, prohlédněte si téma [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

Pokud jste už nainstalovali Azure PowerShell a neznáte verzi, z konzoly PowerShellu `(Get-Module azure -ListAvailable).Version`typ.  

### <a id="connect"></a>Připojení k předplatným
Spusťte relaci Azure PowerShellu a přihlaste se k účtu Azure pomocí následujícího příkazu:  

```powershell
Connect-AzAccount
```

V automaticky otevřeném okně prohlížeče zadejte svoje uživatelské jméno a heslo k účtu Azure. Azure PowerShell získá všechna předplatná, která jsou přidružená k tomuto účtu, a ve výchozím nastavení použije první z nich.

Pokud máte více než jedno předplatné, bude pravděpodobně nutné zadat konkrétní předplatné, které bylo použito k vytvoření trezoru klíčů Azure. Pokud chcete zobrazit předplatná pro svůj účet, zadejte následující příkaz:

```powershell
Get-AzSubscription
```

Potom zadejte následující příkaz, pokud chcete určit předplatné, které je přidružené k účtu Azure Cosmos DB, který jste právě přihlásili:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Pokud máte více než jedno předplatné, které je přidruženo k vašemu účtu, je důležité zadat předplatné, které chcete použít.
>
>

Další informace o tom, jak nakonfigurovat Azure PowerShell, najdete v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Vytvoření nového účtu úložiště pro protokoly
I když pro vaše protokoly můžete použít existující účet úložiště, vytvoříme v tomto kurzu nový účet úložiště, který je vyhrazený pro Azure Cosmos DB protokolů. Pro usnadnění práce ukládáme podrobnosti účtu úložiště do proměnné s názvem **SA**.

Pro další snadné řízení používáme v tomto kurzu stejnou skupinu prostředků jako ta, která obsahuje databázi Azure Cosmos. Podle potřeby nahraďte hodnoty parametrů **ContosoResourceGroup**, **contosocosmosdblogs**a **střed USA – sever** :

```powershell
$sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Pokud se rozhodnete použít existující účet úložiště, musí účet používat stejné předplatné jako vaše předplatné Azure Cosmos DB. Účet také musí používat model nasazení Správce prostředků, nikoli model nasazení Classic.
>
>

### <a id="identify"></a>Identifikace Azure Cosmos DB účtu pro vaše protokoly
Nastavte název Azure Cosmos DB účtu na proměnnou s názvem **account**, kde **resourcename** je název Azure Cosmos DB účtu.

```powershell
$account = Get-AzResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Povolení protokolování
Pokud chcete povolit protokolování pro Azure Cosmos DB, použijte rutinu `Set-AzDiagnosticSetting` s proměnnými pro nový účet úložiště, účet Azure Cosmos DB a kategorii, která se má povolit pro protokolování. Spusťte následující příkaz a nastavte příznak **-Enabled** na **$true**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

Výstup příkazu by měl vypadat podobně jako v následujícím příkladu:

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

Výstup z příkazu potvrdí, že je teď povolené protokolování pro vaši databázi a že se ukládají informace do svého účtu úložiště.

Volitelně můžete také nastavit zásady uchovávání informací pro vaše protokoly, aby se automaticky odstranily staré protokoly. Nastavte například zásady uchovávání informací s příznakem **-RetentionEnabled** nastaveným na **$true**. Nastavte parametr **-RetentionInDays** na **90** , aby se automaticky odstranily protokoly starší než 90 dní.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Přístup k protokolům
Protokoly Azure Cosmos DB pro kategorii **DataPlaneRequests** se ukládají do kontejneru **Insights-logs-DataPlaneRequests** v účtu úložiště, který jste zadali. 

Nejprve vytvořte proměnnou pro název kontejneru. Proměnná se používá v průběhu průchodu.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Chcete-li zobrazit seznam všech objektů BLOB v tomto kontejneru, zadejte:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Výstup příkazu by měl vypadat podobně jako v následujícím příkladu:

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

Jak vidíte z tohoto výstupu, objekty blob dodržují konvenci pojmenování: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Hodnoty data a času používají UTC.

Vzhledem k tomu, že se ke shromažďování protokolů pro více prostředků dá použít stejný účet úložiště, můžete použít plně kvalifikované ID prostředku v názvu objektu BLOB pro přístup k určitým objektům blob, které potřebujete, a stáhnout je. Než to uděláte, pokryjeme, jak stáhnout všechny objekty blob.

Nejprve vytvořte složku, kam stáhnete objekty blob. Příklad:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Pak Získejte seznam všech objektů BLOB:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Přesměrujte tento seznam pomocí příkazu `Get-AzStorageBlobContent`, aby se objekty blob stáhly do cílové složky:

```powershell
$blobs | Get-AzStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

Při spuštění tohoto druhého příkazu vytvoří oddělovač **/** v názvech objektů BLOB úplnou strukturu složek v cílové složce. Tato struktura složky slouží ke stažení a uložení objektů BLOB jako souborů.

Chcete-li stahovat objekty blob selektivně, použijte zástupné znaky. Příklad:

* Pokud máte více databází a chcete stáhnout protokoly jenom pro jednu databázi s názvem **CONTOSOCOSMOSDB3**, použijte příkaz:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Pokud máte více skupin prostředků a chcete stáhnout protokoly jenom pro jednu skupinu prostředků, použijte příkaz `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Pokud chcete stáhnout všechny protokoly pro měsíc z července 2017, použijte příkaz `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Můžete také spustit následující příkazy:

* Chcete-li zadat dotaz na stav nastavení diagnostiky pro prostředek databáze, použijte příkaz `Get-AzDiagnosticSetting -ResourceId $account.ResourceId`.
* Chcete-li zakázat protokolování kategorie **DataPlaneRequests** pro prostředek databázového účtu, použijte příkaz `Set-AzDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`.


Objekty blob, které se vrátí v každém z těchto dotazů, se ukládají jako text a naformátují jako objekt BLOB JSON, jak je znázorněno v následujícím kódu:

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

Další informace o datech v jednotlivých objektech blob JSON najdete v tématu [Interpretace protokolů Azure Cosmos DB](#interpret).

## <a name="manage-your-logs"></a>Správa protokolů

Diagnostické protokoly jsou ve vašem účtu k dispozici po dobu dvou hodin od okamžiku provedení operace Azure Cosmos DB. Správa protokolů v účtu úložiště je pouze na vás:

* Pomocí standardních metod řízení přístupu Azure Zabezpečte své protokoly a omezte, kdo k nim má přístup.
* Odstraňte protokoly, které už nechcete uchovávat v účtu úložiště.
* Doba uchování požadavků na rovinu dat, které se archivují na účet úložiště, se konfiguruje na portálu, když je vybrané nastavení **DataPlaneRequests protokolu** . Pokud chcete toto nastavení změnit, přečtěte si téma [Zapnutí protokolování v Azure Portal](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-azure-monitor-logs"></a>Zobrazit protokoly v protokolu Azure Monitor

Pokud jste vybrali možnost **Odeslat do Log Analytics** , když jste zapnuli protokolování diagnostiky, budou diagnostická data z vašeho kontejneru předána do Azure monitor protokolů do dvou hodin. Když se Azure Monitor protokoly hned po zapnutí protokolování, nezobrazí se žádná data. Stačí počkat dvě hodiny a pak to zkuste znovu. 

Před zobrazením protokolů zkontrolujte a zjistěte, jestli je váš pracovní prostor Log Analytics upgradovaný tak, aby používal nový dotazovací jazyk Kusto. Chcete-li kontrolu spustit, otevřete [Azure Portal](https://portal.azure.com), vyberte možnost **Log Analytics pracovní prostory** úplně vlevo a potom vyberte název pracovního prostoru, jak je znázorněno na následujícím obrázku. Zobrazí se stránka **Log Analytics pracovní prostor** :

![Azure Monitor protokoly v Azure Portal](./media/logging/azure-portal.png)

>[!NOTE]
>Pracovní prostory OMS se teď označují jako pracovní prostory Log Analytics.  

Pokud se na stránce **Log Analytics pracovní prostor** zobrazí následující zpráva, váš pracovní prostor nebyl upgradován tak, aby používal nový jazyk. Další informace o tom, jak upgradovat na nový dotazovací jazyk, najdete v tématu [upgrade pracovního prostoru Azure Log Analytics na nové prohledávání protokolu](../log-analytics/log-analytics-log-search-upgrade.md). 

![Zpráva o upgradu protokolů Azure Monitor](./media/logging/upgrade-notification.png)

Chcete-li zobrazit diagnostická data v protokolech Azure Monitor, otevřete stránku **prohledávání protokolu** v nabídce vlevo nebo oblasti **správy** stránky, jak je znázorněno na následujícím obrázku:

![Možnosti prohledávání protokolu v Azure Portal](./media/logging/log-analytics-open-log-search.png)

Teď, když jste povolili shromažďování dat, spusťte následující příklad hledání protokolu pomocí nového dotazovacího jazyka, abyste zobrazili 10 posledních protokolů `AzureDiagnostics | take 10`.

![Ukázka hledání protokolu pro 10 posledních protokolů](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="cosmosdb-log-analytics-queries-in-azure-monitor"></a>CosmosDB Log Analytics dotazy v Azure Monitor

Tady je několik dalších dotazů, které můžete zadat do pole **hledání v protokolu** , které vám pomůžou monitorovat vaše kontejnery Cosmos Azure. Tyto dotazy fungují s [novým jazykem](../log-analytics/log-analytics-log-search-upgrade.md).  

Další informace o významu dat vrácených jednotlivými prohledáváním protokolů najdete v tématu [interpretace Azure Cosmos DBch protokolů](#interpret).

* Dotaz na všechny diagnostické protokoly z Azure Cosmos DB v zadaném časovém období:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Dotaz na 10 naposledy protokolovaných událostí:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | limit 10
    ```

* Dotaz na všechny operace seskupený podle typu operace:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName
    ```

* Dotaz na všechny operace seskupené podle **prostředků**:

    ```
    AzureActivity 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```

* Dotaz na všechny aktivity uživatelů seskupené podle prostředků:

    ```
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```
    > [!NOTE]
    > Tento příkaz je určen pro protokol aktivit, nikoli pro diagnostický protokol.

* Získání všech dotazů větších než 100 ru spojených s daty z DataPlaneRequests a QueryRunTimeStatistics

    ```
    AzureDiagnostics
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
    | project activityId_g, requestCharge_s
    | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
    ) on $left.activityId_g == $right.activityId_g
    | order by requestCharge_s desc
    | limit 100
    ```
    
      

* Dotaz na to, které operace trvá déle než 3 milisekundy:

    ```
    AzureDiagnostics 
    | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Dotaz, pro který agent spouští operace:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName, userAgent_s
    ```

* Dotaz na dobu, kdy byly provedeny dlouho běžící operace:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | project TimeGenerated , duration_s 
    | render timechart
    ```
    
* Získání statistik o klíčích oddílů pro vyhodnocení zešikmení v horních 3 oddílech pro databázový účet:

    ```
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    
   
    ```
    

Další informace o použití nového jazyka pro hledání v protokolu najdete v tématu [vysvětlení prohledávání protokolů v Azure Monitorch protokolech](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>Interpretace protokolů

Diagnostická data uložená v protokolech Azure Storage a Azure Monitor používají podobné schéma. 

Následující tabulka popisuje obsah každé položky protokolu.

| Azure Storage pole nebo vlastnost | Vlastnost protokolů Azure Monitor | Popis |
| --- | --- | --- |
| **interval** | **TimeGenerated** | Datum a čas (UTC), kdy došlo k operaci. |
| **Prostředku** | **Prostředek** | Účet Azure Cosmos DB, pro který jsou povoleny protokoly.|
| **kategorií** | **Kategorie** | V případě protokolů Azure Cosmos DB je jediná dostupná hodnota **DataPlaneRequests** . |
| **operationName** | **OperationName** | Název operace Tato hodnota může být libovolná z následujících operací: vytvořit, aktualizovat, číst, ReadFeed, odstranit, nahradit, spustit, SqlQuery, dotaz, JSQuery, Head, HeadFeed nebo Upsert.   |
| **vlastnosti** | neuvedeno | Obsah tohoto pole je popsán v následujících řádcích. |
| **activityId** | **activityId_g** | Jedinečný identifikátor GUID pro protokolované operaci. |
| **userAgent** | **userAgent_s** | Řetězec, který určuje uživatelského agenta klienta, který provádí požadavek. Formát je {User Agent Name}/{Version}.|
| **requestResourceType** | **requestResourceType_s** | Typ dostupného prostředku Tato hodnota může být libovolný z následujících typů prostředků: databáze, kontejner, dokument, příloha, uživatel, oprávnění, StoredProcedure, Trigger, UserDefinedFunction nebo nabídka. |
| **statusCode** | **statusCode_s** | Stav odpovědi operace. |
| **requestResourceId** | **Prostředku** | ResourceId, který se vztahuje k žádosti. Hodnota může ukazovat na databaseRid, collectionRid nebo documentRid v závislosti na provedené operaci.|
| **clientIpAddress** | **clientIpAddress_s** | IP adresa klienta. |
| **requestCharge** | **requestCharge_s** | Počet ru, které se používají v operaci |
| **collectionRid** | **collectionId_s** | Jedinečné ID pro kolekci.|
| **úkolu** | **duration_s** | Doba trvání operace v milisekundách. |
| **requestLength** | **requestLength_s** | Délka požadavku (v bajtech). |
| **responseLength** | **responseLength_s** | Délka odpovědi v bajtech|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Tato hodnota není prázdná, pokud se pro ověřování používají [tokeny prostředků](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) . Hodnota odkazuje na ID prostředku uživatele. |

## <a name="next-steps"></a>Další kroky

- Pokud chcete pochopit, jak povolit protokolování, a také kategorie metrik a protokolů, které jsou podporované různými službami Azure, přečtěte si [Přehled metrik v tématu Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) a [Přehled článků o diagnostických protokolech Azure](../azure-monitor/platform/resource-logs-overview.md) .
- Přečtěte si tyto články, abyste se seznámili s centry událostí:
   - [Co je Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Začínáme s Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Přečtěte si téma [stažení metrik a diagnostické protokoly z Azure Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
- Přečtěte si [vysvětlení hledání v protokolu v](../log-analytics/log-analytics-log-search-new.md)protokolech Azure monitor.
