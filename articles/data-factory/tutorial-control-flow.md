---
title: Větvení v kanálu Azure Data Factory
description: Zjistěte, jak řídit tok dat v Azure Data Factory prostřednictvím větvení a řetězení aktivit.
services: data-factory
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 9/27/2019
ms.openlocfilehash: 7ba921656d0dad059b1d15f443bcefeff03ade50
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75977386"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Větvení a řetězení aktivit v kanálech Data Factory

V tomto kurzu vytvoříte kanál Data Factory, který představuje některé funkce toku ovládacího prvku. Tento kanál zkopíruje z kontejneru v Azure Blob Storage do jiného kontejneru ve stejném účtu úložiště. Pokud je aktivita kopírování úspěšná, kanál odešle podrobnosti o úspěšné operaci kopírování v e-mailu. Tyto informace by mohly zahrnovat množství zapsaných údajů. Pokud se aktivita kopírování nezdaří, odešle podrobnosti o selhání kopírování, jako je například chybová zpráva, v e-mailu. V rámci tohoto kurzu se dozvíte, jak předávat parametry.

Tato grafika poskytuje přehled scénáře:

![Přehled](media/tutorial-control-flow/overview.png)

V tomto kurzu se můžete naučit provést následující úkoly:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření propojené služby Azure Storage
> * Vytvoření datové sady Azure Blob
> * Vytvoření kanálu, který obsahuje aktivitu kopírování a aktivitu webu
> * Odeslání výstupů aktivit následným aktivitám
> * Použití předávání parametrů a systémových proměnných
> * Spuštění kanálu
> * Monitorování spuštění aktivit a kanálu

Tento kurz používá .NET SDK. K interakci s Azure Data Factory můžete použít jiné mechanismy. Rychlé starty datové továrny naleznete v [tématu 5minutové rychlé starty](/azure/data-factory/quickstart-create-data-factory-portal).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

* Účet Azure Storage. Úložiště objektů blob používáte jako zdrojové úložiště dat. Pokud nemáte účet úložiště Azure, přečtěte [si tématu Vytvoření účtu úložiště](../storage/common/storage-account-create.md).
* Průzkumník úložišť Azure. Pokud chcete nainstalovat tento nástroj, přečtěte si [téma Průzkumník úložišť Azure](https://storageexplorer.com/).
* Azure SQL Database. Tuto databázi použijete jako úložiště dat jímky. Pokud nemáte azure sql database, najdete v [tématu vytvoření databáze Azure SQL](../sql-database/sql-database-get-started-portal.md).
* Visual Studio. Tento článek používá Visual Studio 2019.
* Azure .NET SDK. Stáhněte a nainstalujte [sadu Azure .NET SDK](https://azure.microsoft.com/downloads/).

Seznam oblastí Azure, ve kterých je data factory momentálně dostupná, najdete v [tématu Produkty dostupné podle oblastí](https://azure.microsoft.com/global-infrastructure/services/). Úložiště a výpočetní prostředky dat mohou být v jiných oblastech. Mezi obchody patří Azure Storage a Azure SQL Database. Výpočetní prostředky zahrnují HDInsight, který používá Data Factory.

Vytvořte aplikaci, jak je popsáno v [seznamu Vytvoření aplikace Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Přiřaďte aplikaci roli **přispěvatele** podle pokynů ve stejném článku. Budete potřebovat několik hodnot pro novější části tohoto kurzu, jako je **například ID aplikace (klienta)** a **ID adresáře (tenanta).**

### <a name="create-a-blob-table"></a>Vytvoření tabulky objektů blob

1. Otevřete textový editor. Zkopírujte následující text a uložte jej místně jako *input.txt*.

   ```
   Ethel|Berg
   Tamika|Walsh
   ```

1. Otevřete Průzkumníka úložišť Azure. Rozšiřte svůj účet úložiště. Klepněte pravým tlačítkem myši na **kontejnery objektů blob** a vyberte **příkaz Vytvořit kontejner objektů blob**.
1. Pojmenujte novou *větev adfv2 větev* kontejneru a vyberte **Odeslat** a přidejte do kontejneru soubor *input.txt.*

## <a name="create-visual-studio-project"></a>Vytvoření projektu v sadě Visual Studio<a name="create-visual-studio-project"></a>

Vytvoření aplikace konzoly C# .NET:

1. Spusťte Visual Studio a vyberte **Vytvořit nový projekt**.
1. V **okně Vytvořit nový projekt**zvolte Console App **(.NET Framework)** pro C# a vyberte **Další**.
1. Název projektu *ADFv2BranchTutorial*.
1. Vyberte **možnost .NET verze 4.5.2** nebo vyšší a pak vyberte **Vytvořit**.

### <a name="install-nuget-packages"></a>Instalace balíčků NuGet

1. Vyberte **možnost Nástroje,** > vyberte**konzolu Správce balíčků****nástroje** > NuGet .
1. V **konzole Správce balíčků**spusťte následující příkazy pro instalaci balíčků. Podrobnosti najdete v [balíčku nuget microsoft.azure.management.datafactory.](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/)

   ```powershell
   Install-Package Microsoft.Azure.Management.DataFactory
   Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

### <a name="create-a-data-factory-client"></a>Vytvoření klienta datové továrny

1. Otevřete *Program.cs* a přidejte následující příkazy:

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

1. Přidejte tyto statické `Program` proměnné do třídy. Zástupné znaky nahraďte vlastními hodnotami.

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

1. Do metody `Main` přidejte následující kód. Tento kód vytvoří `DataFactoryManagementClient` instanci třídy. Tento objekt pak použijete k vytvoření datové továrny, propojené služby, datových sad a kanálu. Tento objekt můžete také použít ke sledování podrobností spuštění kanálu.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
   ```

### <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Přidejte `CreateOrUpdateDataFactory` do *Program.cs* souboru metodu:

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

1. Přidejte následující řádek `Main` k metodě, která vytváří továrnu dat:

   ```csharp
   Factory df = CreateOrUpdateDataFactory(client);
   ```

## <a name="create-an-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage

1. Přidejte `StorageLinkedServiceDefinition` do *Program.cs* souboru metodu:

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

1. Přidejte k metodě, `Main` která vytváří propojenou službu Azure Storage, následující řádek:

   ```csharp
   client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
   ```

Další informace o podporovaných vlastnostech a podrobnostech naleznete [v tématu Vlastnosti propojené služby](connector-azure-blob-storage.md#linked-service-properties).

## <a name="create-datasets"></a>Vytvoření datových sad

V této části vytvoříte dvě datové sady, jednu pro zdroj a jednu pro jímku.

### <a name="create-a-dataset-for-a-source-azure-blob"></a>Vytvoření datové sady pro zdrojový objekt blob Azure

Přidejte metodu, která vytvoří *datovou sadu objektů blob Azure*. Další informace o podporovaných vlastnostech a podrobnostech najdete v [tématu Vlastnosti datové sady Objektů blob Azure](connector-azure-blob-storage.md#dataset-properties).

Přidejte `SourceBlobDatasetDefinition` do *Program.cs* souboru metodu:

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

Nadefinujete datovou sadu, která představuje zdrojová data v objektu blob Azure. Tato datová sada objektů blob odkazuje na propojenou službu Azure Storage podporovanou v předchozím kroku. Datová sada objektu Blob popisuje umístění objektu blob, ze který chcete kopírovat: *FolderPath* a *FileName*.

Všimněte si použití parametrů pro *FolderPath*. `sourceBlobContainer`je název parametru a výraz je nahrazen hodnotami předanými při spuštění kanálu. Syntaxe pro definování parametrů je `@pipeline().parameters.<parameterName>`

### <a name="create-a-dataset-for-a-sink-azure-blob"></a>Vytvoření datové sady pro objekt blob Azure pro jímky

1. Přidejte `SourceBlobDatasetDefinition` do *Program.cs* souboru metodu:

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

1. Přidejte následující kód `Main` k metodě, která vytvoří zdroj objektu blob Azure a jímač datových sad.

   ```csharp
   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
   ```

## <a name="create-a-c-class-emailrequest"></a>Vytvoření třídy EmailRequest v C#

V projektu Jazyka C# vytvořte třídu s názvem `EmailRequest`. Tato třída definuje, jaké vlastnosti kanál odesílá v požadavku na tělo při odesílání e-mailu. V tomto kurzu kanál do e-mailu odešle čtyři vlastnosti:

* Zprávu. Tělo e-mailu. Pro úspěšnou kopii tato vlastnost obsahuje množství zapsaných dat. Pro neúspěšnou kopii tato vlastnost obsahuje podrobnosti o chybě.
* Název datové továrny. Název datové továrny.
* Název kanálu. Název kanálu.
* Přijímač. Parametr, který prochází. Tato vlastnost určuje příjemce e-mailu.

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

K aktivaci odesílání e-mailů použijete [Logic Apps](../logic-apps/logic-apps-overview.md) pro definování pracovního postupu. Podrobnosti o vytvoření pracovního postupu Logic Apps najdete v [tématu Jak vytvořit aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

### <a name="success-email-workflow"></a>Pracovní postup pro e-maily s informací o úspěchu

Na [portálu Azure](https://portal.azure.com)vytvořte pracovní postup Logic Apps s názvem *CopySuccessEmail*. Definujte aktivační `When an HTTP request is received`událost pracovního postupu jako . Pro trigger požadavku zadejte do `Request Body JSON Schema` následující JSON:

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

Pracovní postup vypadá podobně jako v následujícím příkladu:

![Pracovní postup pro e-maily s informací o úspěchu](media/tutorial-control-flow/success-email-workflow-trigger.png)

Tento obsah JSON zarovná s třídou, kterou `EmailRequest` jste vytvořili v předchozí části.

Přidejte akci `Office 365 Outlook – Send an email`aplikace . Pro **odeslat e-mail** ovou akci, přizpůsobit, jak chcete formátovat e-mail, pomocí vlastností předaných v požadavku **Tělo** JSON schéma. Tady je příklad:

![Návrhář aplikace logiky – akce odeslání e-mailu](media/tutorial-control-flow/customize-send-email-action.png)

Po uložení pracovního postupu zkopírujte a uložte hodnotu **adresy URL HTTP POST** ze spouště.

## <a name="fail-email-workflow"></a>Pracovní postup pro e-maily s informací o úspěchu

Clone **CopySuccessEmail** jako jiný pracovní postup Logic Apps s názvem *CopyFailEmail*. Schéma `Request Body JSON schema` v triggeru požadavku je stejné. Změňte formát e-mailu, například `Subject`, tak, aby to odpovídalo neúspěchu. Zde naleznete příklad:

![Návrhář aplikace logiky – selhání pracovního postupu e-mailu](media/tutorial-control-flow/fail-email-workflow.png)

Po uložení pracovního postupu zkopírujte a uložte hodnotu **adresy URL HTTP POST** ze spouště.

Nyní byste měli mít dvě adresy URL pracovního postupu, například následující příklady:

```csharp
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>Vytvoření kanálu

Vraťte se k projektu v sadě Visual Studio. Nyní přidáme kód, který vytvoří kanál s `DependsOn` aktivitou a vlastností kopírování. V tomto kurzu kanál obsahuje jednu aktivitu, aktivitu kopírování, která bere v datové sadě objektů Blob jako zdroj a další datovou sadu objektů Blob jako jímku. Pokud je aktivita kopírování úspěšná nebo neúspěšná, volá různé e-mailové úkoly.

V tomto kanálu použijete následující funkce:

* Parametry
* Webová aktivita
* Závislost aktivit
* Použití výstupu z aktivity jako vstupu do jiné aktivity

1. Přidejte tuto metodu do projektu. Následující části poskytují podrobněji.

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

1. K metodě, `Main` která vytvoří kanál, přidejte následující řádek:

   ```csharp
   client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
   ```

### <a name="parameters"></a>Parametry

První část našeho kódu kanálu definuje parametry.

* `sourceBlobContainer`. Zdrojová datová sada objektů blob spotřebovává tento parametr v kanálu.
* `sinkBlobContainer`. Datová sada objektu blob jímky spotřebovává tento parametr v kanálu.
* `receiver`. Dvě webové aktivity v kanálu, které odesílají úspěšné nebo neúspěšné e-maily příjemci, používají tento parametr.

```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>Webová aktivita

Aktivita webu umožňuje volání libovolného koncového bodu REST. Další informace o aktivitě najdete [v tématu Webová aktivita v Azure Data Factory](control-flow-web-activity.md). Tento kanál používá webovou aktivitu k volání pracovního postupu e-mailu Logic Apps. Vytvoříte dvě webové aktivity: `CopySuccessEmail` jednu, která `CopyFailWorkFlow`volá pracovní postup a jednu, která volá .

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

Ve `Url` vlastnosti vložte koncové body **adresy URL HTTP POST** z pracovních postupů logic apps. Ve `Body` vlastnosti předavte `EmailRequest` instanci třídy. Obsahuje následující vlastnosti:

* Zprávu. Předá `@{activity('CopyBlobtoBlob').output.dataWritten`hodnotu . Přistupuje k vlastnosti předchozí aktivity kopírování a předá hodnotu `dataWritten`. V případě neúspěchu předejte výstup chyby místo `@{activity('CopyBlobtoBlob').error.message`.
* Název datové továrny. Předává `@{pipeline().DataFactory}` hodnota Tato systémová proměnná umožňuje přístup k odpovídajícímu názvu datové továrny. Seznam systémových proměnných naleznete v tématu [Systémové proměnné](control-flow-system-variables.md).
* Název kanálu. Předá `@{pipeline().Pipeline}`hodnotu . Tato systémová proměnná umožňuje přístup k odpovídajícímu názvu kanálu.
* Přijímač. Předá `"@pipeline().parameters.receiver"`hodnotu . Přistupuje k parametrům kanálu.

Tento kód vytvoří novou závislost aktivity, která závisí na předchozí aktivitě kopírování.

## <a name="create-a-pipeline-run"></a>Vytvoření spuštění kanálu

Přidejte následující kód `Main` k metodě, která aktivuje spuštění kanálu.

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

Vaše `Main` poslední metoda by měla vypadat takto.

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

1. Přidejte následující kód `Main` k metodě, která načítá podrobnosti spuštění aktivity kopírování, například velikost přečtených/zapsaných dat:

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

Aplikace zobrazuje průběh vytváření datové továrny, propojené služby, datových sad, kanálu a spuštění kanálu. Potom zkontroluje stav spuštění kanálu. Počkejte, dokud aktivita kopírování nezobrazí údaje o velikosti načtených/zapsaných dat. Potom použijte nástroje, jako je Například Průzkumník úložiště Azure ke kontrole objektu blob byl zkopírován do *outputBlobPath* z *inputBlobPath,* jak jste zadali v proměnných.

Výstup by se měl podobat následující ukázce:

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

V tomto kurzu jste provedli následující úkoly:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření propojené služby Azure Storage
> * Vytvoření datové sady Azure Blob
> * Vytvoření kanálu, který obsahuje aktivitu kopírování a aktivitu webu
> * Odeslání výstupů aktivit následným aktivitám
> * Použití předávání parametrů a systémových proměnných
> * Spuštění kanálu
> * Monitorování spuštění aktivit a kanálu

Teď můžete pokračovat v části Koncepty další informace o Azure Data Factory.
> [!div class="nextstepaction"]
>[Kanály a aktivity](concepts-pipelines-activities.md)
