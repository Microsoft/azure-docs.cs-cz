---
title: "Kurz: Vytvoření kanálu s aktivitou kopírování pomocí rozhraní .NET API | Dokumentace Microsoftu"
description: "V tomto kurzu vytvoříte kanál Azure Data Factory s aktivitou kopírování pomocí rozhraní .NET API."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 58fc4007-b46d-4c8e-a279-cb9e479b3e2b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: eba262239c759677e445d8e3f8b8fae72804f176
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>Kurz: Vytvoření kanálu s aktivitou kopírování pomocí rozhraní .NET API
> [!div class="op_single_selector"]
> * [Přehled a požadavky](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Šablona Azure Resource Manageru](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verzi 2 služby Data Factory, který je ve verzi Preview, přečtěte si [kurz aktivity kopírování v dokumentaci verze 2](../quickstart-create-data-factory-dot-net.md). 

V tomto článku se naučíte, jak používat rozhraní [.NET API](https://portal.azure.com), abyste vytvořili datovou továrnu s kanálem, který kopíruje data z úložiště objektů blob v Azure do databáze SQL v Azure. Pokud s Azure Data Factory začínáte, přečtěte si článek [Seznámení se službou Azure Data Factory](data-factory-introduction.md), než s tímto kurzem začnete.   

V tomto kurzu vytvoříte kanál s jednou aktivitou: aktivita kopírování. Aktivita kopírování kopíruje data z podporovaného úložiště dat do podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje a jímky najdete v tématu [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Aktivita používá globálně dostupnou službu, která může kopírovat data mezi různými úložišti dat zabezpečeným, spolehlivým a škálovatelným způsobem. Další informace o aktivitě kopírování najdete v tématu [Aktivity pohybu dat](data-factory-data-movement-activities.md).

Kanál může obsahovat víc než jednu aktivitu. A dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Další informace naleznete, když přejdete na [více aktivit v kanálu](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

> [!NOTE] 
> Úplnou dokumentaci k rozhraní .NET API pro datovou továrnu najdete v [referencích k rozhraní .NET API služby Data Factory](/dotnet/api/index?view=azuremgmtdatafactories-4.12.1).
> 
> Datový kanál v tomto kurzu kopíruje data ze zdrojového úložiště dat do cílového úložiště dat. Kurz předvádějící způsoby transformace dat pomocí Azure Data Factory najdete v tématu popisujícím [kurz vytvoření kanálu, který umožňuje transformovat data pomocí clusteru Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Požadavky
* Projděte si [Přehled a požadavky kurzu](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), kde najdete informace o kurzu, a proveďte **nutné** kroky.
* Visual Studio 2012 nebo 2013 nebo 2015
* Stáhněte sadu [Azure .NET SDK](http://azure.microsoft.com/downloads/) a nainstalujte ji.
* Azure Powershell Podle pokynů v článku [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/install-azurerm-ps) si na počítač nainstalujte prostředí Azure PowerShell. K vytvoření aplikace v Azure Active Directory použijete Azure PowerShell.

### <a name="create-an-application-in-azure-active-directory"></a>Vytvoření aplikace v Azure Active Directory
Vytvořte aplikaci Azure Active Directory, vytvořte pro ni instanční objekt a přiřaďte ho roli **Přispěvatel Data Factory**.

1. Spusťte **PowerShell**.
2. Spusťte následující příkaz a zadejte uživatelské jméno a heslo, které používáte k přihlášení na web Azure Portal.

    ```PowerShell
    Login-AzureRmAccount
    ```
3. Spuštěním následujícího příkazu zobrazíte všechna předplatná pro tento účet.

    ```PowerShell
    Get-AzureRmSubscription
    ```
4. Spuštěním následujícího příkazu vyberte předplatné, se kterým chcete pracovat. Místo **&lt;NameOfAzureSubscription**&gt; zadejte název svého předplatného Azure.

    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
    ```

   > [!IMPORTANT]
   > Poznamenejte si **SubscriptionId** a **TenantId** z výstupu tohoto příkazu.

5. Spuštěním následujícího příkazu v PowerShellu vytvořte skupinu prostředků Azure s názvem **ADFTutorialResourceGroup**.

    ```PowerShell
    New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    Pokud skupina prostředků už existuje, určete, jestli se má aktualizovat (Y), nebo ponechat tak, jak je (N).

    Pokud používáte jinou skupinu prostředků, použijte v postupech v tomto kurzu místo skupiny ADFTutorialResourceGroup název vaší skupiny prostředků.
6. Vytvořte aplikaci Azure Active Directory.

    ```PowerShell
    $azureAdApplication = New-AzureRmADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"
    ```

    Pokud se zobrazí následující chyba, zadejte jinou adresu URL a spusťte příkaz znovu.
    
    ```PowerShell
    Another object with the same value for property identifierUris already exists.
    ```
7. Vytvořte instanční objekt služby AD.

    ```PowerShell
    New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
8. Přidejte instanční objekt k roli **Přispěvatel Data Factory**.

    ```PowerShell
    New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
9. Získejte ID aplikace.

    ```PowerShell
    $azureAdApplication 
    ```
    Poznamenejte si ID aplikace (applicationID) ve výstupu.

Z těchto kroků byste měli mít tyto čtyři hodnoty:

* ID tenanta
* ID předplatného
* ID aplikace
* Heslo (zadané v prvním příkazu)

## <a name="walkthrough"></a>Názorný postup
1. Pomocí sady Visual Studio 2012/2013/2015 vytvořte konzolovou aplikaci C# .NET.
   1. Spusťte **Visual Studio** 2012/2013/2015.
   2. Klikněte na **Soubor**, přejděte na **Nový** a klikněte na **Projekt**.
   3. Rozbalte **Šablony** a vyberte **Visual C#**. V tomto názorném postupu použijete C#, ale mohli byste využít libovolný jazyk .NET.
   4. V seznamu typů projektů napravo vyberte **Konzolová aplikace**.
   5. Jako název zadejte **DataFactoryAPITestApp**.
   6. Jako umístění vyberte **C:\ADFGetStarted**.
   7. Projekt vytvoříte kliknutím na **OK**.
2. Klikněte na **Nástroje**, přejděte na **Správce balíčků NuGet** a klikněte na **Konzola Správce balíčků**.
3. V **Konzole Správce balíčků** postupujte takto:
   1. Spusťte následující příkaz a nainstalujte balíček služby Data Factory: `Install-Package Microsoft.Azure.Management.DataFactories`
   2. Spusťte následující příkaz pro instalaci balíčku Azure Active Directory (v kódu použijete rozhraní API Active Directory): `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
4. Do souboru **App.config** přidejte následující část **appSetttings**. Tyto nastavení používá pomocná metoda: **GetAuthorizationHeader**.

    Nahraďte hodnoty pro **&lt;ID aplikace&gt;**, **&lt;Heslo&gt;**, **&lt;ID předplatného&gt;** a **&lt;ID tenanta&gt;** vlastními hodnotami.

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

5. Do zdrojového souboru (Program.cs) v projektu přidejte následující příkazy **using**.

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
    string resourceGroupName = "ADFTutorialResourceGroup";
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > Hodnotu **resourceGroupName** nahraďte názvem skupiny prostředků Azure.
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

    Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál může obsahovat jednu nebo víc aktivit. Může obsahovat například aktivitu kopírování, která slouží ke kopírování dat ze zdrojového do cílového úložiště dat, a aktivitu HDInsight Hive pro spuštění skriptu Hive, který umožňuje transformovat vstupní data na výstupní data produktu. V tomto kroku začneme vytvořením objektu pro vytváření dat.
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

    V datové továrně vytvoříte propojené služby, abyste svá úložiště dat a výpočetní služby spojili s datovou továrnou. V tomto kurzu nebudete používat žádnou výpočetní službu jako je Azure HDInsight nebo Azure Data Lake Analytics. Budete používat dvě úložiště dat typu Azure Storage (zdroj) a Azure SQL Database (cíl). 

    Vytvoříte tedy dvě propojené služby s názvem AzureStorageLinkedService a AzureSqlLinkedService typu: AzureStorage a AzureSqlDatabase.  

    Služba AzureStorageLinkedService propojí váš účet služby Azure Storage s datovou továrnou. Tento účet úložiště je ten, ve kterém jste v rámci [požadavků](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) vytvořili kontejner a nahráli do něj data.
9. Do metody **Main** přidejte následující kód, který vytvoří **propojenou službu Azure SQL**.

   > [!IMPORTANT]
   > Položky **servername**, **databasename**, **username** a **password** nahraďte názvem serveru SQL Azure, názvem databáze, uživatelským jménem a heslem.

    ```csharp
    // create a linked service for output data store: Azure SQL Database
    Console.WriteLine("Creating Azure SQL Database linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureSqlLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                )
            }
        }
    );
    ```

    Služba AzureSqlLinkedService propojí službu Azure SQL Database s datovou továrnou. Data kopírovaná z úložiště objektů blob se ukládají do této databáze. V této databázi jste v rámci [požadavků](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) vytvořili tabulku emp.
10. Do metody **Main** přidejte následující kód, který vytvoří **vstupní a výstupní datové sady**.

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "InputDataset";
    string Dataset_Destination = "OutputDataset";

    Console.WriteLine("Creating input dataset of type: Azure Blob");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
                Structure = new List<DataElement>()
                {
                    new DataElement() { Name = "FirstName", Type = "String" },
                    new DataElement() { Name = "LastName", Type = "String" }
                },
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

    Console.WriteLine("Creating output dataset of type: Azure SQL");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new DatasetCreateOrUpdateParameters()
        {
            Dataset = new Dataset()
            {
                Name = Dataset_Destination,
                Properties = new DatasetProperties()
                {
                    Structure = new List<DataElement>()
                    {
                        new DataElement() { Name = "FirstName", Type = "String" },
                        new DataElement() { Name = "LastName", Type = "String" }
                    },
                    LinkedServiceName = "AzureSqlLinkedService",
                    TypeProperties = new AzureSqlTableDataset()
                    {
                        TableName = "emp"
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
    
    V předchozím kroku jste vytvořili propojené služby, abyste propojili účet úložiště Azure a Azure SQL Database s datovou továrnou. V tomto kroku nadefinujete dvě datové sady s názvem InputDataset a OutputDataset, které představují vstupní a výstupní data uložená v úložištích dat, na která odkazují služby AzureStorageLinkedService a AzureSqlLinkedService.

    Propojená služba úložiště Azure určuje připojovací řetězec, který služba Data Factory používá za běhu, aby se připojila k vašemu účtu úložiště Azure. A vstupní datová sada objektu blob (InputDataset) určuje kontejner a složku obsahující vstupní data.  

    Podobně také propojená služba Azure SQL Database určuje připojovací řetězec, který služba Data Factory používá za běhu, aby se připojila k vašemu účtu Azure SQL database. A výstupní datová sada tabulky SQL (OutputDataset) určuje tabulku v databázi, do které se kopírují data z úložiště objektů blob.

    V tomto kroku vytvoříte datovou sadu s názvem InputDataset, která odkazuje na soubor blob (emp.txt) v kořenové složce kontejneru objektů blob (adftutorial), který se nachází ve službě Azure Storage reprezentované propojenou službou AzureStorageLinkedService. Pokud neurčíte hodnotu fileName (nebo ji přeskočíte), data ze všech objektů blob ve vstupní složce se zkopírují do cíle. V tomto kurzu určíte hodnotu fileName.    

    V tomto kroku vytvoříte výstupní datovou sadu s názvem **OutputDataset**. Tato datová sada odkazuje na tabulku SQL ve službě Azure SQL Database, kterou reprezentuje **AzureSqlLinkedService**.
11. Do metody **Main** přidejte následující kód, který **vytvoří a aktivuje kanál**. V tomto kroku vytvoříte kanál s **aktivitou kopírování**, která používá **InputDataset** jako vstup a **OutputDataset** jako výstup.

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2017, 5, 11, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = new DateTime(2017, 5, 12, 0, 0, 0, 0, DateTimeKind.Utc);
    string PipelineName = "ADFTutorialPipeline";

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
                            Name = "BlobToAzureSql",
                            Inputs = new List<ActivityInput>()
                            {
                                new ActivityInput() {
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
                    }
                }
            }
        });
    ```

    Je třeba počítat s následujícím:
   
    - V části aktivit je jenom jedna aktivita, jejíž vlastnost **type** je nastavená na **Copy**. Další informace o aktivitě kopírování najdete v tématu [Aktivity pohybu dat](data-factory-data-movement-activities.md). V řešeních služby Data Factory můžete také použít [aktivity transformace dat](data-factory-data-transformation-activities.md).
    - Vstup aktivity je nastavený na **InputDataset** a výstup aktivity je nastavený na **OutputDataset**. 
    - V části **typeProperties** je jako typ zdroje určen **BlobSource** a jako typ jímky **SqlSink**. Úplný seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky najdete v tématu [podporovaných úložištích dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Kliknutím na odkaz v tabulce se dozvíte, jak použít konkrétní podporovaná úložiště dat jako zdroj/jímku.  
   
    Výstupní datové sady v současné době řídí plán. V tomto kurzu je výstupní datová sada nakonfigurovaná tak, aby vytvářela řez jednou za hodinu. Kanál má čas spuštění a čas ukončení nastavený jeden den od sebe, což je 24 hodin. Proto kanál vytvoří 24 řezů výstupní datové sady.
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

13. Do metody **Main** přidejte následující kód pro získání běhových dat pro datový řez.

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
            }
        );

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

14. Do třídy **Program** přidejte následující pomocnou metodu, kterou používá metoda **Main**.

    > [!NOTE] 
    > Když následující kód kopírujete a vkládáte, ujistěte se, že je tento zkopírovaný kód na stejné úrovní jako metoda Main.

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

15. V Průzkumníku řešení rozbalte projekt (DataFactoryAPITestApp), klikněte pravým tlačítkem na **Odkazy** a potom klikněte na **Přidat odkaz**. Zaškrtněte políčko pro sestavení **System.Configuration**. Pak klikněte na **OK**.
16. Sestavte konzolovou aplikaci. Klikněte v nabídce na **Sestavit** a potom klikněte na **Sestavit řešení**.
17. Potvrďte, že kontejner **adftutorial** v Azure Blob Storage obsahuje alespoň jeden soubor. Pokud ne, vytvořte v Poznámkovém bloku soubor **Emp.txt** s následujícím obsahem a nahrajte ho do kontejneru adftutorial.

    ```
    John, Doe
    Jane, Doe
    ```
18. Ukázku spusťte kliknutím na **Ladit** -> **Spustit ladění** v nabídce. Když se zobrazí **Získávání běhových podrobností o datovém řezu**, počkejte několik minut a stiskněte **ENTER**.
19. Pomocí webu Azure Portal ověřte, že je objekt pro vytváření dat **APITutorialFactory** vytvořený s těmito artefakty:
   * Propojená služba: **LinkedService_AzureStorage**
   * Datová sada: **InputDataset** a **OutputDataset**.
   * Kanál: **PipelineBlobSample**
20. Ověřte, že se v tabulce **emp** vytvořily dva záznamy zaměstnanců v zadané databázi Azure SQL.

## <a name="next-steps"></a>Další kroky
Úplnou dokumentaci k rozhraní .NET API pro datovou továrnu najdete v [referencích k rozhraní .NET API služby Data Factory](/dotnet/api/index?view=azuremgmtdatafactories-4.12.1).

V tomto kurzu jste v operaci kopírování použili úložiště objektů blob jako zdrojové úložiště dat a databázi Azure SQL jako cílové úložiště dat. Následující tabulka obsahuje seznam úložišť dat podporovaných jako zdroje a cíle aktivitou kopírování: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Kliknutím na odkaz úložiště dat v tabulce získáte další informace o kopírování dat do nebo z úložiště dat.

