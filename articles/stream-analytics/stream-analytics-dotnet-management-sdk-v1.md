---
title: Správa .NET v1.x SDK pro Azure Stream Analytics | Microsoft Docs
description: Začínáme s .NET SDK služby Stream Analytics správy. Zjistěte, jak nastavit a spustit úlohy analytics. Vytvoření projektu, vstupy, výstupy a transformace.
keywords: .NET SDK, analýzy rozhraní API
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: 5e93de87-0c6f-4f4b-be98-08d63f832897
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/06/2017
ms.author: jeanb
ms.openlocfilehash: acda06ae2d0eea4b3352870bd6e1f4410ad05b17
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="management-net-sdk-v1x-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Správa .NET SDK v1.x: nastavit až a spouštění úloh analytics pomocí rozhraní API služby Azure Stream Analytics pro .NET
Zjistěte, jak nastavit a spustit úlohy analytics pomocí rozhraní API služby Stream Analytics pro .NET pomocí .NET SDK služby správy. Nastavení projektu, vytvořte vstupní a výstupní zdrojů, transformace a spuštění a zastavení úloh. Pro úlohy analýzy může Streamovat data z úložiště objektů Blob nebo z centra událostí.

Najdete v článku [správu referenční dokumentaci rozhraní API služby Stream Analytics pro .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Azure Stream Analytics je plně spravovaná služba poskytuje zpracování událostí s nízkou latencí, vysoce dostupná, škálovatelná, komplexní přes streamování dat v cloudu. Stream Analytics umožňuje zákazníkům nastavit úloh streamování k analýze datové proudy a umožňuje, aby jednotka téměř analýzu v reálném čase.  

> [!NOTE]
> Ukázkový kód v tomto článku se pořád používají starší verze (1.x) verzi .NET SDK služby Azure Stream Analytics správy. Ukázku kódu pomocí aktuální verze sady SDK, najdete v tématu [používat sadu .NET SDK správy pro Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk).

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto článku měli tyto položky:

* Nainstalujte Visual Studio 2017 nebo 2015.
* Stáhněte a nainstalujte [Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Vytvořte skupinu prostředků Azure v rámci vašeho předplatného. Toto je ukázkový skript prostředí Azure PowerShell. Prostředí Azure PowerShell informace najdete v tématu [nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview);  

        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group
        Select-AzureSubscription -SubscriptionName <subscription name>

            # If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
            #Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>


* Nastavení služby vstupní zdroj a cíl výstupu používat. Další pokyny najdete v tématu [vstupy přidat](stream-analytics-add-inputs.md) nastavit ukázka vstup a [přidejte výstupy](stream-analytics-add-outputs.md) nastavit ukázkový výstup.

## <a name="set-up-a-project"></a>Nastavení projektu
Chcete-li vytvořit úlohu analytics použít rozhraní API analýzy datového proudu pro platformu .NET, nejprve nastavte projekt.

1. Vytvořte konzolovou aplikaci Visual Studio C# .NET.
2. V konzole Správce balíčků spusťte následující příkazy instalace balíčků NuGet. První z nich je Azure Stream Analytics správu .NET SDK. Druhá je klient Azure Active Directory, který se použije pro ověřování.
   
        Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 1.8.3
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.4
3. Přidejte následující **appSettings** část do souboru App.config:
   
        <appSettings>
          <!--CSM Prod related values-->
          <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
          <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
          <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
          <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
          <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
          <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION" />
          <add key="ActiveDirectoryTenantId" value="YOU TENANT ID" />
        </appSettings>

    Nahraďte hodnoty pro **SubscriptionId** a **ActiveDirectoryTenantId** s Azure ID předplatného a klienta. Tyto hodnoty můžete získat spuštěním následující rutiny prostředí Azure PowerShell:

        Get-AzureAccount

4. Do souboru .csproj přidejte následující odkaz:

        <Reference Include="System.Configuration" />

1. Přidejte následující **pomocí** příkazy ke zdrojovému souboru (Program.cs) v projektu:
   
        using System;
        using System.Configuration;
        using System.Threading.Tasks;
        
        using Microsoft.Azure;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
2. Přidejte Pomocná metoda ověřování:

   ```   
   private static async Task<string> GetAuthorizationHeader()
   {
       var context = new AuthenticationContext(
           ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
           ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

        AuthenticationResult result = await context.AcquireTokenASync(
           resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
           clientId: ConfigurationManager.AppSettings["AsaClientId"],
           redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
           promptBehavior: PromptBehavior.Always);

        if (result != null)
            return result.AccessToken;

       throw new InvalidOperationException("Failed to acquire token");
   }
   ```  

## <a name="create-a-stream-analytics-management-client"></a>Vytvoření klienta správy Stream Analytics
A **StreamAnalyticsManagementClient** objekt umožňuje spravovat úlohy a úlohy součástí, např. vstupní, výstupní a transformace.

Přidejte následující kód do začátku **hlavní** metoda:

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
    string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
    string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
    string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
        ConfigurationManager.AppSettings["SubscriptionId"],
        GetAuthorizationHeader().Result);

    // Create Stream Analytics management client
    StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

**ResourceGroupName** hodnota proměnné by měl být stejný jako název skupiny prostředků vytvořené nebo zachyceny v požadovaných předchozích kroků.

Automatizovat aspekt úlohy vytvoření přihlašovacích údajů prezentace, najdete v tématu [ověřování hlavní název služby pomocí Azure Resource Manageru](../azure-resource-manager/resource-group-authenticate-service-principal.md).

Zbývající části tohoto článku předpokládá, že tento kód je na začátku **hlavní** metoda.

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics
Následující kód vytvoří úlohu služby Stream Analytics v rámci skupiny prostředků, který jste definovali. Do úlohy budou později přidat vstupní, výstupní a transformace.

    // Create a Stream Analytics job
    JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
    {
        Job = new Job()
        {
            Name = streamAnalyticsJobName,
            Location = "<LOCATION>",
            Properties = new JobProperties()
            {
                EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
                Sku = new Sku()
                {
                    Name = "Standard"
                }
            }
        }
    };

    JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## <a name="create-a-stream-analytics-input-source"></a>Vytvoření vstupní zdroj Stream Analytics
Následující kód vytvoří Stream Analytics vstupní zdroj s typ vstupního zdroje blob a serializace sdíleného svazku clusteru. Chcete-li vytvořit vstupní zdroje centra událostí, použijte **EventHubStreamInputDataSource** místo **BlobStreamInputDataSource**. Podobně můžete přizpůsobit serializace typ vstupního zdroje.

    // Create a Stream Analytics input source
    InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
    {
        Input = new Input()
        {
            Name = streamAnalyticsInputName,
            Properties = new StreamInputProperties()
            {
                Serialization = new CsvSerialization
                {
                    Properties = new CsvSerializationProperties
                    {
                        Encoding = "UTF8",
                        FieldDelimiter = ","
                    }
                },
                DataSource = new BlobStreamInputDataSource
                {
                    Properties = new BlobStreamInputDataSourceProperties
                    {
                        StorageAccounts = new StorageAccount[]
                        {
                            new StorageAccount()
                            {
                                AccountName = "<YOUR STORAGE ACCOUNT NAME>",
                                AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
                            }
                        },
                        Container = "samples",
                        PathPattern = ""
                    }
                }
            }
        }
    };

    InputCreateOrUpdateResponse inputCreateResponse =
        client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

Vstupní zdroje, ať už z úložiště objektů Blob nebo centra událostí, jsou svázané s určité úlohy. Pokud chcete použít stejný vstupní zdroj pro různé úlohy, musí volat metodu znovu a zadejte jiný název úlohy.

## <a name="test-a-stream-analytics-input-source"></a>Testování vstupního zdroje Stream Analytics
**TestConnection** Metoda testuje, jestli úloha Stream Analytics se může připojit ke zdroji vstupní stejně jako další aspekty, které jsou specifické pro daný typ vstupního zdroje. Například v objektu blob vstupního zdroje, které jste vytvořili v předchozím kroku, metoda zkontroluje, že název účtu úložiště a pár klíčů slouží k připojení k účtu úložiště a také zkontrolujte, jestli zadaný kontejner existuje.

    // Test input source connection
    DataSourceTestConnectionResponse inputTestResponse =
        client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## <a name="create-a-stream-analytics-output-target"></a>Vytvořit cíl výstupu Stream Analytics
Vytvoření cíl výstupu je velmi podobné vytváření vstupní zdroj Stream Analytics. Jako vstupní zdrojů jsou svázané cíle výstup do konkrétní úlohy. Pokud chcete použít stejný cíl výstupu pro různé úlohy, musí volat metodu znovu a zadejte jiný název úlohy.

Následující kód vytvoří cíl výstupu (Azure SQL database). Můžete upravit cíl výstupní datový typ nebo typ serializace.

    // Create a Stream Analytics output target
    OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
    {
        Output = new Output()
        {
            Name = streamAnalyticsOutputName,
            Properties = new OutputProperties()
            {
                DataSource = new SqlAzureOutputDataSource()
                {
                    Properties = new SqlAzureOutputDataSourceProperties()
                    {
                        Server = "<YOUR DATABASE SERVER NAME>",
                        Database = "<YOUR DATABASE NAME>",
                        User = "<YOUR DATABASE LOGIN>",
                        Password = "<YOUR DATABASE LOGIN PASSWORD>",
                        Table = "<YOUR DATABASE TABLE NAME>"
                    }
                }
            }
        }
    };

    OutputCreateOrUpdateResponse outputCreateResponse =
        client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## <a name="test-a-stream-analytics-output-target"></a>Testování cíl výstupu Stream Analytics
Cíl výstupu Stream Analytics má také **TestConnection** metoda pro testování připojení.

    // Test output target connection
    DataSourceTestConnectionResponse outputTestResponse =
        client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## <a name="create-a-stream-analytics-transformation"></a>Vytvořte transformaci Stream Analytics
Následující kód vytvoří transformaci Stream Analytics s dotaz "vybrat * z vstup" a určuje přidělit jednu jednotku streamování pro úlohu služby Stream Analytics. Další informace o úpravě jednotek streamování, najdete v části [úlohy škálování Azure Stream Analytics](stream-analytics-scale-jobs.md).

    // Create a Stream Analytics transformation
    TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
    {
        Transformation = new Transformation()
        {
            Name = streamAnalyticsTransformationName,
            Properties = new TransformationProperties()
            {
                StreamingUnits = 1,
                Query = "select * from Input"
            }
        }
    };

    var transformationCreateResp =
        client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

Jako vstup a výstup je vázaný na konkrétní úlohy Stream Analytics, který byl vytvořen v rámci také transformace.

## <a name="start-a-stream-analytics-job"></a>Spustit úlohu služby Stream Analytics
Po vytvoření úlohy služby Stream Analytics a jeho input(s), výstupem a transformace, můžete spustit úlohu voláním **spustit** metoda.

Následující ukázkový kód spustí úlohu služby Stream Analytics s časem zahájení vlastní výstup nastavena na 12 prosinec 2012, 12:12:12 UTC:

    // Start a Stream Analytics job
    JobStartParameters jobStartParameters = new JobStartParameters
    {
        OutputStartMode = OutputStartMode.CustomTime,
        OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
    };

    LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);

## <a name="stop-a-stream-analytics-job"></a>Zastavení úlohy Stream Analytics
Spuštěná úloha Stream Analytics můžete zastavit voláním **Zastavit** metoda.

    // Stop a Stream Analytics job
    LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## <a name="delete-a-stream-analytics-job"></a>Odstranit úlohu služby Stream Analytics
**Odstranit** metoda odstraní úlohu, jakož i základní dílčí prostředků, včetně input(s), výstupem a transformaci úlohy.

    // Delete a Stream Analytics job
    LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);

## <a name="get-support"></a>Získat podporu
Pro další pomoc, vyzkoušejte naše [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup
Když jste se naučili základy používání služby pomocí sady .NET SDK k vytváření a spouštění úloh analytics. Další informace najdete v těchto článcích:

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Správa Azure Stream Analytics sady .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx).
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
