---
title: Konfigurace a použití odkazu na Azure synapse pro Azure Cosmos DB (Preview)
description: Naučte se, jak povolit synapse odkaz pro účty Azure Cosmos, vytvořit kontejner s povoleným analytickým úložištěm, připojit databázi Azure Cosmos k pracovnímu prostoru synapse a spustit dotazy.
author: SriChintala
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: srchi
ms.openlocfilehash: d2a10d064bed3e2e2e798d16ce72ccf55c965f8d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85262034"
---
# <a name="configure-and-use-azure-synapse-link-for-azure-cosmos-db-preview"></a>Konfigurace a použití odkazu na Azure synapse pro Azure Cosmos DB (Preview)

Synapse odkaz pro Azure Cosmos DB je cloudová funkce hybridního transakčního a analytického zpracování (HTAP), která umožňuje spouštět analýzy prakticky v reálném čase nad provozními daty v Azure Cosmos DB. Synapse Link vytvoří úzkou plynulou integraci mezi Azure Cosmos DB a Azure synapse Analytics.


> [!IMPORTANT]
> Pokud chcete použít Azure synapse Link, nezapomeňte zřídit účet Azure Cosmos & pracovní prostor Azure synapse Analytics v jedné z výše uvedených podporovaných oblastí. Seznam podporovaných oblastí najdete v tématu [aktualizace služby Azure](https://azure.microsoft.com/updates/). 

Pomocí následujících kroků spusťte analytické dotazy s odkazem na synapse pro Azure Cosmos DB:

* [Povolit synapse odkaz pro účty Azure Cosmos](#enable-synapse-link)
* [Vytvoření kontejneru Azure Cosmos s povoleným analytickým úložištěm](#create-analytical-ttl)
* [Připojení databáze Azure Cosmos k pracovnímu prostoru synapse](#connect-to-cosmos-database)
* [Dotaz na analytické úložiště pomocí synapse Spark](#query-analytical-store)

## <a name="enable-azure-synapse-link-for-azure-cosmos-accounts"></a><a id="enable-synapse-link"></a>Povolit Azure synapse Link pro účty Azure Cosmos

### <a name="azure-portal"></a>portál Azure

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

1. [Vytvořte nový účet Azure](create-sql-api-dotnet.md#create-account)nebo vyberte existující účet Azure Cosmos.

1. Přejděte k účtu Azure Cosmos a otevřete podokno **funkce** .

1. V seznamu funkcí vyberte **odkaz synapse** .

   :::image type="content" source="./media/configure-synapse-link/find-synapse-link-feature.png" alt-text="Najít funkci synapse Link Preview":::

1. V dalším kroku se zobrazí výzva, abyste na svém účtu povolili odkaz na synapse. Vyberte Povolit.

   :::image type="content" source="./media/configure-synapse-link/enable-synapse-link-feature.png" alt-text="Povolit funkci synapse Link":::

1. Váš účet teď povolil použití odkazu synapse. Další informace najdete v tématu Vytvoření kontejnerů s povoleným analytickým úložištěm pro automatické zahájení replikace provozních dat z transakčního úložiště do analytického úložiště.

### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

[Šablona Azure Resource Manager](manage-sql-with-resource-manager.md#azure-cosmos-account-with-analytical-store) vytvoří pro rozhraní SQL API účet Azure Cosmos s povoleným odkazem na synapse. Tato šablona vytvoří účet základního (SQL) rozhraní API v jedné oblasti s kontejnerem nakonfigurovaným s povoleným analytickým standardem TTL a možností ručního škálování nebo propustnosti. Tuto šablonu nasadíte kliknutím na **nasadit do Azure** na stránce Readme.

## <a name="create-an-azure-cosmos-container-with-analytical-store"></a><a id="create-analytical-ttl"></a>Vytvoření kontejneru Azure Cosmos s analytickým úložištěm

Analytické úložiště můžete zapnout v kontejneru Azure Cosmos při vytváření kontejneru. Můžete použít Azure Portal nebo nakonfigurovat `analyticalTTL` vlastnost během vytváření kontejneru pomocí sady Azure Cosmos DB SDK.

> [!NOTE]
> V současné době můžete pro **nové** kontejnery povolit analytické úložiště (v nových i stávajících účtech).

### <a name="azure-portal"></a>portál Azure

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) nebo ke [službě Azure Cosmos Explorer](https://cosmos.azure.com/).

1. Přejděte k účtu Azure Cosmos a otevřete kartu **Průzkumník dat** .

1. Vyberte **Nový kontejner** a zadejte název své databáze, kontejneru, klíče oddílu a propustnosti. Zapněte možnost **analytické úložiště** . Po povolení analytického úložiště se vytvoří kontejner s `AnalyicalTTL` vlastností nastavenou na výchozí hodnotu-1 (nekonečné uchovávání). Toto analytické úložiště uchovává všechny historické verze záznamů.

   :::image type="content" source="./media/configure-synapse-link/create-container-analytical-store.png" alt-text="Zapnout službu analytické úložiště pro Azure Cosmos Container":::

1. Pokud jste na tomto účtu dříve nepovolili odkaz na synapse, zobrazí se výzva k tomu, abyste to učinili, protože je to předpoklad pro vytvoření kontejneru s povoleným analytickým úložištěm. Po zobrazení výzvy vyberte **Povolit odkaz na synapse**.

1. Vyberte **OK**a vytvořte kontejner Azure Cosmos s povoleným analytickým úložištěm.

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

### <a name="python-v3-sdk"></a>Python V3 SDK

Následující kód vytvoří kontejner s analytickým úložištěm pomocí sady Python SDK:

```python
import azure.cosmos.cosmos_client as cosmos_client
def create_collection_if_not_exists(cosmosEndpoint, cosmosKey, databaseName, collectionName):
    client = cosmos_client.CosmosClient(url_connection=cosmosEndpoint, auth={'masterKey': cosmosKey})

db = client.QueryDatabases("select * from c where c.id = '" + databaseName + "'").fetch_next_block()[0]
options = {
    'offerThroughput': 1000
}

container_definition = {
    'id': collectionName,
    "partitionKey": {  
        "paths": [  
        "/id"  
        ],  
        "kind": "Hash" 
    },
    "indexingPolicy": {  
    "indexingMode": "consistent",  
    "automatic": True,  
    "includedPaths": [],  
    "excludedPaths": [{
        "path": "/*"
    }]  
    },
    "defaultTtl": -1,
    "analyticalStorageTtl": -1
}

container = client.CreateContainer(db['_self'], container_definition, options)
```

### <a name="update-the-analytical-store-time-to-live"></a><a id="update-analytical-ttl"></a>Aktualizace doby analytického úložiště na Live

Po povolení analytického úložiště s konkrétní hodnotou TTL můžete tuto hodnotu později aktualizovat na jinou platnou hodnotu. Tuto hodnotu můžete aktualizovat pomocí webu Azure Portal nebo sad SDK. Informace o různých možnostech konfigurace analytického standardu TTL najdete v článku věnovaném [hodnotám analytického TTL](analytical-store-introduction.md#analytical-ttl) .

#### <a name="azure-portal"></a>portál Azure

Pokud jste vytvořili kontejner analytického úložiště s povoleným Azure Portal, obsahuje výchozí analytickou hodnotu TTL-1. Tuto hodnotu můžete aktualizovat pomocí následujících kroků:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) nebo ke [službě Azure Cosmos Explorer](https://cosmos.azure.com/).

1. Přejděte k účtu Azure Cosmos a otevřete kartu **Průzkumník dat** .

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

## <a name="connect-to-a-synapse-workspace"></a><a id="connect-to-cosmos-database"></a>Připojení k pracovnímu prostoru synapse

Použijte pokyny v tématu [připojení k Azure synapse](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md) , jak získat přístup k databázi Azure Cosmos DB z Azure synapse Analytics studia pomocí Azure synapse Link.

## <a name="query-using-synapse-spark"></a><a id="query-analytical-store"></a>Dotazování pomocí synapse Spark

Postupujte podle pokynů v článku [dotaz Azure Cosmos DB analytické úložiště](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md) pro dotazování pomocí synapse Spark. Tento článek obsahuje několik příkladů, jak můžete pracovat s analytickým úložištěm z synapse gest. Tato gesta se zobrazí po kliknutí pravým tlačítkem na kontejner. Pomocí gest můžete rychle vygenerovat kód a vylepšit ho podle svých potřeb. Jsou také ideální pro zjišťování dat jediným kliknutím.

## <a name="getting-started-with-azure-synpase-link---samples"></a><a id="cosmosdb-synapse-link-samples"></a>Začínáme s propojením Azure Synpase – ukázky

Ukázky, které vám pomůžou začít s propojením Azure synapse na [GitHubu](https://aka.ms/cosmosdb-synapselink-samples), najdete v ukázkách. Tato předvádí ucelená řešení s využitím scénářů IoT a Retail.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících dokumentech:

* [Odkaz na Azure synapse pro Azure Cosmos DB.](synapse-link.md)

* [Přehled analytického úložiště Azure Cosmos DB](analytical-store-introduction.md)

* [Nejčastější dotazy týkající se Azure Cosmos DB odkaz na synapse.](synapse-link-frequently-asked-questions.md)

* [Apache Spark ve službě Azure synapse Analytics](../synapse-analytics/spark/apache-spark-concepts.md).

* [SQL Server bez a na vyžádání v Azure synapse Analytics](../synapse-analytics/sql/on-demand-workspace-overview.md).
