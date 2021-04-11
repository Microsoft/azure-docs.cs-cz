---
title: Větvení v kanálu Azure Data Factory
description: Zjistěte, jak řídit tok dat v Azure Data Factory prostřednictvím větvení a řetězení aktivit.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 9/27/2019
ms.openlocfilehash: 83bc90ca251fddd498dd365f1a4b00689a471475
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785661"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Větvení a řetězení aktivit v kanálech Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto kurzu vytvoříte kanál Data Factory, který prezentuje některé funkce toku ovládacích prvků. Tento kanál kopíruje z kontejneru v Azure Blob Storage do jiného kontejneru ve stejném účtu úložiště. Pokud je aktivita kopírování úspěšná, kanál pošle podrobnosti o úspěšné operaci kopírování v e-mailu. Tyto informace mohou zahrnovat množství zapsaných dat. Pokud aktivita kopírování selže, pošle vám podrobnosti o selhání kopírování, jako je například chybová zpráva, v e-mailu. V rámci tohoto kurzu se dozvíte, jak předávat parametry.

Tento obrázek nabízí přehled scénáře:

![Diagram zobrazuje Azure Blob Storage, což je cíl kopie, která při úspěšném odeslání pošle e-mail s podrobnostmi, nebo při selhání pošle e-mail s podrobnostmi o chybě.](media/tutorial-control-flow/overview.png)

V tomto kurzu se dozvíte, jak provádět následující úlohy:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření propojené služby Azure Storage
> * Vytvoření datové sady Azure Blob
> * Vytvoření kanálu, který obsahuje aktivitu kopírování a aktivitu webu
> * Odeslání výstupů aktivit následným aktivitám
> * Použití předávání parametrů a systémových proměnných
> * Spuštění kanálu
> * Monitorování spuštění aktivit a kanálu

Tento kurz používá .NET SDK. K interakci s Azure Data Factory můžete použít jiné mechanismy. Data Factory rychlých startech najdete v tématu [5 minut rychlých startů](./quickstart-create-data-factory-portal.md).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

* Účet Azure Storage. Úložiště objektů BLOB použijete jako zdrojové úložiště dat. Pokud nemáte účet úložiště Azure, přečtěte si téma [Vytvoření účtu úložiště](../storage/common/storage-account-create.md).
* Průzkumník služby Azure Storage. Pokud chcete tento nástroj nainstalovat, přečtěte si téma [Průzkumník služby Azure Storage](https://storageexplorer.com/).
* Azure SQL Database Tuto databázi použijete jako úložiště dat jímky. Pokud nemáte databázi v Azure SQL Database, přečtěte si téma [Vytvoření databáze v Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md).
* Visual Studio Tento článek používá Visual Studio 2019.
* Sada Azure .NET SDK. Stáhněte a nainstalujte si [sadu Azure .NET SDK](https://azure.microsoft.com/downloads/).

Seznam oblastí Azure, ve kterých je Data Factory aktuálně k dispozici, najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). Úložiště dat a výpočetní prostředky můžou být v jiných oblastech. Mezi obchody patří Azure Storage a Azure SQL Database. K výpočtům patří HDInsight, který Data Factory používá.

Vytvořte aplikaci, jak je popsáno v tématu [Vytvoření aplikace Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Přiřaďte aplikaci roli **Přispěvatel** podle pokynů ve stejném článku. Pro pozdější části tohoto kurzu budete potřebovat několik hodnot, jako je například **ID aplikace (klienta)** a **ID adresáře (tenant)**.

### <a name="create-a-blob-table"></a>Vytvoření tabulky objektů BLOB

1. Otevřete textový editor. Zkopírujte následující text a uložte ho místně jako *input.txt*.

   ```
   Ethel|Berg
   Tamika|Walsh
   ```

1. Otevřete Průzkumník služby Azure Storage. Rozbalte svůj účet úložiště. Pravým tlačítkem klikněte na **Kontejnery objektů blob** a vyberte **Vytvořit kontejner objektů blob**.
1. Pojmenujte nový kontejner *adfv2branch* a vyberte **nahrát** a přidejte soubor *input.txt* do kontejneru.

## <a name="create-visual-studio-project"></a>Vytvoření projektu v sadě Visual Studio<a name="create-visual-studio-project"></a>

Vytvořte konzolovou aplikaci v jazyce C# .NET:

1. Spusťte aplikaci Visual Studio a vyberte možnost **vytvořit nový projekt**.
1. V možnosti **vytvořit nový projekt** zvolte **Konzolová aplikace (.NET Framework)** pro C# a vyberte **Další**.
1. Pojmenujte projekt *ADFv2BranchTutorial*.
1. Vyberte **.NET verze 4.5.2** nebo vyšší a pak vyberte **vytvořit**.

### <a name="install-nuget-packages"></a>Instalace balíčků NuGet

1. Vyberte **Nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**.
1. V **konzole správce balíčků** spusťte následující příkazy pro instalaci balíčků. Podrobnosti najdete v [balíčku NuGet pro Microsoft. Azure. Management. DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/) .

   ```powershell
   Install-Package Microsoft.Azure.Management.DataFactory
   Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

### <a name="create-a-data-factory-client"></a>Vytvoření klienta datové továrny

1. Otevřete *program. cs* a přidejte následující příkazy:

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

1. Přidejte tyto statické proměnné do `Program` třídy. Zástupné znaky nahraďte vlastními hodnotami.

   ```csharp
   // Set variables
   static string tenantID = "<tenant ID>";
   static string applicationId = "<application ID>";
   static string authenticationKey = "<Authentication key for your application>";
   static string subscriptionId = "<Azure subscription ID>";
   static string resourceGroup = "<Azure resource group name>";

   static string region = "East US";
   static string dataFactoryName = "<Data factory name>";

   // Specify the source Azure Blob information
   static string storageAccount = "<Azure Storage account name>";
   static string storageKey = "<Azure Storage account key>";
   // confirm that you have the input.txt file placed in th input folder of the adfv2branch container.
   static string inputBlobPath = "adfv2branch/input";
   static string inputBlobName = "input.txt";
   static string outputBlobPath = "adfv2branch/output";
   static string emailReceiver = "<specify email address of the receiver>";

   static string storageLinkedServiceName = "AzureStorageLinkedService";
   static string blobSourceDatasetName = "SourceStorageDataset";
   static string blobSinkDatasetName = "SinkStorageDataset";
   static string pipelineName = "Adfv2TutorialBranchCopy";

   static string copyBlobActivity = "CopyBlobtoBlob";
   static string sendFailEmailActivity = "SendFailEmailActivity";
   static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
   ```

1. Do metody `Main` přidejte následující kód. Tento kód vytvoří instanci `DataFactoryManagementClient` třídy. Pak tento objekt použijete k vytvoření datové továrny, propojené služby, datových sad a kanálu. Tento objekt můžete také použít k monitorování podrobností o spuštění kanálu.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
   ```

### <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. `CreateOrUpdateDataFactory`Do souboru *program. cs* přidejte metodu:

   ```csharp
   static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating data factory " + dataFactoryName + "...");
       Factory resource = new Factory
       {
           Location = region
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

       Factory response;
       {
           response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
       }

       while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
       {
           System.Threading.Thread.Sleep(1000);
       }
       return response;
   }
   ```

1. Přidejte následující řádek do `Main` metody, která vytvoří datovou továrnu:

   ```csharp
   Factory df = CreateOrUpdateDataFactory(client);
   ```

## <a name="create-an-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage

1. `StorageLinkedServiceDefinition`Do souboru *program. cs* přidejte metodu:

   ```csharp
   static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
   {
      Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
      AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
      {
          ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
      };
      Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
      LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
      return linkedService;
   }
   ```

1. Do `Main` metody, která vytvoří propojenou službu Azure Storage, přidejte následující řádek:

   ```csharp
   client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
   ```

Další informace o podporovaných vlastnostech a podrobnostech najdete v tématu věnovaném [vlastnostem propojených služeb](connector-azure-blob-storage.md#linked-service-properties).

## <a name="create-datasets"></a>Vytvoření datových sad

V této části vytvoříte dvě datové sady, jednu pro zdroj a jednu pro jímku.

### <a name="create-a-dataset-for-a-source-azure-blob"></a>Vytvoření datové sady pro zdrojový objekt blob Azure

Přidejte metodu, která vytvoří *datovou sadu objektů BLOB v Azure*. Další informace o podporovaných vlastnostech a podrobnostech najdete v tématu [Vlastnosti datové sady objektů BLOB v Azure](connector-azure-blob-storage.md#dataset-properties).

`SourceBlobDatasetDefinition`Do souboru *program. cs* přidejte metodu:

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    {
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

Nadefinujete datovou sadu, která představuje zdrojová data v objektu blob Azure. Tato datová sada objektů BLOB odkazuje na propojenou službu Azure Storage, která je v předchozím kroku podporovaná. Datová sada objektů BLOB popisuje umístění objektu blob, ze kterého se mají kopírovat: *FolderPath* a *filename*.

Všimněte si použití parametrů pro *FolderPath*. `sourceBlobContainer` je název parametru a výraz je nahrazen hodnotami předanými při spuštění kanálu. Syntaxe pro definování parametrů je `@pipeline().parameters.<parameterName>`

### <a name="create-a-dataset-for-a-sink-azure-blob"></a>Vytvoření datové sady pro objekt blob Azure jímky

1. `SourceBlobDatasetDefinition`Do souboru *program. cs* přidejte metodu:

   ```csharp
   static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
       AzureBlobDataset blobDataset = new AzureBlobDataset
       {
           FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
           LinkedServiceName = new LinkedServiceReference
           {
               ReferenceName = storageLinkedServiceName
           }
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
       DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
       return dataset;
   }
   ```

1. Do metody přidejte následující kód `Main` , který vytvoří datovou sadu Azure Blob source i jímky.

   ```csharp
   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
   ```

## <a name="create-a-c-class-emailrequest"></a>Vytvoření třídy EmailRequest v C#

V projektu C# vytvořte třídu s názvem `EmailRequest` . Tato třída definuje, jaké vlastnosti kanál posílá v žádosti o tělo při odesílání e-mailů. V tomto kurzu kanál do e-mailu odešle čtyři vlastnosti:

* Zpráva. Text e-mailu Pro úspěšnou kopii Tato vlastnost obsahuje množství zapsaných dat. Pro neúspěšnou kopii Tato vlastnost obsahuje podrobnosti o chybě.
* Název datové továrny. Název datové továrny.
* Název kanálu Název kanálu.
* Pozorování. Parametr, který projde. Tato vlastnost určuje příjemce e-mailu.

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```

## <a name="create-email-workflow-endpoints"></a>Vytvoření koncových bodů pracovního postupu pro e-maily

K aktivaci odesílání e-mailů použijete [Logic Apps](../logic-apps/logic-apps-overview.md) pro definování pracovního postupu. Podrobnosti o vytvoření pracovního postupu Logic Apps najdete v tématu [jak vytvořit aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

### <a name="success-email-workflow"></a>Pracovní postup pro e-maily s informací o úspěchu

V [Azure Portal](https://portal.azure.com)vytvořte pracovní postup Logic Apps s názvem *CopySuccessEmail*. Definujte Trigger pracovního postupu jako `When an HTTP request is received` . Pro trigger požadavku zadejte do `Request Body JSON Schema` následující JSON:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

Váš pracovní postup vypadá podobně jako v následujícím příkladu:

![Pracovní postup pro e-maily s informací o úspěchu](media/tutorial-control-flow/success-email-workflow-trigger.png)

Tento obsah JSON zarovnává `EmailRequest` třídu, kterou jste vytvořili v předchozí části.

Přidat akci `Office 365 Outlook – Send an email` . V případě akce **Odeslat e-mail** upravte způsob, jakým chcete e-maily naformátovat, pomocí vlastností předaných v rámci schématu JSON pro **tělo** požadavku. Tady je příklad:

![Návrhář aplikace logiky – akce Odeslat e-mail](media/tutorial-control-flow/customize-send-email-action.png)

Po uložení pracovního postupu zkopírujte a uložte hodnotu **adresy URL post protokolu HTTP** z aktivační události.

## <a name="fail-email-workflow"></a>Pracovní postup pro e-maily s informací o úspěchu

Naklonujte **CopySuccessEmail** jako jiný pracovní postup Logic Apps s názvem *CopyFailEmail*. Schéma `Request Body JSON schema` v triggeru požadavku je stejné. Změňte formát e-mailu, například `Subject`, tak, aby to odpovídalo neúspěchu. Tady je příklad:

![Návrhář aplikace logiky – pracovní postup selhání e-mailu](media/tutorial-control-flow/fail-email-workflow.png)

Po uložení pracovního postupu zkopírujte a uložte hodnotu **adresy URL post protokolu HTTP** z aktivační události.

Nyní byste měli mít dvě adresy URL pracovního postupu, například následující příklady:

```csharp
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>Vytvoření kanálu

Vraťte se do projektu v aplikaci Visual Studio. Nyní přidáme kód, který vytvoří kanál s aktivitou kopírování a `DependsOn` vlastností. V tomto kurzu kanál obsahuje jednu aktivitu, aktivitu kopírování, která přebírá datovou sadu objektů BLOB jako zdroj a jinou datovou sadu objektů BLOB jako jímku. Pokud je aktivita kopírování úspěšná nebo selže, volá různé e-mailové úkoly.

V tomto kanálu použijete následující funkce:

* Parametry
* Aktivita webu
* Závislost aktivit
* Použití výstupu z aktivity jako vstupu jiné aktivitě

1. Přidejte tuto metodu do projektu. Následující části obsahují další podrobnosti.

    ```csharp
    static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
            {
                Console.WriteLine("Creating pipeline " + pipelineName + "...");
                PipelineResource resource = new PipelineResource
                {
                    Parameters = new Dictionary<string, ParameterSpecification>
                    {
                        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                    },
                    Activities = new List<Activity>
                    {
                        new CopyActivity
                        {
                            Name = copyBlobActivity,
                            Inputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSourceDatasetName
                                }
                            },
                            Outputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSinkDatasetName
                                }
                            },
                            Source = new BlobSource { },
                            Sink = new BlobSink { }
                        },
                        new WebActivity
                        {
                            Name = sendSuccessEmailActivity,
                            Method = WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Succeeded" }
                                }
                            }
                        },
                        new WebActivity
                        {
                            Name = sendFailEmailActivity,
                            Method =WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Failed" }
                                }
                            }
                        }
                    }
                };
                Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
                return resource;
            }
    ```

1. Přidejte následující řádek do `Main` metody, která vytvoří kanál:

   ```csharp
   client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
   ```

### <a name="parameters"></a>Parametry

První část našeho kódu kanálu definuje parametry.

* `sourceBlobContainer`. Zdrojová datová sada objektů BLOB využívá tento parametr v kanálu.
* `sinkBlobContainer`. Datová sada objektu BLOB jímky používá tento parametr v kanálu.
* `receiver`. Tento parametr používají dvě aktivity webu v kanálu, které odesílají e-maily o úspěchu nebo neúspěchu příjemci.

```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>Aktivita webu

Aktivita webu umožňuje volání libovolného koncového bodu REST. Další informace o aktivitě najdete v tématu [Webová aktivita v Azure Data Factory](control-flow-web-activity.md). Tento kanál používá aktivitu webu pro volání pracovního postupu Logic Appsho e-mailu. Vytvoříte dvě webové aktivity: jednu, která volá do `CopySuccessEmail` pracovního postupu a jednu, která volá `CopyFailWorkFlow` .

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```

Do `Url` vlastnosti vložte koncové body **adresy URL http post** z pracovních postupů Logic Apps. Ve `Body` vlastnosti předejte instanci `EmailRequest` třídy. Obsahuje následující vlastnosti:

* Zpráva. Předává hodnotu `@{activity('CopyBlobtoBlob').output.dataWritten` . Přistupuje k vlastnosti předchozí aktivity kopírování a předá hodnotu `dataWritten` . V případě neúspěchu předejte výstup chyby místo `@{activity('CopyBlobtoBlob').error.message`.
* Název Data Factory Hodnota Pass `@{pipeline().DataFactory}` této systémové proměnné vám umožní přístup k odpovídajícímu názvu datové továrny. Seznam systémových proměnných najdete v tématu [systémové proměnné](control-flow-system-variables.md).
* Název kanálu Předává hodnotu `@{pipeline().Pipeline}` . Tato systémová proměnná umožňuje přístup k odpovídajícímu názvu kanálu.
* Pozorování. Předává hodnotu `"@pipeline().parameters.receiver"` . Přistupuje k parametrům kanálu.

Tento kód vytvoří novou závislost aktivity, která závisí na předchozí aktivitě kopírování.

## <a name="create-a-pipeline-run"></a>Vytvoření spuštění kanálu

Do `Main` metody, která aktivuje spuštění kanálu, přidejte následující kód.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Hlavní třída

Poslední `Main` Metoda by měla vypadat takto.

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

Sestavte a spusťte program pro aktivaci spuštění kanálu!

## <a name="monitor-a-pipeline-run"></a>Monitorování spuštění kanálu

1. Do metody `Main` přidejte následující kód:

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

    Tento kód průběžně kontroluje stav spuštění, dokud nedokončí kopírování dat.

1. Přidejte následující kód do `Main` metody, která načte podrobnosti o spuštění aktivity kopírování, například velikost načtených/zapsaných dat:

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList();

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Spuštění kódu

Sestavte a spusťte aplikaci a potom ověřte spuštění kanálu.

Aplikace zobrazuje průběh vytváření datové továrny, propojené služby, datových sad, kanálu a spuštění kanálu. Potom zkontroluje stav spuštění kanálu. Počkejte, dokud aktivita kopírování nezobrazí údaje o velikosti načtených/zapsaných dat. Pak pomocí nástrojů, jako je Průzkumník služby Azure Storage, zkontrolujte, že se objekt BLOB zkopíroval do *outputBlobPath* z *inputBlobPath* , jak jste určili v proměnných.

Výstup by měl vypadat podobně jako v následujícím příkladu:

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
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
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli následující úlohy:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření propojené služby Azure Storage
> * Vytvoření datové sady Azure Blob
> * Vytvoření kanálu, který obsahuje aktivitu kopírování a aktivitu webu
> * Odeslání výstupů aktivit následným aktivitám
> * Použití předávání parametrů a systémových proměnných
> * Spuštění kanálu
> * Monitorování spuštění aktivit a kanálu

Teď můžete pokračovat v části koncepty, kde najdete další informace o Azure Data Factory.
> [!div class="nextstepaction"]
>[Kanály a aktivity](concepts-pipelines-activities.md)