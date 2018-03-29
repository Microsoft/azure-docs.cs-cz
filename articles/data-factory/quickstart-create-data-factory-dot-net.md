---
title: Vytvoření datové továrny Azure pomocí .NET | Dokumentace Microsoftu
description: Vytvořte datovou továrnu Azure ke zkopírování dat z jednoho umístění v úložišti objektů blob v Azure do jiného.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: 7e3fd12b2157d28c5d61bfa8d5fb69a00557a1f9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-data-factory-and-pipeline-using-net-sdk"></a>Vytvoření datové továrny a kanálu s využitím .NET SDK
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Verze 2 – Preview](quickstart-create-data-factory-dot-net.md)

Tento rychlý start popisuje použití sady .NET SDK k vytvoření datové továrny Azure. Kanál, který vytvoříte v této datové továrně, **kopíruje** data z jedné složky do jiné složky v úložišti objektů blob Azure. Kurz předvádějící způsoby **transformace** dat pomocí Azure Data Factory najdete v tématu [Kurz: Transformace dat pomocí Sparku](transform-data-using-spark.md). 

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), prostudujte si [úvod do služby Data Factory verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
>
> Tento článek neposkytuje podrobný úvod do služby Data Factory. Úvod do služby Azure Data Factory najdete v tématu [Úvod do Azure Data Factory](introduction.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

### <a name="azure-subscription"></a>Předplatné Azure
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

### <a name="azure-roles"></a>Role Azure
Pro vytvoření instancí Data Factory musí být uživatelský účet, který použijete pro přihlášení k Azure, členem rolí **přispěvatel** nebo **vlastník** nebo **správcem** předplatného Azure. Na webu Azure Portal klikněte na **uživatelské jméno** v pravém horním rohu a vyberte **Oprávnění**. Zobrazí se oprávnění, která v příslušném předplatném máte. Pokud máte přístup k několika předplatným, vyberte odpovídající předplatné. Ukázku pokynů pro přidání uživatele k roli najdete v článku věnovaném [přidání rolí](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="azure-storage-account"></a>Účet služby Azure Storage
V tomto rychlém startu použijete účet služby Azure Storage (konkrétně služby Blob Storage) pro obecné účely jako **zdrojové** i **cílové úložiště dat**. Pokud nemáte účet úložiště Azure pro obecné účely, přečtěte si téma popisující [Vytvoření účtu úložiště](../storage/common/storage-create-storage-account.md#create-a-storage-account). 

#### <a name="get-storage-account-name-and-account-key"></a>Získání názvu a klíče účtu úložiště
V tomto rychlém startu použijete název a klíč svého účtu úložiště Azure. Následující postup předvádí kroky k získání názvu a klíče vašeho účtu úložiště. 

1. Spusťte webový prohlížeč a přejděte na [Azure Portal](https://portal.azure.com). Přihlaste se pomocí svého uživatelského jména a hesla Azure. 
2. V nabídce vlevo klikněte na **Další služby >**, použijte filtr s klíčovým slovem **úložiště** a vyberte **Účty úložiště**.

    ![Vyhledání účtu úložiště](media/quickstart-create-data-factory-dot-net/search-storage-account.png)
3. V seznamu účtů úložiště vyfiltrujte váš účet úložiště (pokud je to potřeba) a pak vyberte **váš účet úložiště**. 
4. Na stránce **Účet úložiště** vyberte v nabídce **Přístupové klíče**.

    ![Získání názvu a klíče účtu úložiště](media/quickstart-create-data-factory-dot-net/storage-account-name-key.png)
5. Zkopírujte do schránky hodnoty z polí **Název účtu úložiště** a **klíč1**. Vložte je do Poznámkového bloku nebo jiného editoru a uložte je.  

#### <a name="create-input-folder-and-files"></a>Vytvoření vstupní složky a souborů
V této části vytvoříte ve svém úložišti objektů blob v Azure kontejner objektů blob s názvem **adftutorial**. Potom v kontejneru vytvoříte složku **input** a nahrajete do ní ukázkový soubor. 

1. Na stránce **Účet úložiště** přepněte na **Přehled** a potom klikněte na **Objekty blob**. 

    ![Výběr možnosti Objekty blob](media/quickstart-create-data-factory-dot-net/select-blobs.png)
2. Na stránce **Blob Service** klikněte na panelu nástrojů na **+ Kontejner**. 

    ![Tlačítko pro přidání kontejneru](media/quickstart-create-data-factory-dot-net/add-container-button.png)    
3. V dialogovém okně **Nový kontejner** jako název zadejte **adftutorial** a klikněte na **OK**. 

    ![Zadání názvu kontejneru](media/quickstart-create-data-factory-dot-net/new-container-dialog.png)
4. V seznamu kontejnerů klikněte na **adftutorial**. 

    ![Výběr kontejneru](media/quickstart-create-data-factory-dot-net/select-adftutorial-container.png)
1. Na stránce **Kontejner** klikněte na panelu nástrojů na **Nahrát**.  

    ![Tlačítko Nahrát](media/quickstart-create-data-factory-dot-net/upload-toolbar-button.png)
6. Na stránce **Nahrát objekt blob** klikněte na **Upřesnit**.

    ![Kliknutí na odkaz Upřesnit](media/quickstart-create-data-factory-dot-net/upload-blob-advanced.png)
7. Spusťte **Poznámkový blok** a vytvořte soubor nazvaný **emp.txt** s následujícím obsahem: Uložte ho do složky **c:\ADFv2QuickStartPSH**. Pokud složka **ADFv2QuickStartPSH** neexistuje, vytvořte ji.
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. Na webu Azure Portal na stránce **Nahrát objekt blob** vyberte **emp.txt** v poli **Soubory**. 
9. Jako hodnotu pole **Nahrát do složky** zadejte **input**. 

    ![Nastavení pro nahrání objektu blob](media/quickstart-create-data-factory-dot-net/upload-blob-settings.png)    
10. Potvrďte, že je nastavená složka **input** a soubor **emp.txt** a klikněte na **Nahrát**.
11. Měli byste vidět soubor **emp.txt** a stav nahrávání v seznamu. 
12. Zavřete okno **Nahrát objekt blob** kliknutím na **X** v rohu. 

    ![Zavření okna Nahrát objekt blob](media/quickstart-create-data-factory-dot-net/close-upload-blob.png)
1. Stránku **kontejneru** nechte otevřenou. Použijete ji k ověření výstupu na konci tohoto rychlého startu.

### <a name="visual-studio"></a>Visual Studio
Názorný postup v tomto článku využívá Visual Studio 2017. Můžete také použít Visual Studio 2013 nebo 2015.

### <a name="azure-net-sdk"></a>Azure .NET SDK
Stáhněte sadu [Azure .NET SDK](http://azure.microsoft.com/downloads/) a nainstalujte ji do svého počítače.

### <a name="create-an-application-in-azure-active-directory"></a>Vytvoření aplikace v Azure Active Directory
Postupujte podle pokynů v [tomto článku](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) a proveďte následující úlohy: 

1. **Vytvořte aplikaci Azure Active Directory**. V Azure Active Directory vytvořte aplikaci reprezentující aplikaci .NET, kterou vytváříte v tomto kurzu. Jako přihlašovací adresu URL můžete poskytnout fiktivní URL, jak ukazuje článek (`https://contoso.org/exampleapp`).
2. Pomocí pokynů v části tohoto článku věnované **získání ID aplikace a ověřovacího klíče** získejte **ID aplikace** a **ověřovací klíč****. Poznamenejte si tyto hodnoty, které použijete později v tomto kurzu. 
3. Pomocí pokynů v části tohoto článku věnované **získání ID tenanta** získejte **ID tenanta**. Poznamenejte si tuto hodnotu. 
4. Přiřaďte aplikaci k roli **Přispěvatel** na úrovni předplatného, aby aplikace mohla v předplatném vytvářet datové továrny. Pomocí pokynů v části tohoto článku věnované **přiřazení aplikace k roli**. 

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

Pomocí sady Visual Studio 2013/2015/2017 vytvořte konzolovou aplikaci v C# .NET.

1. Spusťte **Visual Studio**.
2. Klikněte na **Soubor**, přejděte na **Nový** a klikněte na **Projekt**.
3. V seznamu typů projektů napravo vyberte **Visual C#** -> **Aplikace konzoly (.NET Framework)**. Vyžaduje se .NET verze 4.5.2 nebo novější.
4. Jako název zadejte **ADFv2QuickStart**.
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
    string subscriptionId = "<your subscription ID where the data factory resides>";
    string resourceGroup = "<your resource group where the data factory resides>";
    string region = "East US 2";
    string dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>";
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    // specify the container and input folder from which all files need to be copied to the output folder. 
    string inputBlobPath = "<the path to existing blob(s) to copy data from, e.g. containername/foldername>";
    //specify the contains and output folder where the files are copied
    string outputBlobPath = "<the blob path to copy data to, e.g. containername/foldername>";

    string storageLinkedServiceName = "AzureStorageLinkedService";  // name of the Azure Storage linked service
    string blobDatasetName = "BlobDataset";             // name of the blob dataset
    string pipelineName = "Adfv2QuickStartPipeline";    // name of the pipeline
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
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
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

## <a name="create-a-linked-service"></a>Vytvoření propojené služby

Do metody **Main** přidejte následující kód, který vytvoří **propojenou službu Azure Storage**.

V datové továrně vytvoříte propojené služby, abyste svá úložiště dat a výpočetní služby spojili s datovou továrnou. V tomto kurzu Rychlý start stačí vytvořit jednu propojenou službu Azure Storage pro zdroj kopírování i úložiště jímky s názvem AzureStorageLinkedService.

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

## <a name="create-a-dataset"></a>Vytvoření datové sady

Do metody **Main** přidejte následující kód, který vytvoří **datovou sadu objektů blob Azure**.

Definujete datovou sadu, která představuje data pro kopírování ze zdroje do jímky. Tato datová sada objektů blob v tomto příkladu odkazuje na propojenou službu Azure Storage, kterou jste vytvořili v předchozím kroku: Datová sada přebírá parametr, jehož hodnota je nastavená v aktivitě, která tuto datovou sadu využívá. Tento parametr se používá ke konstrukci folderPath s odkazem na místo uložení dat.

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
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }

        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Vytvoření kanálu

Do metody **Main** přidejte následující kód, který vytvoří **kanál s aktivitou kopírování**.

V tomto příkladu tento kanál obsahuje jednu aktivitu a přebírá dva parametry – vstupní cestu objektů blob a výstupní cestu objektů blob. Hodnoty pro tyto parametry se nastaví při aktivaci nebo spuštění kanálu. Aktivita kopírování odkazuje na stejnou datovou sadu objektů blob, kterou jste vytvořili v předchozím kroku jako vstup a výstup. Když se tato datová sada použije jako vstupní, zadá se vstupní cesta. A když se tato datová sada použije jako výstupní, zadá se výstupní cesta. 

```csharp
// Create a pipeline with a copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Vytvoření spuštění kanálu

Do metody **Main** přidejte následující kód, který **aktivuje spuštění kanálu**.

Tento kód také jako hodnoty parametrů **inputPath** a **outputPath** zadaných v kanálu nastaví skutečné cesty k objektům blob zdroje a jímky.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Monitorování spuštění kanálu

1. Do metody **Main** přidejte následující kód, který nepřetržitě kontroluje stav, dokud se kopírování dat nedokončí.

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
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Spuštění kódu

Sestavte a spusťte aplikaci a potom ověřte spuštění kanálu.

Konzola vytiskne průběh vytváření datové továrny, propojených služeb, datových sad, kanálu a spuštění kanálu. Potom zkontroluje stav spuštění kanálu. Počkejte, dokud aktivita kopírování nezobrazí údaje o velikosti načtených/zapsaných dat. Potom použijte nástroj, jako je [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/), a zkontrolujte, že se objekty blob zkopírovaly z inputBlobPath do outputBlobPath, jak jste zadali v proměnných.

### <a name="sample-output"></a>Ukázkový výstup: 
```json
Creating data factory SPv2Factory0907...
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
        "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
        "type": "SecureString"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@{dataset().path}",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "AzureStorageLinkedService",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "path": {
        "type": "String"
      }
    }
  }
}
Creating pipeline Adfv2QuickStartPipeline...
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
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.inputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.outputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "name": "CopyFromBlobToBlob"
      }
    ],
    "parameters": {
      "inputPath": {
        "type": "String"
      },
      "outputPath": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 308d222d-3858-48b1-9e66-acd921feaa09
Checking pipeline run status...
Status: InProgress
Status: InProgress
Checking copy activity run details...
{
    "dataRead": 331452208,
    "dataWritten": 331452208,
    "copyDuration": 23,
    "throughput": 14073.209,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedCloudDataMovementUnits": 2,
    "billedDuration": 23
}

Press any key to exit...
```

## <a name="verify-the-output"></a>Ověření výstupu
Kanál v kontejneru objektů blob adftutorial automaticky vytvoří výstupní složku. Potom do výstupní složky zkopíruje soubor emp.txt ze vstupní složky. 

1. Na webu Azure Portal na stránce kontejneru **adftutorial** klikněte na **Obnovit**. Zobrazí se výstupní složka. 
    
    ![Obnovení](media/quickstart-create-data-factory-dot-net/output-refresh.png)
2. V seznamu složek klikněte na **output**. 
2. Potvrďte, že je do výstupní složky zkopírovaný soubor **emp.txt**. 

    ![Obnovení](media/quickstart-create-data-factory-dot-net/output-file.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete datovou továrnu odstranit prostřednictvím kódu programu, přidejte do programu následující řádky kódu: 

```csharp
            Console.WriteLine("Deleting the data factory");
            client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>Další kroky
Kanál v této ukázce kopíruje data z jednoho umístění do jiného umístění v úložišti objektů blob v Azure. Projděte si [kurzy](tutorial-copy-data-dot-net.md), kde se dozvíte o použití služby Data Factory ve více scénářích. 
