---
title: Vytváření datových kanálů pomocí sady Azure .NET SDK
description: Naučte se programově vytvářet, monitorovat a spravovat datové továrny Azure pomocí Data Factory SDK.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: c9dce90b0dc563fb0fa7ecaaa0882167eef444a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89019891"
---
# <a name="create-monitor-and-manage-azure-data-factories-using-azure-data-factory-net-sdk"></a>Vytváření, sledování a Správa datových továrn Azure pomocí sady Azure Data Factory .NET SDK
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi této služby, podívejte se na [kurz o aktivitě kopírování](../quickstart-create-data-factory-dot-net.md). 

## <a name="overview"></a>Přehled
Pomocí sady Data Factory .NET SDK můžete programově vytvářet, monitorovat a spravovat datové továrny Azure. Tento článek obsahuje návod, pomocí kterého můžete vytvořit ukázkovou konzolovou aplikaci .NET, která vytvoří a monitoruje datovou továrnu. 

> [!NOTE]
> Tento článek nepopisuje všechny možnosti rozhraní .NET API služby Data Factory. V tématu [Data Factory rozhraní .NET API Reference](/dotnet/api/index?view=azuremgmtdatafactories-4.12.1) pro komplexní dokumentaci k rozhraní .NET api pro Data Factory. 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Visual Studio 2012 nebo 2013 nebo 2015
* Stáhněte a nainstalujte sadu [Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Azure Powershell Podle pokynů v článku [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/) si na počítač nainstalujte prostředí Azure PowerShell. K vytvoření aplikace v Azure Active Directory použijete Azure PowerShell.

### <a name="create-an-application-in-azure-active-directory"></a>Vytvoření aplikace v Azure Active Directory
Vytvořte aplikaci Azure Active Directory, vytvořte pro ni instanční objekt a přiřaďte ho roli **Přispěvatel Data Factory**.

1. Spusťte **PowerShell**.
2. Spusťte následující příkaz a zadejte uživatelské jméno a heslo, které používáte k přihlášení na web Azure Portal.

    ```powershell
    Connect-AzAccount
    ```
3. Spuštěním následujícího příkazu zobrazíte všechna předplatná pro tento účet.

    ```powershell
    Get-AzSubscription
    ```
4. Spuštěním následujícího příkazu vyberte předplatné, se kterým chcete pracovat. Nahraďte ** &lt; NameOfAzureSubscription** &gt; názvem vašeho předplatného Azure.

    ```powershell
    Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
    ```

   > [!IMPORTANT]
   > Poznamenejte si **SubscriptionId** a **TenantId** z výstupu tohoto příkazu.

5. Spuštěním následujícího příkazu v PowerShellu vytvořte skupinu prostředků Azure s názvem **ADFTutorialResourceGroup**.

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    Pokud skupina prostředků už existuje, určete, jestli se má aktualizovat (Y), nebo ponechat tak, jak je (N).

    Pokud používáte jinou skupinu prostředků, použijte v postupech v tomto kurzu místo skupiny ADFTutorialResourceGroup název vaší skupiny prostředků.
6. Vytvořte aplikaci Azure Active Directory.

    ```powershell
    $azureAdApplication = New-AzADApplication -DisplayName "ADFDotNetWalkthroughApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfdotnetwalkthroughapp.org/example" -Password "Pass@word1"
    ```

    Pokud se zobrazí následující chyba, zadejte jinou adresu URL a spusťte příkaz znovu.
    
    ```powershell
    Another object with the same value for property identifierUris already exists.
    ```
7. Vytvořte instanční objekt služby AD.

    ```powershell
    New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
8. Přidejte instanční objekt k roli **Přispěvatel Data Factory**.

    ```powershell
    New-AzRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
9. Získejte ID aplikace.

    ```powershell
    $azureAdApplication 
    ```
    Poznamenejte si ID aplikace (applicationID) ve výstupu.

Z těchto kroků byste měli mít tyto čtyři hodnoty:

* ID tenanta
* ID předplatného
* ID aplikace
* Heslo (zadané v prvním příkazu)

## <a name="walkthrough"></a>Názorný postup
V tomto návodu vytvoříte datovou továrnu s kanálem, který obsahuje aktivitu kopírování. Aktivita kopírování kopíruje data ze složky ve vašem úložišti objektů BLOB v Azure do jiné složky ve stejném úložišti objektů BLOB. 

Aktivita kopírování provádí přesun dat ve službě Azure Data Factory. Aktivita používá globálně dostupnou službu, která může kopírovat data mezi různými úložišti dat zabezpečeným, spolehlivým a škálovatelným způsobem. Podrobnosti o aktivitě kopírování najdete v článku [Aktivity přesunu dat](data-factory-data-movement-activities.md).

1. Pomocí sady Visual Studio 2012/2013/2015 vytvořte konzolovou aplikaci C# .NET.
   1. Spusťte **Visual Studio** 2012/2013/2015.
   2. Klikněte na **Soubor**, přejděte na **Nový** a klikněte na **Projekt**.
   3. Rozbalte **Šablony** a vyberte **Visual C#**. V tomto názorném postupu použijete C#, ale mohli byste využít libovolný jazyk .NET.
   4. V seznamu typů projektů napravo vyberte **Konzolová aplikace**.
   5. Jako název zadejte **DataFactoryAPITestApp**.
   6. Jako umístění vyberte **C:\ADFGetStarted**.
   7. Kliknutím na tlačítko **OK** vytvořte projekt.
2. Klikněte na **nástroje**, přejděte na **Správce balíčků NuGet**a klikněte na **Konzola správce balíčků**.
3. V **Konzole Správce balíčků** postupujte takto:
   1. Spusťte následující příkaz a nainstalujte balíček služby Data Factory: `Install-Package Microsoft.Azure.Management.DataFactories`
   2. Spusťte následující příkaz pro instalaci balíčku Azure Active Directory (v kódu použijete rozhraní API Active Directory): `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
4. Nahraďte obsah souboru **App.config** v projektu následujícím obsahem: 
    
    ```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

            <add key="ApplicationId" value="your application ID" />
            <add key="Password" value="Password you used while creating the AAD application" />
            <add key="SubscriptionId" value= "Subscription ID" />
            <add key="ActiveDirectoryTenantId" value="Tenant ID" />
        </appSettings>
    </configuration>
    ```
5. V souboru App.Config aktualizujte hodnoty ** &lt; ID &gt; aplikace**, ** &lt; heslo &gt; **, ** &lt; ID &gt; předplatného**a ** &lt; ID &gt; tenanta** vlastními hodnotami.
6. Přidejte následující příkazy **using** do souboru **program.cs** v projektu.

    ```csharp
    using System.Configuration;
    using System.Collections.ObjectModel;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure;
    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;

    ```
6. Do metody **Main** přidejte následující kód, který vytvoří instanci třídy **DataPipelineManagementClient**. Tento objekt použijete k vytvoření objektu pro vytváření dat, propojené služby, vstupních a výstupních datových sad a kanálu. Použijete ho také k monitorování řezů datových sad při spuštění.

    ```csharp
    // create data factory management client

    //IMPORTANT: specify the name of Azure resource group here
    string resourceGroupName = "ADFTutorialResourceGroup";

    //IMPORTANT: the name of the data factory must be globally unique.
    // Therefore, update this value. For example:APITutorialFactory05122017
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > Hodnotu **resourceGroupName** nahraďte názvem skupiny prostředků Azure. Skupinu prostředků můžete vytvořit pomocí rutiny [New-AzureResourceGroup](/powershell/module/az.resources/new-azresourcegroup) .
   >
   > Aktualizujte název datové továrny (dataFactoryName) tak, aby byl jedinečný. Název objektu pro vytváření dat musí být globálně jedinečný. V tématu [Objekty pro vytváření dat – pravidla pojmenování](data-factory-naming-rules.md) najdete pravidla pojmenování artefaktů služby Data Factory.
7. Do metody **Main** přidejte následující kód, který vytvoří **objekt pro vytváření dat**.

    ```csharp
    // create a data factory
    Console.WriteLine("Creating a data factory");
    client.DataFactories.CreateOrUpdate(resourceGroupName,
        new DataFactoryCreateOrUpdateParameters()
        {
            DataFactory = new DataFactory()
            {
                Name = dataFactoryName,
                Location = "westus",
                Properties = new DataFactoryProperties()
            }
        }
    );
    ```
8. Do metody **Main** přidejte následující kód, který vytvoří **propojenou službu Azure Storage**.

   > [!IMPORTANT]
   > Položky **storageaccountname** a **accountkey** nahraďte názvem svého účtu Azure Storage a jeho klíčem.

    ```csharp
    // create a linked service for input data store: Azure Storage
    Console.WriteLine("Creating Azure Storage linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureStorageLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                )
            }
        }
    );
    ```
9. Do metody **Main** přidejte následující kód, který vytvoří **vstupní a výstupní datové sady**.

    **FolderPath** pro vstupní objekt BLOB je nastavená na **adftutorial/** kde **adftutorial** je název kontejneru v úložišti objektů BLOB. Pokud tento kontejner ve službě Azure Blob Storage neexistuje, vytvořte kontejner s tímto názvem: **adftutorial** a nahrajte textový soubor do kontejneru.

    FolderPath pro výstupní objekt BLOB je nastavená na: **adftutorial/apifactoryoutput/{Slice}** , kde se **řez** dynamicky počítá na základě hodnoty **vlastnosti slicestart** (datum a čas zahájení každého řezu).

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "DatasetBlobSource";
    string Dataset_Destination = "DatasetBlobDestination";
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/",
                    FileName = "emp.txt"
                },
                External = true,
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },
    
                Policy = new Policy()
                {
                    Validation = new ValidationPolicy()
                    {
                        MinimumRows = 1
                    }
                }
            }
        }
    });
    
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Destination,
            Properties = new DatasetProperties()
            {
    
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                    PartitionedBy = new Collection<Partition>()
                    {
                        new Partition()
                        {
                            Name = "Slice",
                            Value = new DateTimePartitionValue()
                            {
                                Date = "SliceStart",
                                Format = "yyyyMMdd-HH"
                            }
                        }
                    }
                },
    
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },
            }
        }
    });
    ```
10. Do metody **Main** přidejte následující kód, který **vytvoří a aktivuje kanál**. Tento kanál má aktivitu **CopyActivity**, která jako zdroj používá **BlobSource** a jako jímku používá **BlobSink**.

    Aktivita kopírování provádí přesun dat ve službě Azure Data Factory. Aktivita používá globálně dostupnou službu, která může kopírovat data mezi různými úložišti dat zabezpečeným, spolehlivým a škálovatelným způsobem. Podrobnosti o aktivitě kopírování najdete v článku [Aktivity přesunu dat](data-factory-data-movement-activities.md).

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
    string PipelineName = "PipelineBlobSample";
    
    client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
    new PipelineCreateOrUpdateParameters()
    {
        Pipeline = new Pipeline()
        {
            Name = PipelineName,
            Properties = new PipelineProperties()
            {
                Description = "Demo Pipeline for data transfer between blobs",
    
                // Initial value for pipeline's active period. With this, you won't need to set slice status
                Start = PipelineActivePeriodStartTime,
                End = PipelineActivePeriodEndTime,
    
                Activities = new List<Activity>()
                {
                    new Activity()
                    {
                        Name = "BlobToBlob",
                        Inputs = new List<ActivityInput>()
                        {
                            new ActivityInput()
                {
                                Name = Dataset_Source
                            }
                        },
                        Outputs = new List<ActivityOutput>()
                        {
                            new ActivityOutput()
                            {
                                Name = Dataset_Destination
                            }
                        },
                        TypeProperties = new CopyActivity()
                        {
                            Source = new BlobSource(),
                            Sink = new BlobSink()
                            {
                                WriteBatchSize = 10000,
                                WriteBatchTimeout = TimeSpan.FromMinutes(10)
                            }
                        }
                    }
    
                },
            }
        }
    });
    ```
12. Do metody **Main** přidejte následující kód pro získání stavu datového řezu výstupní datové sady. V této ukázce se očekává jenom jeden řez.

    ```csharp
    // Pulling status within a timeout threshold
    DateTime start = DateTime.Now;
    bool done = false;
    
    while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
    {
        Console.WriteLine("Pulling the slice status");
        // wait before the next status check
        Thread.Sleep(1000 * 12);
    
        var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
            new DataSliceListParameters()
            {
                DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
            });
    
        foreach (DataSlice slice in datalistResponse.DataSlices)
        {
            if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
            {
                Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                done = true;
                break;
            }
            else
            {
                Console.WriteLine("Slice status is: {0}", slice.State);
            }
        }
    }
    ```
13. **(volitelné)** Přidejte následující kód pro získání podrobností o spuštění pro datový řez do metody **Main** .

    ```csharp
    Console.WriteLine("Getting run details of a data slice");
    
    // give it a few minutes for the output slice to be ready
    Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
    Console.ReadKey();
    
    var datasliceRunListResponse = client.DataSliceRuns.List(
        resourceGroupName,
        dataFactoryName,
        Dataset_Destination,
        new DataSliceRunListParameters()
        {
            DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
        });
    
    foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
    {
        Console.WriteLine("Status: \t\t{0}", run.Status);
        Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
        Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
        Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
        Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
        Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
        Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
    }
    
    Console.WriteLine("\nPress any key to exit.");
    Console.ReadKey();
    ```
14. Do třídy **Program** přidejte následující pomocnou metodu, kterou používá metoda **Main**. Tato metoda představuje dialogové okno, ve kterém můžete zadat **uživatelské jméno** a **heslo** , které používáte k přihlášení do Azure Portal.

    ```csharp
    public static async Task<string> GetAuthorizationHeader()
    {
        AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        ClientCredential credential = new ClientCredential(
            ConfigurationManager.AppSettings["ApplicationId"],
            ConfigurationManager.AppSettings["Password"]);
        AuthenticationResult result = await context.AcquireTokenAsync(
            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
            clientCredential: credential);

        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```

15. V Průzkumník řešení rozbalte projekt: **DataFactoryAPITestApp**, klikněte pravým tlačítkem na **odkazy**a pak klikněte na **Přidat odkaz**. Vyberte zaškrtávací políčko pro `System.Configuration` sestavení a klikněte na tlačítko **OK**.
15. Sestavte konzolovou aplikaci. Klikněte v nabídce na **Sestavit** a potom klikněte na **Sestavit řešení**.
16. Potvrďte, že kontejner adftutorial v Azure Blob Storage obsahuje alespoň jeden soubor. Pokud ne, vytvořte v Poznámkovém bloku soubor Emp.txt s následujícím obsahem a nahrajte ho do kontejneru adftutorial.

    ```
    John, Doe
    Jane, Doe
    ```
17. Spusťte ukázku kliknutím na **ladění**  ->  **Spustit ladění** v nabídce. Když se zobrazí **Získávání běhových podrobností o datovém řezu**, počkejte několik minut a stiskněte **ENTER**.
18. Pomocí webu Azure Portal ověřte, že je objekt pro vytváření dat **APITutorialFactory** vytvořený s těmito artefakty:
    * Propojená služba: **AzureStorageLinkedService**
    * Datová sada: **DatasetBlobSource** a **DatasetBlobDestination**.
    * Kanál: **PipelineBlobSample**
19. Ověřte, že se ve složce **apifactoryoutput** v kontejneru **adftutorial** vytvoří výstupní soubor.

## <a name="get-a-list-of-failed-data-slices"></a>Získat seznam neúspěšných datových řezů 

```csharp
// Parse the resource path
var ResourceGroupName = "ADFTutorialResourceGroup";
var DataFactoryName = "DataFactoryAPITestApp";

var parameters = new ActivityWindowsByDataFactoryListParameters(ResourceGroupName, DataFactoryName);
parameters.WindowState = "Failed";
var response = dataFactoryManagementClient.ActivityWindows.List(parameters);
do
{
    foreach (var activityWindow in response.ActivityWindowListResponseValue.ActivityWindows)
    {
        var row = string.Join(
            "\t",
            activityWindow.WindowStart.ToString(),
            activityWindow.WindowEnd.ToString(),
            activityWindow.RunStart.ToString(),
            activityWindow.RunEnd.ToString(),
            activityWindow.DataFactoryName,
            activityWindow.PipelineName,
            activityWindow.ActivityName,
            string.Join(",", activityWindow.OutputDatasets));
        Console.WriteLine(row);
    }

    if (response.NextLink != null)
    {
        response = dataFactoryManagementClient.ActivityWindows.ListNext(response.NextLink, parameters);
    }
    else
    {
        response = null;
    }
}
while (response != null);
```

## <a name="next-steps"></a>Další kroky
Podívejte se na následující příklad pro vytvoření kanálu pomocí sady .NET SDK, který kopíruje data z úložiště objektů BLOB v Azure do Azure SQL Database: 

- [Vytvoření kanálu pro kopírování dat z Blob Storage do SQL Database](data-factory-copy-activity-tutorial-using-dotnet-api.md)
