---
title: Kopírování dat z úložiště objektů blob Azure do databáze Azure SQL
description: Tento kurz obsahuje podrobné pokyny pro kopírování dat z Azure Blob Storage do Azure SQL Database.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 11/08/2019
ms.author: jingwang
ms.openlocfilehash: a835e67b1091a55c832955d8dac8615289a6d99e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418690"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Kopírování dat z objektu blob Azure do Azure SQL Database pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto kurzu vytvoříte kanál datové továrny, který kopíruje data ze služby Azure Blob Storage do Azure SQL Database. Schéma konfigurace v tomto kurzu se vztahuje na kopírování z úložiště dat založeného na souborech do relačního úložiště dat. Seznam úložišť dat podporovaných jako zdroje a propady naleznete v [tématu podporovaná úložiště a formáty dat](copy-activity-overview.md#supported-data-stores-and-formats).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření propojených služeb Azure Storage a Azure SQL Database
> * Vytvoření datových sad objektů blob Azure a Azure SQL Database
> * Vytvoření kanálu obsahujícího aktivitu kopírování
> * Zahajte spuštění kanálu.
> * Monitorování spuštění aktivit a kanálu

Tento kurz používá .NET SDK. K interakci s Azure Data Factory můžete použít jiné mechanismy. viz ukázky v části **Rychlé starty**.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* *Účet Azure Storage*. Úložiště objektů blob použijete jako *zdrojové* úložiště dat. Pokud nemáte účet úložiště Azure, přečtěte [si tématu Vytvoření účtu úložiště pro obecné účely](../storage/common/storage-account-create.md).
* *Azure SQL Database*. Tuto databázi použijete jako úložiště dat *jímky*. Pokud nemáte azure sql database, najdete v [tématu vytvoření databáze Azure SQL](../sql-database/sql-database-single-database-get-started.md).
* *Visual Studio*. Návod v tomto článku používá Visual Studio 2019.
* *[Sada Azure SDK pro rozhraní .NET](/dotnet/azure/dotnet-tools)*.
* *Aplikace Azure Active Directory*. Pokud nemáte aplikaci Azure Active Directory, přečtěte si část [Vytvoření aplikace Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) v části [Postup: Pomocí portálu vytvořte aplikaci Azure AD](../active-directory/develop/howto-create-service-principal-portal.md). Zkopírujte následující hodnoty pro použití v pozdějších krocích: **ID aplikace (klienta),** **ověřovací klíč**a **ID adresáře (klienta).** Přiřaďte aplikaci roli **přispěvatele** podle pokynů ve stejném článku.

### <a name="create-a-blob-and-a-sql-table"></a>Vytvoření objektu blob a tabulky SQL

Teď připravte azure blob a Azure SQL database pro kurz vytvořením zdrojového blogu a tabulky SQL jímky.

#### <a name="create-a-source-blob"></a>Vytvoření zdrojového objektu blob

Nejprve vytvořte zdrojový objekt blob vytvořením kontejneru a nahráním vstupního textového souboru:

1. Otevřete poznámkový blok. Zkopírujte následující text a uložte jej místně do souboru s názvem *inputEmp.txt*.

    ```inputEmp.txt
    John|Doe
    Jane|Doe
    ```

2. Pomocí nástroje, jako je [například Průzkumník úložiště Azure](https://azure.microsoft.com/features/storage-explorer/) k vytvoření kontejneru *adfv2tutorial* a k nahrání souboru *inputp.txt* do kontejneru.

#### <a name="create-a-sink-sql-table"></a>Vytvoření tabulky SQL jímky

Dále vytvořte tabulku SQL jímky:

1. Pomocí následujícího skriptu SQL vytvořte tabulku *dbo.emp* v Azure SQL Database.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Povolte službám Azure přístup k SQL serveru. Ujistěte se, že povolíte přístup ke službám Azure na serveru Azure SQL, aby služba Data Factory mohla zapisovat data na váš server Azure SQL. Pokud chcete toto nastavení ověřit a zapnout, proveďte následující kroky:

    1. Přejděte na [portál Azure](https://portal.azure.com) a spravujte svůj SQL server. Vyhledejte a vyberte **servery SQL**.

    2. Vyberte svůj server.

    3. Pod nadpisem **Zabezpečení** nabídky serveru SQL vyberte **brány firewall a virtuální sítě**.

    4. Na stránce **Brána firewall a virtuální sítě** vyberte v části **Povolit službám a prostředkům Azure přístup k tomuto serveru**v **možnosti ZAPNUTO**.

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

Pomocí sady Visual Studio vytvořte aplikaci konzoly C# .NET.

1. Otevřete sadu Visual Studio.
2. V okně **Start** vyberte **Vytvořit nový projekt**.
3. V okně **Vytvořit nový projekt** zvolte verzi aplikace Console App **(.NET Framework)** jazyka C# ze seznamu typů projektů. Pak vyberte **Další**.
4. V okně **Konfigurovat nový projekt** zadejte název **projektu** *ADFv2Tutorial*. V **části Umístění**vyhledejte nebo vytvořte adresář pro uložení projektu. Pak vyberte **Vytvořit**. Nový projekt se zobrazí v ide sady Visual Studio.

## <a name="install-nuget-packages"></a>Instalace balíčků NuGet

Dále nainstalujte požadované balíčky knihovny pomocí správce balíčků NuGet.

1. V řádku nabídek zvolte **Nástroje** > **NuGet Správce** > **balíčků Správce balíčků Konzola**.
2. V podokně **Konzola Správce balíčků** spusťte následující příkazy pro instalaci balíčků. Informace o balíčku Azure Data Factory NuGet najdete v tématu [Microsoft.Azure.Management.DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

    ```package manager console
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -PreRelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Vytvoření klienta datové továrny

Chcete-li vytvořit klienta datové továrny, postupujte takto.

1. Otevřete *Program.cs*a pak `using` přepište existující příkazy následujícím kódem a přidejte odkazy na obory názvů.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Přidejte následující kód `Main` k metodě, která nastavuje proměnné. Nahraďte 14 zástupných symbolů vlastními hodnotami.

    Seznam oblastí Azure, ve kterých je data factory momentálně dostupná, najdete v [tématu Produkty dostupné podle oblastí](https://azure.microsoft.com/global-infrastructure/services/). V rozevíracím seznamu **Produkty** zvolte **Procházet** > **analytickou datovou** > **továrnu**. V rozevíracím seznamu **Oblasti** pak vyberte oblasti, které vás zajímají. Zobrazí se mřížka se stavem dostupnosti produktů datové továrny pro vybrané oblasti.

    > [!NOTE]
    > Úložiště dat, jako je Azure Storage a Azure SQL Database, a výpočetní prostředky, jako je HDInsight, které používá Data Factory, můžou být v jiných oblastech, než jaké zvolíte pro Data Factory.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "<location to create the data factory in, such as East US>";
    string dataFactoryName = "<name of data factory to create (must be globally unique)>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString =
        "Server=tcp:<your server name>.database.windows.net,1433;" +
        "Database=<your database name>;" +
        "User ID=<your username>@<your server name>;" +
        "Password=<your password>;" +
        "Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. Přidejte následující kód `Main` do metody, `DataFactoryManagementClient` která vytvoří instanci třídy. Tento objekt použijete k vytvoření datové továrny, propojené služby, datových sad a kanálu. Použijete ho také k monitorování podrobných informací o spuštění kanálu.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync(
        "https://management.azure.com/", cc
    ).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

Přidejte následující kód `Main` do metody, která vytváří *datovou továrnu*.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};

client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings)
);

while (
    client.Factories.Get(
        resourceGroup, dataFactoryName
    ).ProvisioningState == "PendingCreation"
)
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>Vytvoření propojených služeb

V tomto kurzu vytvoříte dvě propojené služby pro zdroj a jímky, v uvedeném pořadí.

### <a name="create-an-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage

Přidejte následující kód `Main` k metodě, která vytvoří *propojenou službu Azure Storage*. Informace o podporovaných vlastnostech a podrobnostech najdete v [tématu Vlastnosti propojené služby Azure Blob](connector-azure-blob-storage.md#linked-service-properties).

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey
        )
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings)
);
```

### <a name="create-an-azure-sql-database-linked-service"></a>Vytvoření propojené služby Azure SQL Database

Přidejte následující kód `Main` k metodě, která vytvoří *propojenou službu Azure SQL Database*. Informace o podporovaných vlastnostech a podrobnostech naleznete v tématu [Vlastnosti propojené služby Azure SQL Database](connector-azure-sql-database.md#linked-service-properties).

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings)
);
```

## <a name="create-datasets"></a>Vytvoření datových sad

V této části vytvoříte dvě datové sady: jednu pro zdroj, druhou pro jímku.

### <a name="create-a-dataset-for-source-azure-blob"></a>Vytvoření datové sady pro zdrojový objekt blob Azure

Přidejte následující kód `Main` k metodě, která vytvoří *datovou sadu objektů blob Azure*. Informace o podporovaných vlastnostech a podrobnostech najdete v [tématu Vlastnosti datové sady Objektů blob Azure](connector-azure-blob-storage.md#dataset-properties).

Nadefinujete datovou sadu, která představuje zdrojová data v objektu blob Azure. Tato datová sada Blob odkazuje na propojenou službu Azure Storage, kterou jste vytvořili v předchozím kroku, a popisuje:

- Umístění objektu blob, ze `FolderPath` který chcete kopírovat: a`FileName`
- Formát objektu blob označující, jak `TextFormat` analyzovat obsah: a jeho nastavení, jako je například oddělovač sloupců
- Struktura dat, včetně názvů sloupců a datových typů, které se mapují v tomto příkladu do tabulky SQL jímky

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement { Name = "FirstName", Type = "String" },
            new DatasetDataElement { Name = "LastName", Type = "String" }
        }
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings)
);
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Vytvoření datové sady pro Azure SQL Database jímky

Přidejte následující kód `Main` do metody, která vytvoří *datovou sadu Azure SQL Database*. Informace o podporovaných vlastnostech a podrobnostech naleznete v [tématu Vlastnosti datové sady Azure SQL Database](connector-azure-sql-database.md#dataset-properties).

Definujete datovou sadu, která představuje data jímky ve službě Azure SQL Database. Tato datová sada odkazuje na propojenou službu Azure SQL Database, kterou jste vytvořili v předchozím kroku. Určuje také tabulku SQL, který obsahuje zkopírovaná data.

```csharp
// Create an Azure SQL Database dataset
Console.WriteLine("Creating dataset " + sqlDatasetName + "...");
DatasetResource sqlDataset = new DatasetResource(
    new AzureSqlTableDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = sqlDbLinkedServiceName
        },
        TableName = azureSqlTableName
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings)
);
```

## <a name="create-a-pipeline"></a>Vytvoření kanálu

Přidejte následující kód `Main` k metodě, která vytvoří *kanál s aktivitou kopírování*. V tomto kurzu tento kanál `CopyActivity`obsahuje jednu aktivitu: , který bere v datové sadě objektu Blob jako zdroj a datovou sadu SQL jako jímky. Informace o podrobnostech o aktivitě kopírování najdete [v tématu Kopírování aktivity v Azure Data Factory](copy-activity-overview.md).

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToSQL",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference() { ReferenceName = blobDatasetName }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference { ReferenceName = sqlDatasetName }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings)
);
```

## <a name="create-a-pipeline-run"></a>Vytvoření spuštění kanálu

Přidejte následující kód `Main` k metodě, která *aktivuje spuštění kanálu*.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Monitorování spuštění kanálu

Nyní vložte kód pro kontrolu stavů spuštění kanálu a získání podrobností o spuštění aktivity kopírování.

1. Přidejte následující kód `Main` k metodě průběžně kontrolovat stavy kanálu spustit, dokud nedokončí kopírování dat.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(
            resourceGroup, dataFactoryName, runResponse.RunId
        );
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Přidejte následující kód `Main` k metodě, která načítá podrobnosti spuštění aktivity kopírování, například velikost dat, která byla přečtena nebo zapsána.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    RunFilterParameters filterParams = new RunFilterParameters(
        DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)
    );

    ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
        resourceGroup, dataFactoryName, runResponse.RunId, filterParams
    );

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(queryResponse.Value.First().Output);
    }
    else
        Console.WriteLine(queryResponse.Value.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Spuštění kódu

Vytvořte aplikaci výběrem **sestavení** > **sestavení řešení**. Potom spusťte aplikaci výběrem **ladění začít** > **ladění**a ověřte spuštění kanálu.

Konzola vytiskne průběh vytváření datové továrny, propojené služby, datové sady, kanál a spuštění kanálu. Potom zkontroluje stav spuštění kanálu. Počkejte, až se zobrazí podrobnosti o aktivitě kopírování spustit s velikostí dat čtení /zápisu. Potom pomocí nástrojů, jako je SQL Server Management Studio (SSMS) nebo Visual Studio, můžete se připojit k cílové Azure SQL Database a zkontrolujte, zda cílová tabulka, kterou jste zadali obsahuje zkopírovaná data.

### <a name="sample-output"></a>Ukázkový výstup

```json
Creating a data factory AdfV2Tutorial...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
      }
    }
  }
}
Creating linked service AzureSqlDbLinkedService...
{
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": "adfv2tutorial/",
      "fileName": "inputEmp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "|"
      }
    },
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureStorageLinkedService"
    }
  }
}
Creating dataset SqlDataset...
{
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": "dbo.emp"
    },
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureSqlDbLinkedService"
    }
  }
}
Creating pipeline Adfv2TutorialBlobToSqlCopy...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "BlobDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SqlDataset"
          }
        ],
        "name": "CopyFromBlobToSQL"
      }
    ]
  }
}
Creating pipeline run...
Pipeline run ID: 1cd03653-88a0-4c90-aabc-ae12d843e252
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 18,
  "dataWritten": 28,
  "rowsCopied": 2,
  "copyDuration": 2,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
  "usedDataIntegrationUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```

## <a name="next-steps"></a>Další kroky

Kanál v této ukázce kopíruje data z jednoho umístění do jiného umístění v úložišti objektů blob Azure. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření propojených služeb Azure Storage a Azure SQL Database
> * Vytvoření datových sad objektů blob Azure a Azure SQL Database
> * Vytvořte kanál obsahující aktivitu kopírování.
> * Zahajte spuštění kanálu.
> * Monitorování spuštění aktivit a kanálu

Pokud se chcete dozvědět víc o kopírování dat z místního prostředí do cloudu, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Kopírování dat z místního prostředí do cloudu](tutorial-hybrid-copy-powershell.md)
