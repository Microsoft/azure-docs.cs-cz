---
title: Povolení Snapshot Debugger pro aplikace .NET a .NET Core v Azure Functions | Microsoft Docs
description: Povolení Snapshot Debugger pro aplikace .NET a .NET Core v Azure Functions
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 12/18/2020
ms.openlocfilehash: ac25962cac36a149807b67a44b3b88a4f40c954a
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211936"
---
# <a name="enable-snapshot-debugger-for-net-and-net-core-apps-in-azure-functions"></a>Povolení Snapshot Debugger pro aplikace .NET a .NET Core v Azure Functions

Snapshot Debugger v současné době funguje pro aplikace ASP.NET a ASP.NET Core, které běží na Azure Functions v plánech služby Windows.

Při použití Snapshot Debugger doporučujeme, abyste aplikaci spustili v základní úrovni služby nebo vyšší.

Pro většinu aplikací nemá úroveň Free a Shared Service dostatek paměti nebo místa na disku pro ukládání snímků.

## <a name="prerequisites"></a>Požadavky

* [Povolit Application Insights monitorování v Function App](../../azure-functions/configure-monitoring.md#add-to-an-existing-function-app)

## <a name="enable-snapshot-debugger"></a>Povolit Snapshot Debugger

Pokud používáte jiný typ služby Azure, najdete tady pokyny k povolení Snapshot Debugger na dalších podporovaných platformách:
* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Služby Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines a Virtual Machine Scale Sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Místní virtuální nebo fyzické počítače](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Pokud chcete povolit Snapshot Debugger ve vaší aplikaci Function App, musíte soubor aktualizovat `host.json` tak, že přidáte vlastnost `snapshotConfiguration` definovanou níže a znovu nasadíte funkci.

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

Snapshot Debugger je předem nainstalován jako součást modulu runtime Azure Functions, který je ve výchozím nastavení zakázán.

Vzhledem k tomu, že Snapshot Debugger je součástí modulu runtime Azure Functions, není nutné přidávat další balíčky NuGet ani nastavení aplikace.

Stejně jako reference pro jednoduchou aplikaci Function App (.NET Core) Níže uvádíme, jak bude vypadat `.csproj` , `{Your}Function.cs` a `host.json` po povolení Snapshot Debugger.

CSPROJ projektu

```xml
<Project Sdk="Microsoft.NET.Sdk">
<PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
    <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.31" />
</ItemGroup>
<ItemGroup>
    <None Update="host.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
    <None Update="local.settings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
    </None>
</ItemGroup>
</Project>
```

Function – třída

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace SnapshotCollectorAzureFunction
{
    public static class ExceptionFunction
    {
        [FunctionName("ExceptionFunction")]
        public static Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            throw new NotImplementedException("Dummy");
        }
    }
}
```

Soubor hostitele

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

## <a name="enable-snapshot-debugger-for-other-clouds"></a>Povolit Snapshot Debugger pro ostatní cloudy

V současné době jsou k [disAzure Government](https://docs.microsoft.com/azure/azure-government/compare-azure-government-global-azure#application-insights) a [Azure Čína](https://docs.microsoft.com/azure/china/resources-developer-guide)jedinými oblastmi, které vyžadují úpravy koncových bodů.

Níže je uveden příklad `host.json` aktualizace s koncovým bodem cloudového agenta státní správy USA:
```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true,
        "agentEndpoint": "https://snapshot.monitor.azure.us"
      }
    }
  }
}
```

Níže jsou podporovaná přepsání koncového bodu agenta Snapshot Debugger:

|Vlastnost    | Cloud pro státní správu USA | Čína – Cloud |   
|---------------|---------------------|-------------|
|AgentEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

## <a name="disable-snapshot-debugger"></a>Zakázat Snapshot Debugger

Pokud chcete zakázat Snapshot Debugger ve vaší aplikaci Function App, stačí `host.json` soubor aktualizovat nastavením na `false` vlastnost `snapshotConfiguration.isEnabled` .

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": false
      }
    }
  }
}
```

Doporučujeme, abyste Snapshot Debugger povolili ve všech aplikacích, abyste mohli snadno diagnostikovat výjimky aplikací.

## <a name="next-steps"></a>Další kroky

- Vygenerujte provoz do vaší aplikace, který může aktivovat výjimku. Potom počkejte 10 až 15 minut, než se snímky odešlou do instance Application Insights.
- [Zobrazení snímků](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) v Azure Portal.
- Upravte konfiguraci Snapshot Debugger na základě vašeho případu použití ve vaší aplikaci Function App. Další informace najdete v tématu [Konfigurace snímků v host.jsna](../../azure-functions/functions-host-json.md#applicationinsightssnapshotconfiguration).
- Nápovědu k řešení potíží s Snapshot Debugger najdete v tématu [řešení potíží s Snapshot Debugger](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).