---
title: Programové sledování a správa úloh Azure Stream Analytics
description: Tento článek popisuje, jak programově sledovat úlohy Stream Analytics vytvořené prostřednictvím rest API, Azure SDK nebo PowerShell.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 23c0cc0d0e4a007fdf46021f857b559266f6a193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431670"
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Programově vytvořit monitor úlohy Stream Analytics

Tento článek ukazuje, jak povolit monitorování úlohy Stream Analytics. Úlohy Analýzy datových proudů, které se vytvářejí pomocí rozhraní REST API, Sady Azure SDK nebo prostředí PowerShell, nemají ve výchozím nastavení povolené monitorování. Můžete ručně povolit na portálu Azure tak, že přejdete na stránce monitor úlohy a kliknutím na tlačítko Povolit nebo můžete automatizovat tento proces podle kroků v tomto článku. Data monitorování se zobrazí v oblasti Metriky na portálu Azure pro úlohu Stream Analytics.

## <a name="prerequisites"></a>Požadavky

Než začnete tento proces, musíte mít následující požadavky:

* Visual Studio 2019 nebo 2015
* [Sada Azure .NET SDK](https://azure.microsoft.com/downloads/) stažená a nainstalovaná
* Existující úloha Stream Analytics, která potřebuje mít povoleno monitorování

## <a name="create-a-project"></a>Vytvoření projektu

1. Vytvořte aplikaci konzoly Visual Studio C# .NET.
2. V konzole Správce balíčků spusťte následující příkazy k instalaci balíčků NuGet. První z nich je Azure Stream Analytics Management .NET SDK. Druhá je sada Azure Monitor SDK, která se použije k povolení monitorování. Poslední z nich je klient Azure Active Directory, který se bude používat pro ověřování.
   
   ```powershell
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Přidejte do souboru App.config následující oddíl appSettings.
   
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
   Nahraďte hodnoty *pro SubscriptionId* a *ActiveDirectoryTenantId* s vaším předplatným Azure a ID klienta. Tyto hodnoty můžete získat spuštěním následující rutiny prostředí PowerShell:
   
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

## <a name="create-management-clients"></a>Vytvořit klienty pro správu

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

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Povolení monitorování pro existující úlohu Stream Analytics

Následující kód umožňuje monitorování **pro existující** úlohu Stream Analytics. První část kódu provádí požadavek GET proti službě Stream Analytics k načtení informací o konkrétní úloze Stream Analytics. Používá *ID* vlastnost (načtené z požadavku GET) jako parametr pro Put metoda v druhé polovině kódu, který odešle put požadavek na službu Insights povolit monitorování pro úlohu Stream Analytics.

> [!WARNING]
> Pokud jste dříve povolili monitorování pro jinou úlohu Stream Analytics, a to buď prostřednictvím portálu Azure, nebo programově pomocí níže uvedeného kódu, **doporučujeme zadat stejný název účtu úložiště, který jste použili, když jste dříve povolili monitorování.**
> 
> Účet úložiště je propojený s oblastí, ve které jste vytvořili úlohu Stream Analytics, ne konkrétně se samotnou úlohou.
> 
> Všechny úlohy Stream Analytics (a všechny ostatní prostředky Azure) ve stejné oblasti sdílejí tento účet úložiště pro ukládání dat monitorování. Pokud zadáte jiný účet úložiště, může to způsobit nežádoucí vedlejší účinky při monitorování dalších úloh Stream Analytics nebo jiných prostředků Azure.
> 
> Název účtu úložiště, který `<YOUR STORAGE ACCOUNT NAME>` použijete k nahrazení v následujícím kódu, by měl být účet úložiště, který je ve stejném předplatném jako úloha Stream Analytics, pro kterou povolujete monitorování.
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

Další pomoc našlápneme na fórum [Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další kroky

* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
