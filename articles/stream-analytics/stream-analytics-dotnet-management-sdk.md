---
title: Správa sady .NET SDK pro Azure Stream Analytics
description: Začínáme se Stream Analytics Management .NET SDK. Zjistěte, jak nastavit a spustit úlohy analýzy. Vytvoří projekt, vstupy, výstupy a transformace.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 53d9345784c16412c643f3b50506bf6abbab93ec
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094898"
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Správa sady .NET SDK: Nastavení a spuštění úlohy analytics pomocí rozhraní API služby Azure Stream Analytics pro .NET
Zjistěte, jak nastavit a spustit úlohy analytics pomocí rozhraní API pro Stream Analytics pro .NET pomocí sady .NET SDK správy. Nastavení projektu, vytvoření vstupní a výstupní zdroje, transformace a spuštění a zastavení úloh. Pro úlohy analýzy můžete Streamovat data z úložiště objektů Blob nebo z centra událostí.

Zobrazit [správu referenční dokumentace pro rozhraní API pro Stream Analytics pro .NET](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Azure Stream Analytics je plně spravovaná služba poskytující zpracování událostí s nízkou latencí, vysoce dostupné, škálovatelné a komplexní přes streamovaných dat v cloudu. Stream Analytics umožňuje zákazníkům nastavit streamovacích úloh pro analýzu datových proudů a umožňuje jejich jednotka analýzy téměř reálném čase.  

> [!NOTE]
> Ukázkový kód v tomto článku jsme aktualizovali verze v2.x Management .NET SDK služby Azure Stream Analytics. Ukázkový kód pomocí verze sady SDK lagecy (1.x) používá, najdete v tématu [použití Management .NET SDK v1.x pro Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk-v1).

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto článku měli tyto položky:

* Instalace sady Visual Studio 2017 nebo 2015.
* Stáhněte a nainstalujte [sady Azure .NET SDK](https://azure.microsoft.com/downloads/).
* Vytvořte skupinu prostředků Azure v rámci vašeho předplatného. Následuje ukázkový skript Azure Powershellu. Informace o Azure Powershellu najdete v tématu [nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview);  

   ```powershell
   # Log in to your Azure account
   Add-AzureAccount
   
   # Select the Azure subscription you want to use to create the resource group
   Select-AzureSubscription -SubscriptionName <subscription name>
   
   # If Stream Analytics has not been registered to the subscription, remove the remark    symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
   #Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
   
   # Create an Azure resource group
   New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
   ```

* Nastavení vstupní zdroj a cíl výstupu úlohy pro připojení k.

## <a name="set-up-a-project"></a>Nastavení projektu
Vytvoření úlohy služby analytics pomocí rozhraní API pro Stream Analytics pro platformu .NET, nejprve nastavte svůj projekt.

1. Vytvořte konzolovou aplikaci Visual Studio C# .NET.
2. V konzole Správce balíčků pomocí následujících příkazů nainstalujte balíčky NuGet. První z nich je Azure Stream Analytics Management .NET SDK. Pro druhou kolekci slouží k ověřování klient služby Azure.

   ```powershell   
   Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
   Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
   ```

3. Přidejte následující **appSettings** část do souboru App.config:
   
   ```powershell
   <appSettings>
       <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
       <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
       <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
       <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```

    Nahraďte hodnoty **SubscriptionId** a **ActiveDirectoryTenantId** s Azure ID předplatného a tenanta. Tyto hodnoty můžete získat spuštěním následující rutiny Azure Powershellu:

   ```powershell
      Get-AzureAccount
   ```

4. V souboru .csproj přidejte následující odkaz:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

5. Přidejte následující **pomocí** příkazy ke zdrojovému souboru (Program.cs) v projektu:
   
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

6. Přidejte metodu helper ověřování:

   ```csharp
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Vytvoření klienta správy Stream Analytics
A **StreamAnalyticsManagementClient** objekt vám umožní spravovat úlohy a úlohy komponenty, například vstup, výstup a transformace.

Přidejte následující kód do začátku **hlavní** metody:

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

**ResourceGroupName** hodnota proměnné by měl být stejný jako název skupiny prostředků vytvořené nebo vydat v požadované kroky.

Automatizace prezentaci aspekty přihlašovacích údajů od vytvoření úlohy, najdete v tématu [ověřování instančního objektu pomocí Azure Resource Manageru](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

Zbývající části tohoto článku se předpokládá, že tento kód je na začátku **hlavní** metody.

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics
Následující kód vytvoří úlohu Stream Analytics ve skupině prostředků, které jste definovali. Do úlohy budou později přidat vstup, výstup a transformace.

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

## <a name="create-a-stream-analytics-input-source"></a>Vytvoření vstupní zdroj Stream Analytics
Následující kód vytvoří Stream Analytics vstupní zdroj s typem vstupní zdroj blob a serializace sdíleného svazku clusteru. Chcete-li vytvořit vstupní zdroj centra událostí, použijte **EventHubStreamInputDataSource** místo **BlobStreamInputDataSource**. Podobně můžete přizpůsobit typ serializace vstupních zdrojů.

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

Vstupní zdroje z úložiště objektů Blob nebo centra událostí jsou vázané na konkrétní úlohy. Použití stejného vstupního zdroje pro různé úlohy, musí volat metodu znovu a zadejte jiný název úlohy.

## <a name="test-a-stream-analytics-input-source"></a>Testovat vstupní zdroj Stream Analytics
**TestConnection** Metoda testuje, jestli úloha Stream Analytics se bude moct připojit ke zdroji vstupních také další aspekty, které jsou specifické pro daný typ vstupního zdroje. Například v objektu blob vstupní zdroj, který jste vytvořili v předchozím kroku, metoda zkontroluje, že název účtu úložiště a pár klíčů slouží k připojení k účtu úložiště také zkontrolujte, jestli existuje zadaný kontejner.

   ```csharp
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>Vytvořit cíl výstupu Stream Analytics
Vytvoření cíl výstupu je velmi podobné jako vytvoření vstupní zdroj Stream Analytics. Jako vstupní zdroje jsou výstup cíle vázané na konkrétní úlohy. Použití stejného cíle výstup pro různé úlohy, musí volat metodu znovu a zadejte jiný název úlohy.

Následující kód vytvoří cíl výstupu (Azure SQL database). Můžete upravit cíl výstupu datový typ nebo typ serializace.

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

## <a name="test-a-stream-analytics-output-target"></a>Testování cíl výstupu Stream Analytics
Cíl výstupu Stream Analytics má také **TestConnection** metoda pro testování připojení.

   ```csharp
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Vytvoření transformace Stream Analytics
Následující kód vytvoří transformaci Stream Analytics s dotazem "vybrat * z zadání" a určuje se přidělit jednu jednotku streamování pro úlohy Stream Analytics. Další informace o úpravě jednotek streamování najdete v tématu [úlohy škálování Azure Stream Analytics](stream-analytics-scale-jobs.md).

   ```csharp
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

Jako vstup a výstup se váže na konkrétní úlohy Stream Analytics, který byl vytvořený v rámci také transformace.

## <a name="start-a-stream-analytics-job"></a>Spustit úlohu Stream Analytics
Po vytvoření úlohy Stream Analytics a jeho nahráním, výstupy a transformace, můžete spustit úlohy voláním **Start** metody.

Následující ukázkový kód spustí úlohu Stream Analytics s časem zahájení vlastního výstupu nastavena na 12. prosince 2012, 12:12:12 UTC:

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
Spuštěná úloha Stream Analytics můžete zastavit pomocí volání **Zastavit** metody.

   ```csharp
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Odstranění úlohy Stream Analytics
**Odstranit** metoda dojde k odstranění úlohy, jakož i základní dílčí prostředky, včetně nahráním, výstupy a transformace úlohy.

   ```csharp
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Získat podporu
Potřebujete další pomoc, vyzkoušejte naše [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup
Když jste se naučili základy používání sady .NET SDK k vytváření a spouštění úloh analytics. Další informace najdete v těchto článcích:

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Sady .NET SDK pro správu Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn889315.aspx).
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
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
