---
title: Větvení v kanálu Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak řídit tok dat v Azure Data Factory prostřednictvím větvení a řetězení aktivit.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: 3b69556c45709629e73aced374db276844e41120
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39618372"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Větvení a řetězení aktivit v kanálech Data Factory
V tomto kurzu vytvoříte kanál služby Data Factory, který prezentuje některé funkce řízení toku. Tento kanál provádí jednoduché kopírování z kontejneru ve službě Azure Blob Storage do jiného kontejneru ve stejném účtu úložiště. Pokud aktivita kopírování proběhne úspěšně, chcete podrobnosti o úspěšném kopírování (jako je například množství zapsaných dat) poslat v e-mailu informujícím o úspěchu. Pokud aktivita kopírování selže, chcete podrobnosti o neúspěšném kopírování (jako je například chybová zpráva) poslat v e-mailu informujícím o selhání. V rámci tohoto kurzu se dozvíte, jak předávat parametry.

Základní schéma tohoto scénáře: ![Přehled](media/tutorial-control-flow/overview.png)

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření propojené služby Azure Storage
> * Vytvoření datové sady Azure Blob
> * Vytvoření kanálu, který obsahuje aktivitu kopírování a aktivitu webu
> * Odeslání výstupů aktivit následným aktivitám
> * Využití předávání parametrů a systémových proměnných
> * Spuštění kanálu
> * Monitorování spuštění aktivit a kanálu

Tento kurz používá .NET SDK. K interakci s Azure Data Factory můžete použít další mechanismy – podívejte se v obsahu na téma Šablony Rychlý start.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* **Účet služby Azure Storage**. Úložiště objektů blob použijete jako **zdrojové** úložiště dat. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../storage/common/storage-create-storage-account.md#create-a-storage-account), kde najdete kroky pro jeho vytvoření.
* **Azure SQL Database**. Tuto databázi použijete jako úložiště dat **jímky**. Pokud Azure SQL Database nemáte, přečtěte si článek věnovaný [vytvoření databáze Azure SQL](../sql-database/sql-database-get-started-portal.md), kde najdete kroky pro její vytvoření.
* **Visual Studio** 2013, 2015 nebo 2017. Názorný postup v tomto článku využívá Visual Studio 2017.
* **Stáhněte a nainstalujte sadu [Azure .NET SDK](http://azure.microsoft.com/downloads/)**.
* **V Azure Active Directory** vytvořte aplikaci s využitím [těchto pokynů](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Poznamenejte následující hodnoty, které použijete v dalších krocích: **ID aplikace**, **ověřovací klíč** a **ID tenanta**. Podle pokynů ve stejném článku přiřaďte aplikaci roli **Přispěvatel**.

### <a name="create-blob-table"></a>Vytvoření tabulky objektů blob

1. Spusťte Poznámkový blok. Zkopírujte následující text a uložte ho na disk jako soubor **input.txt**.

    ```
    John|Doe
    Jane|Doe
    ```
2. Pomocí nástrojů, jako je [Průzkumník služby Azure Storage](http://storageexplorer.com/), vytvořte kontejner **adfv2branch** a odešlete soubor **input.txt** do tohoto kontejneru.

## <a name="create-visual-studio-project"></a>Vytvoření projektu v sadě Visual Studio

Pomocí sady Visual Studio 2015/2017 vytvořte konzolovou aplikaci C# .NET.

1. Spusťte **Visual Studio**.
2. Klikněte na **Soubor**, přejděte na **Nový** a klikněte na **Projekt**. Vyžaduje se .NET verze 4.5.2 nebo novější.
3. V seznamu typů projektů napravo vyberte **Visual C#** -> **Aplikace konzoly (.NET Framework)**.
4. Jako název zadejte **ADFv2BranchTutorial**.
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

2. Do **třídy Program** přidejte tyto statické proměnné. Zástupné znaky nahraďte vlastními hodnotami. Pokud chcete zobrazit seznam oblastí Azure, ve kterých je služba Data Factory aktuálně dostupná, na následující stránce vyberte oblasti, které vás zajímají, pak rozbalte **Analýza** a vyhledejte **Data Factory:** [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). Úložiště dat (Azure Storage, Azure SQL Database atd.) a výpočetní prostředí (HDInsight atd.) používané datovou továrnou mohou být v jiných oblastech.

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
V souboru Program.cs vytvořte funkci CreateOrUpdateDataFactory:

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



Do metody **Main** přidejte následující kód, který vytvoří **datovou továrnu**. 

```csharp
Factory df = CreateOrUpdateDataFactory(client);
```

## <a name="create-an-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage
V souboru Program.cs vytvořte funkci StorageLinkedServiceDefinition:

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
Do metody **Main** přidejte následující kód, který vytvoří **propojenou službu Azure Storage**. V tématu věnovaném [vlastnostem propojených služeb Azure Blob](connector-azure-blob-storage.md#linked-service-properties) se o podporovaných vlastnostech dozvíte víc.

```csharp
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
```

## <a name="create-datasets"></a>Vytvoření datových sad

V této části vytvoříte dvě datové sady, jednu pro zdroj a druhou pro jímku. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Vytvoření datové sady pro zdrojový objekt blob Azure
Do metody **Main** přidejte následující kód, který vytvoří **datovou sadu objektů blob Azure**. V tématu věnovaném [vlastnostem datových sad objektů blob Azure](connector-azure-blob-storage.md#dataset-properties) se o podporovaných vlastnostech dozvíte víc.

Definujete datovou sadu, která představuje zdroj dat ve službě Azure Blob. Tato datová sada Blob odkazuje na propojenou službu Azure Storage, kterou jste vytvořili v předchozím kroku, a popisuje:

- Umístění objektu blob, ze kterého se má kopírovat: **FolderPath** a **FileName**.
- Všimněte si použití parametrů pro FolderPath. sourceBlobContainer je název parametru a výraz se nahradí hodnotami předanými při spuštění kanálu. Syntaxe pro definování parametrů je `@pipeline().parameters.<parameterName>`

V souboru Program.cs vytvořte funkci SourceBlobDatasetDefinition:

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

### <a name="create-a-dataset-for-sink-azure-blob"></a>Vytvoření datové sady pro Azure Blob jímky

V souboru Program.cs vytvořte funkci SourceBlobDatasetDefinition:

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

Do metody **Main** přidejte následující kód, který vytvoří datovou sadu Azure Blob zdroje i jímky. 

```csharp
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
```

## <a name="create-a-c-class-emailrequest"></a>Vytvoření třídy EmailRequest v C#
V projektu C# vytvořte třídu s názvem **EmailRequest**. Definuje, které vlastnosti kanál posílá v těle požadavku při odesílání e-mailu. V tomto kurzu kanál do e-mailu odešle čtyři vlastnosti:

- **Message**: Text e-mailu. V případě úspěšného kopírování tato vlastnost obsahuje podrobnosti o spuštění (počet zapsaných dat). V případě neúspěšného kopírování tato vlastnost obsahuje podrobnosti o chybě.
- **DataFactoryName**: Název datové továrny.
- **PipelineName**: Název kanálu.
- **Receiver**: Parametr, který se předává. Tato vlastnost určuje příjemce e-mailu.

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
K aktivaci odesílání e-mailů použijete [Logic Apps](../logic-apps/logic-apps-overview.md) pro definování pracovního postupu. Podrobnosti o vytvoření pracovního postupu Logic Apps najdete v tématu věnovaném [postupu vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

### <a name="success-email-workflow"></a>Pracovní postup pro e-maily s informací o úspěchu 
Vytvořte pracovní postup aplikace logiky s názvem `CopySuccessEmail`. Jako trigger tohoto pracovního postupu definujte `When an HTTP request is received` a potom přidejte akci `Office 365 Outlook – Send an email`.

![Pracovní postup pro e-maily s informací o úspěchu](media/tutorial-control-flow/success-email-workflow.png)

Pro trigger požadavku zadejte do `Request Body JSON Schema` následující JSON:

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
To odpovídá třídě **EmailRequest**, kterou jste vytvořili v předcházející části. 

Váš požadavek by měl v návrháři aplikace logiky vypadat takto:

![Návrhář aplikace logiky – požadavek](media/tutorial-control-flow/logic-app-designer-request.png)

Pro akci **Odeslat e-mail** upravte, jak chcete e-mail naformátovat, a využijte přitom vlastnosti předané ve schématu JSON těla požadavku. Zde naleznete příklad:

![Návrhář aplikace logiky – akce odeslání e-mailu](media/tutorial-control-flow/send-email-action.png)

Poznamenejte si adresu URL žádosti HTTP Post pro pracovní postup pro e-maily s informací o neúspěchu:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="fail-email-workflow"></a>Pracovní postup pro e-maily s informací o úspěchu 
Naklonujte **CopySuccessEmail** a vytvořte další pracovní postup Logic Apps s názvem **CopyFailEmail**. Schéma `Request Body JSON schema` v triggeru požadavku je stejné. Stačí změnit formát e-mailu, například změnit `Subject` tak, aby to odpovídalo neúspěchu. Zde naleznete příklad:

![Návrhář aplikace logiky – pracovní postup pro e-maily s informací o neúspěchu](media/tutorial-control-flow/fail-email-workflow.png)

Poznamenejte si adresu URL žádosti HTTP Post pro pracovní postup pro e-maily s informací o neúspěchu:

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

Teď byste měli mít dvě adresy URL pracovního postupu:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```
## <a name="create-a-pipeline"></a>Vytvoření kanálu
Do metody Main přidejte následující kód, který vytvoří kanál s aktivitou kopírování a vlastností dependsOn. V tomto kurzu kanál obsahuje jednu aktivitu: aktivitu kopírování, která jako zdroj používá datovou sadu Blob jako zdroj a jako jímku používá jinou datovou sadu Blob. Na základě úspěchu nebo neúspěchu aktivity kopírování potom volá různé e-mailové úlohy.

V tomto kanálu použijete následující funkce:

- Parametry
- Aktivita webu
- Závislost aktivit
- Použití výstupu aktivity jako vstupu pro další aktivitu

Teď se na jednotlivé části kanálu podívejme podrobněji:

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
Do metody **Main** přidejte následující kód, který vytvoří kanál:

```
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
```
### <a name="parameters"></a>Parametry
První část našeho kanálu definuje parametry. 

- sourceBlobContainer – parametr kanálu využívaný zdrojovou datovou sadou objektů blob
- sinkBlobContainer – parametr kanálu využívaný datovou sadou objektů blob pro jímku
- receiver – tento parametr používají dvě aktivity webu v kanálu, které odesílají e-maily s oznámením o úspěchu nebo selhání příjemci, jehož e-mailová adresa je zadána tímto parametrem.


```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```
### <a name="web-activity"></a>Aktivita webu
Aktivita webu umožňuje volání libovolného koncového bodu REST. Další informace o této aktivitě najdete v tématu věnovaném [aktivitě webu](control-flow-web-activity.md). Tento kanál používá aktivitu webu pro volání pracovního postupu pro e-maily Logic Apps. Vytvořili jste dvě aktivity webu, jednu, která volá pracovní postup **CopySuccessEmail**, a druhou, která volá **CopyFailWorkFlow**.

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
Do vlastnosti Url vložte koncové body adres URL požadavku z pracovního postupu Logic Apps. Ve vlastnosti Body předejte instanci třídy EmailRequest. Obsahuje následující vlastnosti:

- Message – Předání hodnoty `@{activity('CopyBlobtoBlob').output.dataWritten`. Má přístup k vlastnosti předchozí aktivity kopírování a předává hodnotu dataWritten. V případě neúspěchu předejte výstup chyby místo `@{activity('CopyBlobtoBlob').error.message`.
- DataFactoryName – Předání hodnoty `@{pipeline().DataFactory}`. Toto je systémová proměnná, která umožňuje přístup k názvu odpovídající datové továrny. Seznam systémových proměnných najdete v článku [Systémové proměnné](control-flow-system-variables.md).
- PipelineName – Předání hodnoty `@{pipeline().Pipeline}`. Toto je také systémová proměnná, která umožňuje přístup k názvu odpovídajícího kanálu. 
- Receiver – Předání hodnoty \@pipeline().parameters.receiver. Má přístup k parametrům kanálu.
 
Tento kód vytvoří novou závislost aktivit, a to v závislosti na předchozí aktivitě kopírování, která je úspěšná.

## <a name="create-a-pipeline-run"></a>Vytvoření spuštění kanálu
Do metody **Main** přidejte následující kód, který **aktivuje spuštění kanálu**.

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
Finální metoda Main by měla vypadat takto. Sestavte a spusťte program pro aktivaci spuštění kanálu!

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
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Spuštění kódu
Sestavte a spusťte aplikaci a potom ověřte spuštění kanálu.
Konzola vytiskne průběh vytváření datové továrny, propojených služeb, datových sad, kanálu a spuštění kanálu. Potom zkontroluje stav spuštění kanálu. Počkejte, dokud aktivita kopírování nezobrazí údaje o velikosti načtených/zapsaných dat. Potom použijte nástroj, jako je průzkumník služby Azure Storage, a zkontrolujte, že se objekty blob zkopírovaly z inputBlobPath do outputBlobPath, jak jste zadali v proměnných.

**Ukázkový výstup:**

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": {
      "type": "SecureString",
      "value": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
    }
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
V tomto kurzu jste provedli následující kroky: 

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření propojené služby Azure Storage
> * Vytvoření datové sady Azure Blob
> * Vytvoření kanálu, který obsahuje aktivitu kopírování a aktivitu webu
> * Odeslání výstupů aktivit následným aktivitám
> * Využití předávání parametrů a systémových proměnných
> * Spuštění kanálu
> * Monitorování spuštění aktivit a kanálu

Teď můžete přejít k části Koncepty, která obsahuje další informace o Azure Data Factory.
> [!div class="nextstepaction"]
>[Kanály a aktivity](concepts-pipelines-activities.md)
