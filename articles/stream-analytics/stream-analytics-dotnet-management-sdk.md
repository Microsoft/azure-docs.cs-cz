---
title: Sada Management .NET SDK pro Azure Stream Analytics
description: Začínáme se sadou Stream Analytics Management .NET SDK Přečtěte si, jak nastavit a spustit analytické úlohy. Vytvoření projektu, vstupů, výstupů a transformací.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/12/2021
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 9adc4c92e3e637b9d3e18249b5de00782a94baab
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232881"
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Management .NET SDK: nastavení a spuštění analytických úloh pomocí rozhraní Azure Stream Analytics API pro .NET
Naučte se, jak nastavit a spustit analytické úlohy pomocí rozhraní Stream Analytics API pro .NET pomocí sady Management .NET SDK. Nastavte projekt, vytvořte vstupní a výstupní zdroje, transformace a spusťte a zastavte úlohy. Pro úlohy analýzy můžete streamovat data z úložiště objektů BLOB nebo z centra událostí.

Přečtěte si [referenční dokumentaci správy pro rozhraní Stream Analytics API pro rozhraní .NET](/previous-versions/azure/dn889315(v=azure.100)).

Azure Stream Analytics je plně spravovaná služba, která poskytuje vysoce dostupná a škálovatelná a složitá zpracování událostí při streamování dat v cloudu s nízkou latencí. Stream Analytics zákazníkům umožňuje nastavovat úlohy streamování pro analýzu datových proudů a umožnit jim tak vyřídit analýzy v reálném čase.  

> [!NOTE]
> Aktualizovali jsme ukázkový kód v tomto článku se sadou Azure Stream Analytics Management .NET SDK V2 verze 2. x. Ukázku kódu pomocí sady SDK používá lagecy (1. x), viz [použití sady Management .NET SDK v1. x pro Stream Analytics]().

## <a name="prerequisites"></a>Požadavky
Než začnete tento článek, musíte mít následující požadavky:

* Nainstalujte Visual Studio 2019 nebo 2015.
* Stáhněte a nainstalujte sadu [Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Vytvořte ve svém předplatném skupinu prostředků Azure. Následující příklad je ukázkový skript Azure PowerShell. Informace o Azure PowerShell najdete v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/);  

   ```powershell
   # Log in to your Azure account
   Add-AzureAccount
   
   # Select the Azure subscription you want to use to create the resource group
   Select-AzureSubscription -SubscriptionName <subscription name>
   
   # If Stream Analytics has not been registered to the subscription, remove the remark    symbol (#) to run the Register-AzProvider cmdlet to register the provider namespace
   #Register-AzProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
   
   # Create an Azure resource group
   New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
   ```

* Nastavte vstupní zdroj a výstupní cíl pro úlohu, ke které se chcete připojit.

## <a name="set-up-a-project"></a>Nastavení projektu
Pokud chcete vytvořit úlohu analýzy, použijte rozhraní API Stream Analytics pro .NET, nejdřív nastavte svůj projekt.

1. Vytvořte konzolovou aplikaci Visual Studio C# .NET.
2. V konzole správce balíčků spusťte následující příkazy a nainstalujte balíčky NuGet. První z nich je sada Azure Stream Analytics Management .NET SDK. Druhá je pro ověřování klientů Azure.

   ```powershell   
   Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
   Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
   ```

3. Do souboru App.config přidejte následující oddíl **appSettings** :
   
   ```powershell
   <appSettings>
       <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
       <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
       <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
       <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```

    Nahraďte hodnoty **SubscriptionId** a **ActiveDirectoryTenantId** vaším předplatným Azure a ID tenantů. Tyto hodnoty můžete získat spuštěním následující rutiny Azure PowerShell:

   ```powershell
      Get-AzureAccount
   ```

4. Do souboru. csproj přidejte následující odkaz:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

5. Do zdrojového souboru (Program.cs) v projektu přidejte následující příkazy **using** :
   
   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Configuration;
   using System.Threading;
   using System.Threading.Tasks;
   
   using Microsoft.Azure.Management.StreamAnalytics;
   using Microsoft.Azure.Management.StreamAnalytics.Models;
   using Microsoft.Rest.Azure.Authentication;
   using Microsoft.Rest;
   ```

6. Přidat pomocnou metodu ověřování:

   ```csharp
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Vytvoření klienta pro správu Stream Analytics
Objekt **StreamAnalyticsManagementClient** umožňuje spravovat úlohy a součásti úloh, jako jsou vstup, výstup a transformace.

Na začátek metody **Main** přidejte následující kód:

   ```csharp
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

Hodnota proměnné **resourceGroupName** by měla být stejná jako název skupiny prostředků, kterou jste vytvořili nebo jste vybrali v krocích splnění požadavků.

Pokud chcete automatizovat aspekty vytváření úlohy přihlašovacích údajů, přečtěte si téma [ověřování instančního objektu s Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

Zbývající části tohoto článku předpokládají, že tento kód je na začátku metody **Main** .

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics
Následující kód vytvoří úlohu Stream Analytics v rámci skupiny prostředků, kterou jste definovali. Později do úlohy přidáte vstup, výstup a transformaci.

   ```csharp
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

## <a name="create-a-stream-analytics-input-source"></a>Vytvoření zdroje vstupu Stream Analytics
Následující kód vytvoří Stream Analytics vstupní zdroj s typem zdroje vstupu a serializací CSV. Pokud chcete vytvořit zdroj vstupu centra událostí, použijte **EventHubStreamInputDataSource** místo **BlobStreamInputDataSource**. Podobně můžete přizpůsobit typ serializace zdroje vstupu.

   ```csharp
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

Vstupní zdroje, ať už z úložiště objektů BLOB nebo z centra událostí, jsou vázané na určitou úlohu. Chcete-li použít stejný zdroj vstupu pro různé úlohy, je nutné zavolat metodu znovu a zadat jiný název úlohy.

## <a name="test-a-stream-analytics-input-source"></a>Test zdroje vstupu Stream Analytics
Metoda **TestConnection** testuje, zda se Stream Analytics úloha může připojit ke zdroji vstupu, i k dalším aspektům, které jsou specifické pro vstupní typ zdroje. Například ve zdrojovém vstupu objektu blob, který jste vytvořili v předchozím kroku, metoda zkontroluje, že se název a pár klíčů účtu úložiště dá použít pro připojení k účtu úložiště, a taky ověří, jestli zadaný kontejner existuje.

   ```csharp
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```
Výsledek volání TestConnection je objekt *ResourceTestResult* , který obsahuje dvě vlastnosti:

- *stav*: může to být jeden z následujících řetězců: ["TestNotAttempted", "TestSucceeded", "TestFailed"]
- *Chyba*: typ ErrorResponse obsahuje následující vlastnosti:
   - *kód*: požadovaná vlastnost typu String. Hodnota je standardní System .NET. HttpStatusCode přijaté při testování.
   - *zpráva*: požadovaná vlastnost typu String představuje chybu. 

## <a name="create-a-stream-analytics-output-target"></a>Vytvoření cíle výstupu Stream Analytics
Vytvoření cíle výstupu je podobné jako vytvoření zdroje vstupu Stream Analytics. Podobně jako vstupní zdroje jsou výstupní cíle vázány na konkrétní úlohu. Chcete-li použít stejný cíl výstupu pro různé úlohy, je nutné zavolat metodu znovu a zadat jiný název úlohy.

Následující kód vytvoří výstupní cíl (Azure SQL Database). Můžete přizpůsobit datový typ nebo typ serializace cíle výstupu.

   ```csharp
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

## <a name="test-a-stream-analytics-output-target"></a>Test cíle Stream Analyticsho výstupu
Cíl výstupního Stream Analytics má také metodu **TestConnection** pro testování připojení.

   ```csharp
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Vytvoření transformace Stream Analytics
Následující kód vytvoří Stream Analytics transformaci s dotazem "SELECT * FROM Input" a určí, že se má přidělit jedna jednotka streamování pro úlohu Stream Analytics. Další informace o úpravách jednotek streamování najdete v tématu [škálování úloh Azure Stream Analytics](stream-analytics-scale-jobs.md).

   ```csharp
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

Stejně jako vstup a výstup, transformace je také svázána s konkrétní Stream Analytics úlohou, kterou byl vytvořen v rámci.

## <a name="start-a-stream-analytics-job"></a>Spuštění úlohy Stream Analytics
Po vytvoření úlohy Stream Analytics a jejích vstupů, výstupů a transformací můžete úlohu spustit voláním metody **Start** .

Následující vzorový kód spustí úlohu Stream Analytics s vlastním počátečním časem výstupu nastaveným na 12. prosince 2012, 12:12:12 UTC:

   ```csharp
   // Start a streaming job
   StartStreamingJobParameters startStreamingJobParameters = new StartStreamingJobParameters()
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 12, 12, 12, DateTimeKind.Utc)
   };
   streamAnalyticsManagementClient.StreamingJobs.Start(resourceGroupName, streamingJobName, startStreamingJobParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Zastavení Stream Analytics úlohy
Spuštěnou úlohu Stream Analytics můžete zastavit voláním metody **stop** .

   ```csharp
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Odstranění Stream Analytics úlohy
Metoda **Delete** Odstraní úlohu i podkladové dílčí prostředky, včetně vstupů, výstupů a transformace úlohy.

   ```csharp
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Získání podpory
Pokud chcete získat další pomoc, vyzkoušejte si naši [stránku Microsoft Q&Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Další kroky
Seznámili jste se se základy používání sady .NET SDK pro vytváření a spouštění analytických úloh. Další informace najdete v těchto článcích:

* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Sada Azure Stream Analytics Management .NET SDK](/previous-versions/azure/dn889315(v=azure.100)).
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](/rest/api/streamanalytics/)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: https://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: ../storage/blobs/storage-quickstart-blobs-dotnet.md

[azure.event.hubs]: https://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: /previous-versions/azure/dn789972(v=azure.100)

[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.forum]: https://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
