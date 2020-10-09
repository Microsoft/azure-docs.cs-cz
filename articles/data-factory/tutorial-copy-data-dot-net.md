---
title: Kopírování dat z Azure Blob Storage do Azure SQL Database
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
ms.openlocfilehash: b2293c0dd74903921abb58037afd8eb5db3659d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85513257"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Kopírování dat z objektu blob Azure do Azure SQL Database pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto kurzu vytvoříte kanál datové továrny, který kopíruje data ze služby Azure Blob Storage do Azure SQL Database. Schéma konfigurace v tomto kurzu se vztahuje na kopírování z úložiště dat založeného na souborech do relačního úložiště dat. Seznam úložišť dat podporovaných jako zdroje a jímky najdete v tématu [podporovaná úložiště dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření propojených služeb Azure Storage a Azure SQL Database
> * Vytvoření datových sad objektů blob Azure a Azure SQL Database
> * Vytvoření kanálu obsahujícího aktivitu kopírování
> * Zahajte spuštění kanálu.
> * Monitorování spuštění aktivit a kanálu

Tento kurz používá .NET SDK. K interakci s Azure Data Factory můžete použít jiné mechanismy. Podívejte se na ukázky v části **rychlé starty**.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* *Účet Azure Storage*. Úložiště objektů blob použijete jako *zdrojové* úložiště dat. Pokud nemáte účet úložiště Azure, přečtěte si téma [Vytvoření účtu úložiště pro obecné účely](../storage/common/storage-account-create.md).
* *Azure SQL Database*. Tuto databázi použijete jako úložiště dat *jímky*. Pokud nemáte databázi v Azure SQL Database, přečtěte si téma [Vytvoření databáze v Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md).
* *Visual Studio*. Návod v tomto článku používá Visual Studio 2019.
* *[Sada Azure SDK pro .NET](/dotnet/azure/dotnet-tools)*
* *Azure Active Directory aplikace* Pokud nemáte aplikaci Azure Active Directory, přečtěte si část [Vytvoření aplikace Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) tématu [Postupy: použití portálu k vytvoření aplikace služby Azure AD](../active-directory/develop/howto-create-service-principal-portal.md). Zkopírujte následující hodnoty pro použití v pozdějších krocích: **ID aplikace (klienta)**, **ověřovací klíč**a **ID adresáře (tenant)**. Podle pokynů ve stejném článku přiřaďte aplikaci k roli **přispěvatele** .

### <a name="create-a-blob-and-a-sql-table"></a>Vytvoření objektu blob a tabulky SQL

Teď připravte svůj objekt blob Azure a Azure SQL Database pro kurz vytvořením zdrojového blogu a tabulky SQL jímky.

#### <a name="create-a-source-blob"></a>Vytvoření zdrojového objektu blob

Nejdřív vytvořte zdrojový objekt BLOB tak, že vytvoříte kontejner a nahrajete do něj vstupní textový soubor:

1. Otevřete Poznámkový blok. Zkopírujte následující text a uložte ho místně do souboru s názvem *inputEmp.txt*.

    ```inputEmp.txt
    John|Doe
    Jane|Doe
    ```

2. Pomocí nástroje, jako je například [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) , vytvořte kontejner *adfv2tutorial* a odešlete soubor *inputEmp.txt* do kontejneru.

#### <a name="create-a-sink-sql-table"></a>Vytvoření tabulky SQL jímky

Dále vytvořte tabulku jímky SQL:

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

2. Umožněte službám Azure přístup k SQL Database. Ujistěte se, že ve svém serveru povolíte přístup ke službám Azure, aby služba Data Factory mohla zapisovat data do SQL Database. Pokud chcete toto nastavení ověřit a zapnout, proveďte následující kroky:

    1. Pro správu SQL serveru přejdete na [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **SQL servery**.

    2. Vyberte svůj server.

    3. V záhlaví **zabezpečení** nabídky serveru SQL vyberte možnost **brány firewall a virtuální sítě**.

    4. Na stránce **Brána firewall a virtuální sítě** v části **Povolení přístupu ke službám a prostředkům Azure pro přístup k tomuto serveru**vyberte **zapnuto**.

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

Pomocí sady Visual Studio vytvořte konzolovou aplikaci C# .NET.

1. Otevřete sadu Visual Studio.
2. V okně **Start** vyberte **vytvořit nový projekt**.
3. V okně **vytvořit nový projekt** vyberte v seznamu typů projektů verzi **aplikace konzoly (.NET Framework)** jazyka C#. Pak vyberte **Další**.
4. V okně **Konfigurovat nový projekt** zadejte **název projektu** *ADFv2Tutorial*. V poli **umístění**vyhledejte a/nebo vytvořte adresář, do kterého chcete projekt uložit. Potom vyberte **Vytvořit**. Nový projekt se zobrazí v integrovaném vývojovém prostředí sady Visual Studio.

## <a name="install-nuget-packages"></a>Instalace balíčků NuGet

Dále nainstalujte požadované balíčky knihovny pomocí Správce balíčků NuGet.

1. V řádku nabídek klikněte na **nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků**.
2. V podokně **konzoly Správce balíčků** spusťte následující příkazy pro instalaci balíčků. Informace o Azure Data Factory balíčku NuGet najdete v tématu [Microsoft. Azure. Management. DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

    ```package manager console
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -PreRelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Vytvoření klienta datové továrny

Pomocí těchto kroků vytvořte klienta datové továrny.

1. Otevřete *program.cs*a pak přepište existující `using` příkazy pomocí následujícího kódu, abyste mohli přidat odkazy na obory názvů.

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

2. Do `Main` metody, která nastavuje proměnné, přidejte následující kód. Hodnoty 14 zástupných symbolů nahraďte vlastními hodnotami.

    Seznam oblastí Azure, ve kterých je Data Factory aktuálně k dispozici, najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). V rozevíracím seznamu **produkty** vyberte možnost **Procházet**  >  **Analytics**  >  **Data Factory**. Pak v rozevíracím seznamu **oblasti** vyberte oblasti, které vás zajímají. Zobrazí se mřížka se stavem dostupnosti Data Factory produktů pro vybrané oblasti.

    > [!NOTE]
    > Úložiště dat, například Azure Storage a Azure SQL Database, a výpočetní služby, jako je HDInsight, které Data Factory používají, můžou být v jiných oblastech, než kolik zvolíte pro Data Factory.

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

3. Do `Main` metody, která vytvoří instanci třídy, přidejte následující kód `DataFactoryManagementClient` . Tento objekt použijete k vytvoření datové továrny, propojené služby, datových sad a kanálu. Použijete ho také k monitorování podrobných informací o spuštění kanálu.

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

Do `Main` metody, která vytvoří *datovou továrnu*, přidejte následující kód.

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

V tomto kurzu vytvoříte dvě propojené služby pro zdroj a jímku, v uvedeném pořadí.

### <a name="create-an-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage

Do `Main` metody, která vytvoří *propojenou službu Azure Storage*, přidejte následující kód. Informace o podporovaných vlastnostech a podrobnostech najdete v tématu [Vlastnosti propojené služby Azure Blob](connector-azure-blob-storage.md#linked-service-properties).

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

Do `Main` metody, která vytvoří *propojenou službu Azure SQL Database*, přidejte následující kód. Informace o podporovaných vlastnostech a podrobnostech najdete v článku [Vlastnosti propojené služby Azure SQL Database](connector-azure-sql-database.md#linked-service-properties).

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

V této části vytvoříte dvě datové sady: jeden pro zdroj, druhý pro jímku.

### <a name="create-a-dataset-for-source-azure-blob"></a>Vytvoření datové sady pro zdrojový objekt blob Azure

Přidejte následující kód do `Main` metody, která vytvoří *datovou sadu objektů BLOB v Azure*. Informace o podporovaných vlastnostech a podrobnostech najdete v tématu [Vlastnosti datové sady objektů BLOB v Azure](connector-azure-blob-storage.md#dataset-properties).

Nadefinujete datovou sadu, která představuje zdrojová data v objektu blob Azure. Tato datová sada Blob odkazuje na propojenou službu Azure Storage, kterou jste vytvořili v předchozím kroku, a popisuje:

- Umístění objektu blob, ze kterého se má kopírovat: `FolderPath` a `FileName`
- Formát objektu blob, který označuje, jak analyzovat obsah: `TextFormat` a jeho nastavení, například Oddělovač sloupců
- Datová struktura, včetně názvů sloupců a datových typů, které jsou v tomto příkladu namapovány na tabulku SQL jímky

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

Do `Main` metody, která vytvoří *datovou sadu Azure SQL Database*, přidejte následující kód. Informace o podporovaných vlastnostech a podrobnostech najdete v tématu [Azure SQL Database vlastnosti datové sady](connector-azure-sql-database.md#dataset-properties).

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

Do `Main` metody, která vytvoří *kanál s aktivitou kopírování*, přidejte následující kód. V tomto kurzu tento kanál obsahuje jednu aktivitu: `CopyActivity` , která přebírá datovou sadu objektů BLOB jako zdroj a datovou sadu SQL jako jímku. Informace o podrobnostech o aktivitě kopírování najdete [v tématu aktivita kopírování v Azure Data Factory](copy-activity-overview.md).

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

Do `Main` metody, která *aktivuje spuštění kanálu*, přidejte následující kód.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Monitorování spuštění kanálu

Nyní vložte kód pro kontrolu stavu běhu kanálu a Získejte podrobné informace o spuštění aktivity kopírování.

1. Přidejte následující kód do `Main` metody pro nepřetržitou kontrolu stavu spuštění kanálu, dokud nedokončí kopírování dat.

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

2. Přidejte následující kód do `Main` metody, která načte podrobnosti o spuštění aktivity kopírování, například velikost dat, která byla načtena nebo zapsána.

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

Sestavte aplikaci tak, že kliknete na **sestavit**sestavení  >  **řešení**. Pak spusťte aplikaci výběrem **ladění**  >  **Spustit ladění**a ověřte spuštění kanálu.

Konzola vytiskne průběh vytváření datové továrny, propojené služby, datové sady, kanál a spuštění kanálu. Potom zkontroluje stav spuštění kanálu. Počkejte, dokud se nezobrazí podrobnosti o spuštění aktivity kopírování s velikostí přečtených a zapsaných dat. Pak můžete pomocí nástrojů, jako je SQL Server Management Studio (SSMS) nebo Visual Studio, připojit se k cíli Azure SQL Database a ověřit, zda cílová tabulka obsahuje zkopírovaná data.

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

Kanál v této ukázce kopíruje data z jednoho umístění do jiného umístění v úložišti objektů blob Azure. Naučili jste se:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření propojených služeb Azure Storage a Azure SQL Database
> * Vytvoření datových sad objektů blob Azure a Azure SQL Database
> * Vytvoření kanálu obsahujícího aktivitu kopírování.
> * Zahajte spuštění kanálu.
> * Monitorování spuštění aktivit a kanálu

Pokud se chcete dozvědět víc o kopírování dat z místního prostředí do cloudu, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Kopírování dat z místního prostředí do cloudu](tutorial-hybrid-copy-powershell.md)
