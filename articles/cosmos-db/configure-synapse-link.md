---
title: Konfigurace a použití odkazu na Azure synapse pro Azure Cosmos DB (Preview)
description: Naučte se, jak povolit synapse odkaz pro účty Azure Cosmos DB, vytvořit kontejner s povoleným analytickým úložištěm, připojit databázi Azure Cosmos k synapse pracovnímu prostoru a spustit dotazy.
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/31/2020
ms.author: rosouz
ms.custom: references_regions
ms.openlocfilehash: a634743441866c4f1a9f6d634efe0ff9e368b5e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91757839"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db-preview"></a>Konfigurace a použití odkazu na Azure synapse pro Azure Cosmos DB (Preview)

[Odkaz na Azure synapse pro Azure Cosmos DB](synapse-link.md) je cloudová funkce hybridního transakčního a analytického zpracování (HTAP), která umožňuje spouštět analýzy téměř v reálném čase nad provozními daty v Azure Cosmos DB. Synapse Link vytvoří úzkou plynulou integraci mezi Azure Cosmos DB a Azure synapse Analytics.

> [!IMPORTANT]
> Pokud chcete použít Azure synapse Link, nezapomeňte zřídit účet Azure Cosmos DB & pracovní prostor Azure synapse Analytics v některé z podporovaných oblastí. Odkaz na Azure synapse je v současnosti dostupný v následujících oblastech Azure: USA – středozápad, Východní USA, západ USA 2, Severní Evropa, Západní Evropa, Střed USA – jih, jihovýchodní Asie, Austrálie – východ, východní U2, Velká Británie – jih.

Odkaz Azure synapse je k dispozici pro Azure Cosmos DB kontejnery rozhraní API SQL nebo pro Azure Cosmos DB API pro kolekce Mongo DB. Pomocí následujících kroků spusťte analytické dotazy s odkazem na Azure synapse pro Azure Cosmos DB:

* [Povolit synapse odkaz na účty Azure Cosmos DB](#enable-synapse-link)
* [Vytvoření kontejneru Azure Cosmos DB s povoleným analytickým úložištěm](#create-analytical-ttl)
* [Připojení databáze Azure Cosmos DB k pracovnímu prostoru synapse](#connect-to-cosmos-database)
* [Dotaz na analytické úložiště pomocí synapse Spark](#query-analytical-store-spark)
* [Dotazování analytického úložiště s využitím synapse SQL bez serveru](#query-analytical-store-sql-on-demand)
* [K analýze a vizualizaci dat v Power BI používejte SQL Server bez serveru synapse](#analyze-with-powerbi)

## <a name="enable-azure-synapse-link-for-azure-cosmos-db-accounts"></a><a id="enable-synapse-link"></a>Povolit Azure synapse Link pro účty Azure Cosmos DB

### <a name="azure-portal"></a>portál Azure

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure](create-sql-api-dotnet.md#create-account)nebo vyberte existující účet Azure Cosmos DB.

1. Přejděte na účet Azure Cosmos DB a otevřete podokno **funkce** .

1. V seznamu funkcí vyberte **odkaz synapse** .

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="Najít funkci synapse Link Preview":::

1. V dalším kroku se zobrazí výzva, abyste na svém účtu povolili odkaz na synapse. Vyberte **Povolit**. Dokončení tohoto procesu může trvat 1 až 5 minut.

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="Najít funkci synapse Link Preview":::

1. Váš účet teď povolil použití odkazu synapse. Další informace najdete v tématu Vytvoření kontejnerů s povoleným analytickým úložištěm pro automatické zahájení replikace provozních dat z transakčního úložiště do analytického úložiště.

> [!NOTE]
> Zapnutím odkazu synapse se analytické úložiště automaticky nezapne. Jakmile povolíte synapse odkaz na účet Cosmos DB, povolíte při vytváření na kontejnerech analytické úložiště, abyste mohli začít replikovat data operace do analytického úložiště. 

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a> Vytvoření kontejneru Azure Cosmos s analytickým úložištěm

Analytické úložiště můžete zapnout v kontejneru Azure Cosmos při vytváření kontejneru. Můžete použít Azure Portal nebo nakonfigurovat `analyticalTTL` vlastnost během vytváření kontejneru pomocí sady Azure Cosmos DB SDK.

> [!NOTE]
> V současné době můžete pro **nové** kontejnery povolit analytické úložiště (v nových i stávajících účtech). Data z kontejnerů identita existující můžete migrovat do nových kontejnerů pomocí [nástrojů pro migraci Azure Cosmos DB.](cosmosdb-migrationchoices.md)

### <a name="azure-portal"></a>portál Azure

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) nebo ke [službě Azure Cosmos Explorer](https://cosmos.azure.com/).

1. Přejděte na účet Azure Cosmos DB a otevřete kartu **Průzkumník dat** .

1. Vyberte **Nový kontejner** a zadejte název své databáze, kontejneru, klíče oddílu a propustnosti. Zapněte možnost **analytické úložiště** . Po povolení analytického úložiště se vytvoří kontejner s `AnalyicalTTL` vlastností nastavenou na výchozí hodnotu-1 (nekonečné uchovávání). Toto analytické úložiště uchovává všechny historické verze záznamů.

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Najít funkci synapse Link Preview":::

1. Pokud jste na tomto účtu dříve nepovolili odkaz na synapse, zobrazí se výzva k tomu, abyste to učinili, protože je to předpoklad pro vytvoření kontejneru s povoleným analytickým úložištěm. Po zobrazení výzvy vyberte **Povolit odkaz na synapse**. Dokončení tohoto procesu může trvat 1 až 5 minut.

1. Vyberte **OK**a vytvořte kontejner Azure Cosmos s povoleným analytickým úložištěm.

1. Po vytvoření kontejneru ověřte, že je analytické úložiště povolené, a to tak, že kliknete na **Nastavení**, vpravo pod dokumenty v Průzkumník dat a zkontrolujete, jestli je zapnutá možnost **Doba provozu analytického úložiště** .

### <a name="net-sdk"></a>.NET SDK

Následující kód vytvoří kontejner s analytickým úložištěm pomocí sady .NET SDK. Nastavte vlastnost analytického TTL na požadovanou hodnotu. Seznam povolených hodnot naleznete v článku [hodnoty podporované v analytickém TTL](analytical-store-introduction.md#analytical-ttl) :

```csharp
// Create a container with a partition key, and analytical TTL configured to  -1 (infinite retention)
string containerId = “myContainerName”;
int analyticalTtlInSec = -1;
ContainerProperties cpInput = new ContainerProperties()
            {
Id = containerId,
PartitionKeyPath = "/id",
AnalyticalStorageTimeToLiveInSeconds = analyticalTtlInSec,
};
 await this. cosmosClient.GetDatabase("myDatabase").CreateContainerAsync(cpInput);
```

### <a name="java-v4-sdk"></a>Sada Java v4 SDK

Následující kód vytvoří kontejner s analytickým úložištěm pomocí sady Java v4 SDK. Nastavte `AnalyticalStoreTimeToLiveInSeconds` vlastnost na požadovanou hodnotu:

```java
// Create a container with a partition key and  analytical TTL configured to  -1 (infinite retention) 
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

containerProperties.setAnalyticalStoreTimeToLiveInSeconds(-1);

container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

### <a name="python-v4-sdk"></a>Sada Python v4 SDK

Python 2,7 a Azure Cosmos DB SDK 4.1.0 jsou vyžadovány minimální verze a sada SDK je kompatibilní pouze s rozhraním SQL API.

Prvním krokem je, abyste se ujistili, že používáte minimálně 4.1.0 verze [Azure Cosmos DB Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos):

```python
import azure.cosmos as cosmos

print (cosmos.__version__)
```
V dalším kroku se vytvoří kontejner s analytickým úložištěm pomocí Azure Cosmos DB Python SDK:

```python
# Azure Cosmos DB Python SDK, for SQL API only.
# Creating an analytical store enabled container.

import azure.cosmos.cosmos_client as cosmos_client
import azure.cosmos.exceptions as exceptions
from azure.cosmos.partition_key import PartitionKey

HOST = 'your-cosmos-db-account-URI'
KEY = 'your-cosmos-db-account-key'
DATABASE = 'your-cosmos-db-database-name'
CONTAINER = 'your-cosmos-db-container-name'

client = cosmos_client.CosmosClient(HOST,  KEY )
# setup database for this sample. 
# If doesn't exist, creates a new one with the name informed above.
try:
    db = client.create_database(DATABASE)

except exceptions.CosmosResourceExistsError:
    db = client.get_database_client(DATABASE)

# Creating the container with analytical store enabled, using the name informed above.
# If a container with the same name exists, an error is returned.
#
# The 3 options for the analytical_storage_ttl parameter are:
# 1) 0 or Null or not informed (Not enabled).
# 2) -1 (The data will be stored in analytical store infinitely).
# 3) Any other number is the actual ttl, in seconds.

try:
    container = db.create_container(
        id=CONTAINER,
        partition_key=PartitionKey(path='/id', kind='Hash'),analytical_storage_ttl=-1
    )
    properties = container.read()
    print('Container with id \'{0}\' created'.format(container.id))
    print('Partition Key - \'{0}\''.format(properties['partitionKey']))

except exceptions.CosmosResourceExistsError:
    print('A container with already exists')
```

### <a name="update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a> Aktualizace doby analytického úložiště na Live

Po povolení analytického úložiště s konkrétní hodnotou TTL můžete tuto hodnotu později aktualizovat na jinou platnou hodnotu. Tuto hodnotu můžete aktualizovat pomocí webu Azure Portal nebo sad SDK. Informace o různých možnostech konfigurace analytického standardu TTL najdete v článku věnovaném [hodnotám analytického TTL](analytical-store-introduction.md#analytical-ttl) .

#### <a name="azure-portal"></a>portál Azure

Pokud jste vytvořili kontejner analytického úložiště s povoleným Azure Portal, obsahuje výchozí analytickou hodnotu TTL-1. Tuto hodnotu můžete aktualizovat pomocí následujících kroků:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) nebo ke [službě Azure Cosmos Explorer](https://cosmos.azure.com/).

1. Přejděte na účet Azure Cosmos DB a otevřete kartu **Průzkumník dat** .

1. Vyberte existující kontejner s povoleným analytickým úložištěm. Rozbalte ho a upravte následující hodnoty:

  * Otevřete okno **nastavení & škálování** .
  * V části **Nastavení** najít, * * čas analytického úložiště na Live * *.
  * Vyberte **zapnuto (žádné výchozí)** nebo **Vyberte a nastavte** hodnotu TTL.
  * Kliknutím na **Uložit** uložte změny.

#### <a name="net-sdk"></a>.NET SDK

Následující kód ukazuje, jak aktualizovat hodnotu TTL pro analytické úložiště pomocí sady .NET SDK:

```csharp
// Get the container, update AnalyticalStorageTimeToLiveInSeconds 
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Update analytical store TTL
containerResponse.Resource. AnalyticalStorageTimeToLiveInSeconds = 60 * 60 * 24 * 180  // Expire analytical store data in 6 months;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

#### <a name="java-v4-sdk"></a>Sada Java v4 SDK

Následující kód ukazuje, jak aktualizovat hodnotu TTL pro analytické úložiště pomocí sady Java v4 SDK:

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");

// Update analytical store TTL to expire analytical store data in 6 months;
containerProperties.setAnalyticalStoreTimeToLiveInSeconds (60 * 60 * 24 * 180 );  
 
// Update container settings
container.replace(containerProperties).block();
```

## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a> Připojení k pracovnímu prostoru synapse

Použijte pokyny v tématu [připojení k Azure synapse](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md) , jak získat přístup k databázi Azure Cosmos DB z Azure synapse Analytics studia pomocí Azure synapse Link.

## <a name="query-analytical-store-using-apache-spark-for-azure-synapse-analytics"></a><a id="query-analytical-store-spark"></a> Dotaz na analytické úložiště pomocí Apache Spark pro Azure synapse Analytics

Postupujte podle pokynů v článku [dotaz Azure Cosmos DB analytické úložiště](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md) pro dotazování pomocí synapse Spark. Tento článek obsahuje několik příkladů, jak můžete pracovat s analytickým úložištěm z synapse gest. Tato gesta se zobrazí po kliknutí pravým tlačítkem na kontejner. Pomocí gest můžete rychle vygenerovat kód a vylepšit ho podle svých potřeb. Jsou také ideální pro zjišťování dat jediným kliknutím.

## <a name="query-the-analytical-store-using-synapse-sql-serverless"></a><a id="query-analytical-store-sql-on-demand"></a> Dotazování analytického úložiště s využitím synapse SQL bez serveru

Synapse SQL bez serveru (funkce Preview, která se dřív odkazovala na **požadavky SQL na vyžádání**), umožňuje dotazovat a analyzovat data v kontejnerech Azure Cosmos DB, které jsou povolené pomocí odkazu Azure synapse. Data můžete analyzovat téměř v reálném čase, aniž by to ovlivnilo výkon transakčních úloh. Nabízí známou syntaxi T-SQL pro dotazování dat z analytického úložiště a integrovaného připojení k široké škále nástrojů pro dotazování BI a ad-hoc, a to prostřednictvím rozhraní T-SQL. Další informace najdete v článku s [dotazem na analytické úložiště s synapse SQL bez serveru](../synapse-analytics/sql/query-cosmos-db-analytical-store.md) .

> [!NOTE]
> Použití Azure Cosmos DBho analytického úložiště s synapse bez SQL serveru je v současné době ve verzi gatedd Preview. Pokud chcete požádat o přístup, přihlaste se k [Azure Cosmos DB týmu](mailto:cosmosdbsynapselink@microsoft.com).

## <a name="use-synapse-sql-serverless-to-analyze-and-visualize-data-in-power-bi"></a><a id="analyze-with-powerbi"></a>K analýze a vizualizaci dat v Power BI používejte SQL Server bez serveru synapse

Můžete vytvořit synapse databázi bez SQL serveru a zobrazení prostřednictvím odkazu synapse pro Azure Cosmos DB. Později můžete zadat dotaz na kontejnery Azure Cosmos a pak vytvořit model s Power BI nad těmito zobrazeními, aby odrážely tento dotaz. Další informace najdete v článku Jak používat [synapse SQL Server bez serveru k analýze Azure Cosmos DB dat pomocí propojení synapse](synapse-link-power-bi.md) .

## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

[Šablona Azure Resource Manager](manage-sql-with-resource-manager.md#azure-cosmos-account-with-analytical-store) vytvoří Azure Cosmos DB účet s povoleným odkazem na synapse pro rozhraní SQL API. Tato šablona vytvoří účet základního (SQL) rozhraní API v jedné oblasti s kontejnerem nakonfigurovaným s povoleným analytickým standardem TTL a možností ručního škálování nebo propustnosti. Tuto šablonu nasadíte kliknutím na **nasadit do Azure** na stránce Readme.

## <a name="getting-started-with-azure-synpase-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a> Začínáme s propojením Azure Synpase – ukázky

Ukázky, které vám pomůžou začít s propojením Azure synapse na [GitHubu](https://aka.ms/cosmosdb-synapselink-samples), najdete v ukázkách. Tato předvádí ucelená řešení s využitím scénářů IoT a Retail. Můžete také najít ukázky, které odpovídají Azure Cosmos DB rozhraní API pro MongoDB ve stejném úložišti ve složce [MongoDB](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/MongoDB) . 

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících dokumentech:

* [Odkaz na Azure synapse pro Azure Cosmos DB.](synapse-link.md)

* [Přehled analytického úložiště Azure Cosmos DB](analytical-store-introduction.md)

* [Nejčastější dotazy týkající se Azure Cosmos DB odkaz na synapse.](synapse-link-frequently-asked-questions.md)

* [Apache Spark ve službě Azure synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md).

* [Podpora běhového prostředí bez SQL serveru ve službě Azure synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md).
