---
title: Správa .NET SDK pro Azure Stream Analytics
description: Začínáme se sadou Stream Analytics Management .NET SDK. Přečtěte si, jak nastavit a spustit analytické úlohy. Vytvořte projekt, vstupy, výstupy a transformace.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 20be2c56635faa4f77ae8e8e6afc3c1ece6d4942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426262"
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Správa .NET SDK: Nastavení a spuštění analytických úloh pomocí rozhraní Azure Stream Analytics API pro rozhraní .NET
Naučte se nastavit a spustit úlohy analýzy pomocí rozhraní Stream Analytics API pro rozhraní .NET pomocí sady Management .NET SDK. Nastavte projekt, vytvořte vstupní a výstupní zdroje, transformace a zastavte úlohy. Pro vaše analytické úlohy můžete streamovat data z úložiště objektů Blob nebo z centra událostí.

Podívejte se na [referenční dokumentaci pro správu rozhraní API Stream Analytics pro rozhraní .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Azure Stream Analytics je plně spravovaná služba poskytující vysoce dostupné a škálovatelné zpracování událostí s nízkou latencí prostředně streamovaná data v cloudu. Stream Analytics umožňuje zákazníkům nastavit úlohy streamování pro analýzu datových toků a umožňuje jim řídit analýzy v reálném čase.  

> [!NOTE]
> Aktualizovali jsme ukázkový kód v tomto článku pomocí azure streamanalytics management .NET SDK verze 2.x. Ukázkový kód pomocí verze sady SDK používá lagecy (1.x), naleznete [v tématu Použití sady Management .NET SDK v1.x pro stream analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk-v1).

## <a name="prerequisites"></a>Požadavky
Než začnete tento článek, musíte mít následující požadavky:

* Nainstalujte Visual Studio 2019 nebo 2015.
* Stáhněte a nainstalujte sadu [Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Vytvořte skupinu prostředků Azure ve vašem předplatném. Následující příklad je ukázkový skript Azure PowerShell. Informace o Azure PowerShellu najdete v [tématu Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview);  

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
Chcete-li vytvořit úlohu analýzy, použijte rozhraní API Stream Analytics pro rozhraní .NET a nejprve nastavte projekt.

1. Vytvořte aplikaci konzoly Visual Studio C# .NET.
2. V konzole Správce balíčků spusťte následující příkazy k instalaci balíčků NuGet. První z nich je Azure Stream Analytics Management .NET SDK. Druhý je pro ověřování klientů Azure.

   ```powershell   
   Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
   Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
   ```

3. Do souboru App.config přidejte následující oddíl **appSettings:**
   
   ```powershell
   <appSettings>
       <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
       <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
       <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
       <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```

    Nahraďte hodnoty **pro SubscriptionId** a **ActiveDirectoryTenantId** s vaším předplatným Azure a ID klienta. Tyto hodnoty můžete získat spuštěním následující rutiny Prostředí Azure PowerShell:

   ```powershell
      Get-AzureAccount
   ```

4. Do souboru .csproj přidejte následující odkaz:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

5. Přidejte následující **příkazy pomocí** zdrojového souboru (Program.cs) v projektu:
   
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

6. Přidejte pomocnou metodu ověřování:

   ```csharp
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Vytvoření klienta pro správu Stream Analytics
Objekt **StreamAnalyticsManagementClient** umožňuje spravovat úlohu a součásti úlohy, jako je například vstup, výstup a transformace.

Na začátek **metody Main** přidejte následující kód:

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

Hodnota proměnné **resourceGroupName** by měla být stejná jako název skupiny prostředků, kterou jste vytvořili nebo vybrali v nezbytných krocích.

Chcete-li automatizovat aspekt prezentace pověření vytváření úloh, naleznete [ověřování instančního objektu pomocí Správce prostředků Azure](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

Zbývající části tohoto článku předpokládají, že tento kód je na začátku **Main** metody.

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics
Následující kód vytvoří úlohu Stream Analytics v rámci skupiny prostředků, kterou jste definovali. Později přidáte do úlohy vstup, výstup a transformaci.

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

## <a name="create-a-stream-analytics-input-source"></a>Vytvoření vstupního zdroje Stream Analytics
Následující kód vytvoří vstupní zdroj Stream Analytics s typem vstupního zdroje objektu blob a serializací CSV. Chcete-li vytvořit vstupní zdroj centra událostí, použijte místo **BlobStreamInputDataSource**aplikaci **EventHubStreamInputDataSource.** Podobně můžete přizpůsobit typ serializace vstupního zdroje.

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

Vstupní zdroje, ať už z úložiště objektů Blob nebo centra událostí, jsou vázané na konkrétní úlohu. Chcete-li použít stejný vstupní zdroj pro různé úlohy, musíte znovu zavolat metodu a zadat jiný název úlohy.

## <a name="test-a-stream-analytics-input-source"></a>Testování vstupního zdroje Stream Analytics
Metoda **TestConnection** testuje, zda je úloha Stream Analytics schopna připojit se ke vstupnímu zdroji a také k dalším aspektům specifickým pro typ vstupního zdroje. Například ve vstupním zdroji objektu blob, který jste vytvořili v dřívějším kroku, metoda zkontroluje, zda název účtu úložiště a pár klíčů lze použít k připojení k účtu úložiště a také zkontrolujte, zda zadaný kontejner existuje.

   ```csharp
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>Vytvoření výstupního cíle Stream Analytics
Vytvoření výstupního cíle je podobné vytvoření vstupního zdroje Stream Analytics. Stejně jako vstupní zdroje jsou výstupní cíle vázány na konkrétní úlohu. Chcete-li použít stejný výstupní cíl pro různé úlohy, musíte znovu zavolat metodu a zadat jiný název úlohy.

Následující kód vytvoří výstupní cíl (azure SQL databáze). Můžete přizpůsobit datový typ a/nebo typ serializace výstupního cíle.

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

## <a name="test-a-stream-analytics-output-target"></a>Testování výstupního cíle Stream Analytics
Cíl výstupu Stream Analytics má také metodu **TestConnection** pro testování připojení.

   ```csharp
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Vytvoření transformace Stream Analytics
Následující kód vytvoří transformaci Stream Analytics s dotazem "select * from Input" a určuje přidělení jedné jednotky streamování pro úlohu Stream Analytics. Další informace o úpravě jednotek streamování najdete v [tématu Škálování úloh Azure Stream Analytics](stream-analytics-scale-jobs.md).

   ```csharp
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

Stejně jako vstup a výstup je transformace také vázána na konkrétní úlohu Stream Analytics, která byla vytvořena pod.

## <a name="start-a-stream-analytics-job"></a>Spuštění úlohy Stream Analytics
Po vytvoření úlohy Stream Analytics a jeho vstupů, výstupů a transformace můžete úlohu spustit voláním metody **Start.**

Následující ukázkový kód spustí úlohu Stream Analytics s vlastním časem zahájení výstupu nastaveným na 12.

   ```csharp
   // Start a streaming job
   StartStreamingJobParameters startStreamingJobParameters = new StartStreamingJobParameters()
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 12, 12, 12, DateTimeKind.Utc)
   };
   streamAnalyticsManagementClient.StreamingJobs.Start(resourceGroupName, streamingJobName, startStreamingJobParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Zastavení úlohy Stream Analytics
Spuštěnou úlohu Stream Analytics můžete zastavit voláním metody **Stop.**

   ```csharp
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Odstranění úlohy Stream Analytics
Delete **Delete** Metoda odstraní úlohu, jakož i základní dílčí prostředky, včetně vstupů, výstupy a transformace úlohy.

   ```csharp
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Získat podporu
Další pomoc našlápneme na fórum [Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další kroky
Naučili jste se základy použití sady .NET SDK k vytváření a spouštění analytických úloh. Další informace naleznete v následujících článcích:

* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Management .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx).
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: https://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: https://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: https://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
