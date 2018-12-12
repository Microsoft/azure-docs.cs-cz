---
title: Monitorování a Správa úloh Azure Stream Analytics prostřednictvím kódu programu
description: Tento článek popisuje, jak prostřednictvím kódu programu monitorovat úlohy Stream Analytics vytvořené pomocí rozhraní REST API, Azure SDK nebo Powershellu.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: fac56117c4c70e2735580abb52d05e008d660003
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53089409"
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Prostřednictvím kódu programu vytvořit monitorování úlohy Stream Analytics

Tento článek ukazuje, jak povolit monitorování pro úlohu Stream Analytics. Úlohy Stream Analytics, vytvořené pomocí rozhraní REST API, Azure SDK nebo Powershellu není nutné monitorování ve výchozím nastavení povolená. Můžete ručně ji povolit na webu Azure Portal přejděte na stránku úlohy monitorování a kliknutím na tlačítko Povolit nebo tento proces automatizovat pomocí následujících kroků v tomto článku. Data monitorování se zobrazí v oblasti metrik na portálu Azure Portal pro vaši úlohu Stream Analytics.

## <a name="prerequisites"></a>Požadavky

Než zahájíte tento postup, musíte mít následující:

* Visual Studio 2017 nebo 2015
* [Azure .NET SDK](https://azure.microsoft.com/downloads/) stáhnout a nainstalovat
* Existující úlohy Stream Analytics, který musí mít povoleno monitorování

## <a name="create-a-project"></a>Vytvoření projektu

1. Vytvořte konzolovou aplikaci Visual Studio C# .NET.
2. V konzole Správce balíčků pomocí následujících příkazů nainstalujte balíčky NuGet. První z nich je Azure Stream Analytics Management .NET SDK. Druhá je sada Azure SDK monitorování, kterého chcete povolit monitorování. Poslední z nich je klient služby Azure Active Directory, který se použije pro ověřování.
   
   ```powershell
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Do souboru App.config přidejte následující sekci appSettings.
   
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
   Nahraďte hodnoty *SubscriptionId* a *ActiveDirectoryTenantId* s Azure ID předplatného a tenanta. Tyto hodnoty můžete získat spuštěním následující rutiny Powershellu:
   
   ```powershell
   Get-AzureAccount
   ```
4. Přidejte následující příkazy using do zdrojového souboru (Program.cs) v projektu.
   
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
5. Přidáte metodu ověřování pomocné rutiny.

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

## <a name="create-management-clients"></a>Vytvoření klientů pro správu

Následující kód vytvoří nezbytné proměnné a klientů pro správu.

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

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Zapněte sledování pro existující úlohy Stream Analytics

Následující kód umožní monitorování pro **existující** úlohy Stream Analytics. První část kódu provede požadavek GET na službu Stream Analytics se načíst informace o konkrétní úloze Stream Analytics. Používá *Id* vlastnosti (získané z požadavku GET) jako datový typ parametru pro metodu Put ve druhé polovině kód, který odešle PUT žádost o služby Insights povolení monitorování pro úlohu Stream Analytics.

>[!WARNING]
>Pokud jste už dříve povolili monitorování pro různé úlohy Stream Analytics, prostřednictvím webu Azure portal nebo prostřednictvím kódu programu přes níže uvedeného kódu, **doporučujeme vám, že zadáte stejný název účtu úložiště, který jste použili při dřív Povolit monitorování.**
> 
> Účet úložiště je propojen s oblasti, které jste vytvořili vaší úlohy Stream Analytics, ne specificky pro samotnou úlohu.
> 
> Všechny Stream Analytics úlohy (a všechny další prostředky Azure) v dané oblasti stejné sdílet tento účet úložiště k ukládání dat monitorování. Pokud zadáte jiný účet úložiště, může způsobit nežádoucí vedlejší účinky při monitorování jiné úlohy Stream Analytics nebo další prostředky Azure.
> 
> Název účtu úložiště, který můžete použít k nahrazení `<YOUR STORAGE ACCOUNT NAME>` by měla být v následujícím kódu, který je ve stejném předplatném jako úloha Stream Analytics, který chcete povolit monitorování účtu úložiště.
> 
> 
```csharp
    // Get an existing Stream Analytics job
    JobGetParameters jobGetParameters = new JobGetParameters()
    {
        PropertiesToExpand = "inputs,transformation,outputs"
    };
    JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);

    // Enable monitoring
    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
    {
            Properties = new ServiceDiagnosticSettings()
            {
                StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
            }
    };
    insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);
```


## <a name="get-support"></a>Získat podporu

Potřebujete další pomoc, vyzkoušejte naše [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

