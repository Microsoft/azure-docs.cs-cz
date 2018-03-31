---
title: Správa sady .NET SDK pro Azure Stream Analytics | Microsoft Docs
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
ms.openlocfilehash: 269491beb2acb854098f6513007c5390f318f945
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Správa .NET SDK: Nastavení a spuštění úloh analytics pomocí rozhraní API služby Azure Stream Analytics pro .NET
Zjistěte, jak nastavit a spustit úlohy analytics pomocí rozhraní API služby Stream Analytics pro .NET pomocí .NET SDK služby správy. Nastavení projektu, vytvořte vstupní a výstupní zdrojů, transformace a spuštění a zastavení úloh. Pro úlohy analýzy může Streamovat data z úložiště objektů Blob nebo z centra událostí.

Najdete v článku [správu referenční dokumentaci rozhraní API služby Stream Analytics pro .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Azure Stream Analytics je plně spravovaná služba poskytuje zpracování událostí s nízkou latencí, vysoce dostupná, škálovatelná, komplexní přes streamování dat v cloudu. Stream Analytics umožňuje zákazníkům nastavit úloh streamování k analýze datové proudy a umožňuje, aby jednotka téměř analýzu v reálném čase.  

> [!NOTE]
> Aktualizovali jsme ukázkový kód v tomto článku s verzí v2.x .NET SDK služby Azure Stream Analytics správy. Ukázku kódu pomocí verze sady SDK lagecy (1.x) používá, najdete v tématu [použít v1.x Management .NET SDK pro Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk-v1).

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
2. V konzole Správce balíčků spusťte následující příkazy instalace balíčků NuGet. První z nich je Azure Stream Analytics správu .NET SDK. Druhá je pro ověřování klienta Azure.
   
        Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
3. Přidejte následující **appSettings** část do souboru App.config:
   
        <appSettings>
          <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
          <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
          <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
          <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
        </appSettings>

    Nahraďte hodnoty pro **SubscriptionId** a **ActiveDirectoryTenantId** s Azure ID předplatného a klienta. Tyto hodnoty můžete získat spuštěním následující rutiny prostředí Azure PowerShell:

        Get-AzureAccount

4. Do souboru .csproj přidejte následující odkaz:

        <Reference Include="System.Configuration" />

5. Přidejte následující **pomocí** příkazy ke zdrojovému souboru (Program.cs) v projektu:
   
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.Threading;
        using System.Threading.Tasks;
        
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Rest;
6. Přidejte Pomocná metoda ověřování:

   ```
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Vytvoření klienta správy Stream Analytics
A **StreamAnalyticsManagementClient** objekt umožňuje spravovat úlohy a úlohy součástí, např. vstupní, výstupní a transformace.

Přidejte následující kód do začátku **hlavní** metoda:

   ```
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamingJobName = "<YOUR STREAMING JOB NAME>";
    string inputName = "<YOUR JOB INPUT NAME>";
    string transformationName = "<YOUR JOB TRANSFORMATION NAME>";
    string outputName = "<YOUR JOB OUTPUT NAME>";
    
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
    // Get credentials
    ServiceClientCredentials credentials = GetCredentials().Result;
    
    // Create Stream Analytics management client
    StreamAnalyticsManagementClient streamAnalyticsManagementClient = new StreamAnalyticsManagementClient(credentials)
    {
        SubscriptionId = ConfigurationManager.AppSettings["SubscriptionId"]
    };
   ```

**ResourceGroupName** hodnota proměnné by měl být stejný jako název skupiny prostředků vytvořené nebo zachyceny v požadovaných předchozích kroků.

Automatizovat aspekt úlohy vytvoření přihlašovacích údajů prezentace, najdete v tématu [ověřování hlavní název služby pomocí Azure Resource Manageru](../azure-resource-manager/resource-group-authenticate-service-principal.md).

Zbývající části tohoto článku předpokládá, že tento kód je na začátku **hlavní** metoda.

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics
Následující kód vytvoří úlohu služby Stream Analytics v rámci skupiny prostředků, který jste definovali. Do úlohy budou později přidat vstupní, výstupní a transformace.

   ```
   // Create a streaming job
   StreamingJob streamingJob = new StreamingJob()
   {
       Tags = new Dictionary<string, string>()
       {
           { "Origin", ".NET SDK" },
           { "ReasonCreated", "Getting started tutorial" }
       },
       Location = "West US",
       EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Drop,
       EventsOutOfOrderMaxDelayInSeconds = 5,
       EventsLateArrivalMaxDelayInSeconds = 16,
       OutputErrorPolicy = OutputErrorPolicy.Drop,
       DataLocale = "en-US",
       CompatibilityLevel = CompatibilityLevel.OneFullStopZero,
       Sku = new Sku()
       {
           Name = SkuName.Standard
       }
   };
   StreamingJob createStreamingJobResult = streamAnalyticsManagementClient.StreamingJobs.CreateOrReplace(streamingJob, resourceGroupName, streamingJobName);
   ```

## <a name="create-a-stream-analytics-input-source"></a>Vytvoření vstupní zdroj Stream Analytics
Následující kód vytvoří Stream Analytics vstupní zdroj s typ vstupního zdroje blob a serializace sdíleného svazku clusteru. Chcete-li vytvořit vstupní zdroje centra událostí, použijte **EventHubStreamInputDataSource** místo **BlobStreamInputDataSource**. Podobně můžete přizpůsobit serializace typ vstupního zdroje.

   ```
   // Create an input
   StorageAccount storageAccount = new StorageAccount()
   {
       AccountName = "<YOUR STORAGE ACCOUNT NAME>",
       AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
   };
   Input input = new Input()
   {
       Properties = new StreamInputProperties()
       {
           Serialization = new CsvSerialization()
           {
               FieldDelimiter = ",",
               Encoding = Encoding.UTF8
           },
           Datasource = new BlobStreamInputDataSource()
           {
               StorageAccounts = new[] { storageAccount },
               Container = "<YOUR STORAGE BLOB CONTAINER>",
               PathPattern = "{date}/{time}",
               DateFormat = "yyyy/MM/dd",
               TimeFormat = "HH",
               SourcePartitionCount = 16
           }
       }
   };
   Input createInputResult = streamAnalyticsManagementClient.Inputs.CreateOrReplace(input, resourceGroupName, streamingJobName, inputName);
   ```

Vstupní zdroje, ať už z úložiště objektů Blob nebo centra událostí, jsou svázané s určité úlohy. Pokud chcete použít stejný vstupní zdroj pro různé úlohy, musí volat metodu znovu a zadejte jiný název úlohy.

## <a name="test-a-stream-analytics-input-source"></a>Testování vstupního zdroje Stream Analytics
**TestConnection** Metoda testuje, jestli úloha Stream Analytics se může připojit ke zdroji vstupní stejně jako další aspekty, které jsou specifické pro daný typ vstupního zdroje. Například v objektu blob vstupního zdroje, které jste vytvořili v předchozím kroku, metoda zkontroluje, že název účtu úložiště a pár klíčů slouží k připojení k účtu úložiště a také zkontrolujte, jestli zadaný kontejner existuje.

   ```
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>Vytvořit cíl výstupu Stream Analytics
Vytvoření cíl výstupu je velmi podobné vytváření vstupní zdroj Stream Analytics. Jako vstupní zdrojů jsou svázané cíle výstup do konkrétní úlohy. Pokud chcete použít stejný cíl výstupu pro různé úlohy, musí volat metodu znovu a zadejte jiný název úlohy.

Následující kód vytvoří cíl výstupu (Azure SQL database). Můžete upravit cíl výstupní datový typ nebo typ serializace.

   ```
   // Create an output
   Output output = new Output()
   {
       Datasource = new AzureSqlDatabaseOutputDataSource()
       {
           Server = "<YOUR DATABASE SERVER NAME>",
           Database = "<YOUR DATABASE NAME>",
           User = "<YOUR DATABASE LOGIN>",
           Password = "<YOUR DATABASE LOGIN PASSWORD>",
           Table = "<YOUR DATABASE TABLE NAME>"
       }
   };
   Output createOutputResult = streamAnalyticsManagementClient.Outputs.CreateOrReplace(output, resourceGroupName, streamingJobName, outputName);
   ```

## <a name="test-a-stream-analytics-output-target"></a>Testování cíl výstupu Stream Analytics
Cíl výstupu Stream Analytics má také **TestConnection** metoda pro testování připojení.

   ```
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Vytvořte transformaci Stream Analytics
Následující kód vytvoří transformaci Stream Analytics s dotaz "vybrat * z vstup" a určuje přidělit jednu jednotku streamování pro úlohu služby Stream Analytics. Další informace o úpravě jednotek streamování, najdete v části [úlohy škálování Azure Stream Analytics](stream-analytics-scale-jobs.md).

   ```
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

Jako vstup a výstup je vázaný na konkrétní úlohy Stream Analytics, který byl vytvořen v rámci také transformace.

## <a name="start-a-stream-analytics-job"></a>Spustit úlohu služby Stream Analytics
Po vytvoření úlohy služby Stream Analytics a jeho input(s), výstupem a transformace, můžete spustit úlohu voláním **spustit** metoda.

Následující ukázkový kód spustí úlohu služby Stream Analytics s časem zahájení vlastní výstup nastavena na 12 prosinec 2012, 12:12:12 UTC:

   ```
   // Start a streaming job
   StartStreamingJobParameters startStreamingJobParameters = new StartStreamingJobParameters()
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 12, 12, 12, DateTimeKind.Utc)
   };
   streamAnalyticsManagementClient.StreamingJobs.Start(resourceGroupName, streamingJobName, startStreamingJobParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Zastavení úlohy Stream Analytics
Spuštěná úloha Stream Analytics můžete zastavit voláním **Zastavit** metoda.

   ```
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Odstranit úlohu služby Stream Analytics
**Odstranit** metoda odstraní úlohu, jakož i základní dílčí prostředků, včetně input(s), výstupem a transformaci úlohy.

   ```
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

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
