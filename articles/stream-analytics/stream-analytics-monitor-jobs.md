---
title: Programové monitorování a správa úloh Azure Stream Analytics
description: Tento článek popisuje, jak programově monitorovat úlohy Stream Analytics vytvořené prostřednictvím rozhraní REST API, Azure SDK nebo PowerShellu.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/20/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: dda1b6b652870440e7228e6640bb555f4c2e4a63
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015072"
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Programové vytvoření monitorování úloh Stream Analytics

Tento článek ukazuje, jak povolit monitorování Stream Analytics úlohy. Stream Analytics úlohy vytvořené přes rozhraní REST API, Azure SDK nebo PowerShell nemají ve výchozím nastavení povolené monitorování. Můžete ji ručně povolit v Azure Portal tak, že přejdete na stránku monitorování úlohy a kliknete na tlačítko Povolit nebo tento proces můžete automatizovat podle kroků v tomto článku. Data monitorování se zobrazí v oblasti metriky Azure Portal pro vaši úlohu Stream Analytics.

## <a name="prerequisites"></a>Požadavky

Než zahájíte tento proces, musíte splnit následující předpoklady:

* Visual Studio 2019 nebo 2015
* Stažená a nainstalovaná [sada Azure .NET SDK](https://azure.microsoft.com/downloads/)
* Existující úloha Stream Analytics, která musí mít povolené monitorování

## <a name="create-a-project"></a>Vytvoření projektu

1. Vytvořte konzolovou aplikaci Visual Studio C# .NET.
2. V konzole správce balíčků spusťte následující příkazy a nainstalujte balíčky NuGet. První z nich je sada Azure Stream Analytics Management .NET SDK. Druhým je Azure Monitor sada SDK, která se bude používat k povolení monitorování. Poslední je klient Azure Active Directory, který se bude používat pro ověřování.
   
   ```powershell
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Do souboru App.config přidejte následující oddíl appSettings.
   
   ```csharp
   <appSettings>
     <!--CSM Prod related values-->
     <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
     <add key="JobName" value="YOUR JOB NAME" />
     <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
     <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
     <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
     <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
     <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
     <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
     <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
     <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```
   Nahraďte hodnoty *SubscriptionId* a *ActiveDirectoryTenantId* vaším předplatným Azure a ID tenantů. Tyto hodnoty můžete získat spuštěním následující rutiny prostředí PowerShell:
   
   ```powershell
   Get-AzureAccount
   ```
4. Do zdrojového souboru (Program.cs) v projektu přidejte následující příkazy using.
   
   ```csharp
     using System;
     using System.Configuration;
     using System.Threading;
     using Microsoft.Azure;
     using Microsoft.Azure.Management.Insights;
     using Microsoft.Azure.Management.Insights.Models;
     using Microsoft.Azure.Management.StreamAnalytics;
     using Microsoft.Azure.Management.StreamAnalytics.Models;
     using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```
5. Přidejte pomocnou metodu ověřování.

   ```csharp   
   public static string GetAuthorizationHeader()
   {
      AuthenticationResult result = null;
      var thread = new Thread(() =>
      {
         try
         {
             var context = new AuthenticationContext(
                ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
             result = context.AcquireToken(
                 resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                 clientId: ConfigurationManager.AppSettings["AsaClientId"],
                 redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                 promptBehavior: PromptBehavior.Always);
         }
         catch (Exception threadEx)
         {
             Console.WriteLine(threadEx.Message);
         }
     });

     thread.SetApartmentState(ApartmentState.STA);
     thread.Name = "AcquireTokenThread";
     thread.Start();
     thread.Join();
   
     if (result != null)
     {
         return result.AccessToken;
     }
         throw new InvalidOperationException("Failed to acquire token");
   }
   ```

## <a name="create-management-clients"></a>Vytváření klientů pro správu

Následující kód nastaví potřebné proměnné a klienty pro správu.

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);
   ```

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Povolit monitorování pro existující úlohu Stream Analytics

Následující kód umožňuje monitorování pro **existující** úlohu Stream Analytics. První část kódu provádí požadavek GET na službu Stream Analytics, aby načetla informace o konkrétní Stream Analytics úlohy. Používá vlastnost *ID* (získanou z požadavku GET) jako parametr metody Put v druhé polovině kódu, který ODEŠLE požadavek PUT do služby Insights, aby povolil monitorování Stream Analytics úlohy.

> [!WARNING]
> Pokud jste dříve povolili monitorování pro jinou Stream Analytics úlohu, a to buď prostřednictvím Azure Portal nebo programově prostřednictvím níže uvedeného kódu, doporučujeme **zadat stejný název účtu úložiště, který jste použili, když jste předtím povolili monitorování.**
> 
> Účet úložiště je propojený s oblastí, ve které jste vytvořili úlohu Stream Analytics, a ne specificky pro samotnou úlohu.
> 
> Všechny úlohy Stream Analytics (a všechny ostatní prostředky Azure) v této oblasti sdílejí tento účet úložiště pro ukládání dat monitorování. Pokud zadáte jiný účet úložiště, může to způsobit nezamýšlené vedlejší účinky při monitorování dalších Stream Analytics úloh nebo jiných prostředků Azure.
> 
> Název účtu úložiště, který použijete k nahrazení `<YOUR STORAGE ACCOUNT NAME>` v následujícím kódu, by měl být účet úložiště, který se nachází ve stejném předplatném jako Stream Analytics úloha, pro kterou povolujete monitorování.
> 
> 
>    ```csharp
>    // Get an existing Stream Analytics job
>     JobGetParameters jobGetParameters = new JobGetParameters()
>     {
>         PropertiesToExpand = "inputs,transformation,outputs"
>     };
>     JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);
>
>    // Enable monitoring
>    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
>    {
>            Properties = new ServiceDiagnosticSettings()
>           {
>               StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
>           }
>    };
>   insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);
>   ```


## <a name="get-support"></a>Získat podporu

Pokud chcete získat další pomoc, vyzkoušejte si naši [stránku Microsoft Q&Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Další kroky

* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](/rest/api/streamanalytics/)