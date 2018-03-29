---
title: Kopírování dat z Azure Blob Storage do SQL Database | Dokumentace Microsoftu
description: Tento kurz obsahuje podrobné pokyny pro kopírování dat z Azure Blob Storage do Azure SQL Database.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: bfbafa2edb1d9195760a99f63113d28d3a978a78
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Kopírování dat z objektu blob Azure do Azure SQL Database pomocí Azure Data Factory
V tomto kurzu vytvoříte kanál datové továrny, který kopíruje data ze služby Azure Blob Storage do Azure SQL Database. Schéma konfigurace v tomto kurzu se vztahuje na kopírování z úložiště dat založeného na souborech do relačního úložiště dat. Seznam úložišť dat, která jsou podporovaná jako zdroje a jímky, najdete v tabulce [podporovaných úložišť dat](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), prostudujte si [dokumentaci služby Data Factory verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření propojených služeb Azure Storage a Azure SQL Database
> * Vytvoření datových sad objektů blob Azure a Azure SQL Database
> * Vytvoření kanálu obsahujícího aktivitu kopírování
> * Zahájení spuštění kanálu
> * Monitorování spuštění aktivit a kanálu

Tento kurz používá .NET SDK. K interakci s Azure Data Factory můžete použít další mechanismy – podívejte se na ukázky v části Šablony Rychlý start.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* **Účet služby Azure Storage**. Úložiště objektů blob použijete jako **zdrojové** úložiště dat. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../storage/common/storage-create-storage-account.md#create-a-storage-account), kde najdete kroky pro jeho vytvoření.
* **Azure SQL Database**. Tuto databázi použijete jako úložiště dat **jímky**. Pokud Azure SQL Database nemáte, přečtěte si článek věnovaný [vytvoření databáze Azure SQL](../sql-database/sql-database-get-started-portal.md), kde najdete kroky pro její vytvoření.
* **Visual Studio** 2015 nebo 2017. Názorný postup v tomto článku využívá Visual Studio 2017.
* **Stáhněte sadu [Azure .NET SDK](http://azure.microsoft.com/downloads/)** a nainstalujte ji.
* **V Azure Active Directory** vytvořte aplikaci s využitím [těchto pokynů](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Poznamenejte si následující hodnoty, které použijete v dalších krocích: **ID aplikace**, **ověřovací klíč** a **ID tenanta**. Podle pokynů ve stejném článku přiřaďte aplikaci roli **Přispěvatel**.

### <a name="create-a-blob-and-a-sql-table"></a>Vytvoření objektu blob a tabulky SQL

Teď připravte objekt blob Azure a Azure SQL Database pro tento kurz provedením následující kroků:

#### <a name="create-a-source-blob"></a>Vytvoření zdrojového objektu blob

1. Spusťte Poznámkový blok. Zkopírujte následující text a uložte ho na disk jako soubor **inputEmp.txt**.

    ```
    John|Doe
    Jane|Doe
    ```

2. Pomocí nástrojů, jako je [Průzkumník služby Azure Storage](http://storageexplorer.com/), vytvořte kontejner **adfv2tutorial** a odešlete soubor **inputEmp.txt** do tohoto kontejneru.

#### <a name="create-a-sink-sql-table"></a>Vytvoření tabulky SQL jímky

1. Pomocí následujícího skriptu SQL vytvořte tabulku **dbo.emp** v Azure SQL Database.

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

2. Povolte službám Azure přístup k SQL serveru. Zkontrolujte, že nastavení **Povolit přístup ke službám Azure** je pro server SQL Azure **ZAPNUTÉ**, aby služba Data Factory mohla na server Azure SQL zapisovat data. Pokud chcete toto nastavení ověřit a zapnout, proveďte následující kroky:

    1. Klikněte na **Další služby** na levé straně a potom klikněte na **Servery SQL**.
    2. Vyberte svůj server a v části **NASTAVENÍ** klikněte na **Brána firewall**.
    3. Na stránce **Nastavení brány firewall** klikněte na **ZAPNUTO** u možnosti **Povolit přístup ke službám Azure**.


## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

Pomocí sady Visual Studio 2015/2017 vytvořte konzolovou aplikaci C# .NET.

1. Spusťte **Visual Studio**.
2. Klikněte na **Soubor**, přejděte na **Nový** a klikněte na **Projekt**.
3. V seznamu typů projektů napravo vyberte **Visual C#** -> **Aplikace konzoly (.NET Framework)**. Vyžaduje se .NET verze 4.5.2 nebo novější.
4. Jako název zadejte **ADFv2Tutorial**.
5. Projekt vytvoříte kliknutím na **OK**.

## <a name="install-nuget-packages"></a>Instalace balíčků NuGet

1. Klikněte na **Nástroje**  ->  **Správce balíčků NuGet**  ->  **Konzola správce balíčků**.
2. V **konzole Správce balíčků** spusťte následující příkazy pro instalaci balíčků:

    ```
    Install-Package Microsoft.Azure.Management.DataFactory -Prerelease
    Install-Package Microsoft.Azure.Management.ResourceManager -Prerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Vytvoření klienta datové továrny

1. Otevřete soubor **Program.cs** a vložte do něj následující příkazy. Přidáte tak odkazy na obory názvů.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
    
2. Do metody **Main** přidejte následující kód, který nastaví proměnné. Zástupné znaky nahraďte vlastními hodnotami. Data Factory V2 v současné době umožňuje vytváření datových továren jenom v oblastech Východní USA, Východní USA 2 a Západní Evropa. Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "East US";
    string dataFactoryName = "<specify the name of a data factory to create. It must be globally unique.>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString = "Server=tcp:<your server name>.database.windows.net,1433;Database=<your database name>;User ID=<your username>@<your server name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. Do metody **Main** přidejte následující kód, který vytvoří instanci třídy **DataFactoryManagementClient**. Tento objekt použijete k vytvoření datové továrny, propojené služby, datových sad a kanálu. Použijete ho také k monitorování podrobných informací o spuštění kanálu.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

Do metody **Main** přidejte následující kód, který vytvoří **datovou továrnu**.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()

};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>Vytvoření propojených služeb

V tomto kurzu vytvoříte dvě propojené služby pro zdroj a jímku:

### <a name="create-an-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage

Do metody **Main** přidejte následující kód, který vytvoří **propojenou službu Azure Storage**. V tématu věnovaném [vlastnostem propojených služeb Azure Blob](connector-azure-blob-storage.md#linked-service-properties) se o podporovaných vlastnostech dozvíte víc.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
```

### <a name="create-an-azure-sql-database-linked-service"></a>Vytvoření propojené služby Azure SQL Database

Do metody **Main** přidejte následující kód, který vytvoří **propojenou službu Azure SQL Database**. V tématu věnovaném [vlastnostem propojených služeb Azure SQL Database](connector-azure-sql-database.md#linked-service-properties) se o podporovaných vlastnostech dozvíte víc.

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings));
```

## <a name="create-datasets"></a>Vytvoření datových sad

V této části vytvoříte dvě datové sady, jednu pro zdroj a druhou pro jímku. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Vytvoření datové sady pro zdrojový objekt blob Azure

Do metody **Main** přidejte následující kód, který vytvoří **datovou sadu objektů blob Azure**. V tématu věnovaném [vlastnostem datových sad objektů blob Azure](connector-azure-blob-storage.md#dataset-properties) se o podporovaných vlastnostech dozvíte víc.

Definujete datovou sadu, která představuje zdroj dat ve službě Azure Blob. Tato datová sada Blob odkazuje na propojenou službu Azure Storage, kterou jste vytvořili v předchozím kroku, a popisuje:

- Umístění objektu blob, ze kterého se má kopírovat: **FolderPath** a **FileName**.
- Formát objektu udávající, jak analyzovat obsah: **TextFormat** a příslušná nastavení (například oddělovač sloupců).
- Strukturu dat, včetně názvů sloupců a typů dat, které se v tomto případě mapují na tabulku SQL jímky.

```csharp
// Create a Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement
            {
                Name = "FirstName",
                Type = "String"
            },
            new DatasetDataElement
            {
                Name = "LastName",
                Type = "String"
            }
        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Vytvoření datové sady pro Azure SQL Database jímky

Do metody **Main** přidejte následující kód, který vytvoří **datovou sadu Azure SQL Database**. V tématu věnovaném [vlastnostem datových sad Azure SQL Database](connector-azure-sql-database.md#dataset-properties) se o podporovaných vlastnostech dozvíte víc.

Definujete datovou sadu, která představuje data jímky ve službě Azure SQL Database. Tato datová sada odkazuje na propojenou službu Azure SQL Database, kterou jste vytvořili v předchozím kroku. Určuje také tabulku SQL, který obsahuje zkopírovaná data. 

```csharp
// Create a Azure SQL Database dataset
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
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Vytvoření kanálu

Do metody **Main** přidejte následující kód, který vytvoří **kanál s aktivitou kopírování**. V tomto kurzu kanál obsahuje jednu aktivitu: aktivitu kopírování, která jako zdroj používá datovou sadu objektů blob jako zdroj a jako jímku používá jinou datovou sadu SQL. Podrobné informace o aktivitě kopírování najdete v tématu [Přehled aktivit kopírování](copy-activity-overview.md).

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
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = sqlDatasetName
                }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Vytvoření spuštění kanálu

Do metody **Main** přidejte následující kód, který **aktivuje spuštění kanálu**.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Monitorování spuštění kanálu

1. Do metody **Main** přidejte následující kód, který nepřetržitě kontroluje stav spuštění kanálu, dokud se kopírování dat nedokončí.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Do metody **Main** přidejte následující kód, který načte podrobnosti o spuštění aktivity kopírování, například velikost načtených/zapsaných dat.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
 
    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
    }
    else
        Console.WriteLine(activityRuns.First().Error);
    
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Spuštění kódu

Sestavte a spusťte aplikaci a potom ověřte spuštění kanálu.

Konzola vytiskne průběh vytváření datové továrny, propojené služby, datové sady, kanál a spuštění kanálu. Potom zkontroluje stav spuštění kanálu. Počkejte, dokud aktivita kopírování nezobrazí údaje o velikosti načtených/zapsaných dat. Potom použijte nástroje, jako je SSMS (SQL Server Management Studio) nebo Visual Studio, připojte se k cílové službě Azure SQL a zkontrolujte, jestli se data zkopírovala do tabulky, kterou jste zadali.

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
  "usedCloudDataMovementUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```


## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z jednoho umístění do jiného umístění v úložišti objektů blob v Azure. Naučili jste se tyto postupy: 

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření propojených služeb Azure Storage a Azure SQL Database
> * Vytvoření datových sad objektů blob Azure a Azure SQL Database
> * Vytvoření kanálu obsahujícího aktivitu kopírování
> * Zahájení spuštění kanálu
> * Monitorování spuštění aktivit a kanálu


Pokud se chcete dozvědět víc o kopírování dat z místního prostředí do cloudu, přejděte k následujícímu kurzu: 

> [!div class="nextstepaction"]
>[Kopírování dat z místního prostředí do cloudu](tutorial-hybrid-copy-powershell.md)
