---
title: Povolit Snapshot Debugger pro aplikace .NET v Azure App Service | Microsoft Docs
description: Povolit Snapshot Debugger pro aplikace .NET v Azure App Service
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 5a637a6f355be32f82878a52a30c77d020190651
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211613"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Povolit Snapshot Debugger pro aplikace .NET v Azure App Service

Snapshot Debugger aktuálně podporuje aplikace ASP.NET a ASP.NET Core, které běží na Azure App Service v plánech služby Windows.

Při použití ladicího programu Snapshot doporučujeme, abyste aplikaci spustili v úrovni služeb Basic nebo vyšší.

Pro většinu aplikací nemá úroveň Free a Shared Service dostatek paměti nebo místa na disku pro ukládání snímků.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a> Povolit Snapshot Debugger
Pokud chcete povolit Snapshot Debugger pro aplikaci, postupujte podle následujících pokynů.

Pokud používáte jiný typ služby Azure, najdete tady pokyny k povolení Snapshot Debugger na dalších podporovaných platformách:
* [Funkce Azure Functions](snapshot-debugger-function-app.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Služby Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines a Virtual Machine Scale Sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Místní virtuální nebo fyzické počítače](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

> [!NOTE]
> Pokud používáte verzi Preview rozhraní .NET Core nebo pokud vaše aplikace odkazuje na sadu Application Insights SDK přímo nebo nepřímo prostřednictvím závislého sestavení, postupujte podle pokynů pro [povolení Snapshot debugger pro další prostředí](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) , která zahrnují balíček NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) a pak dokončete zbývající kroky níže. 
>
> Bezkódová instalace Application Insights Snapshot Debugger se řídí zásadami podpory .NET Core.
> Další informace o podporovaných modulech runtime najdete v tématu [zásady podpory .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

Snapshot Debugger je předem nainstalován jako součást modulu runtime App Services, ale je nutné ji zapnout, abyste získali snímky pro aplikaci App Service.

Po nasazení aplikace postupujte podle následujících kroků a povolte ladicí program snímků:

1. Pro App Service přejděte na ovládací panel Azure.
2. Přejít na stránku **nastavení > Application Insights** .

   ![Povolení App Insights na portálu App Services](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Podle pokynů na stránce vytvořte nový prostředek nebo vyberte existující prostředek App Insights, abyste mohli svoji aplikaci monitorovat. Také se ujistěte, že jsou **zapnuté** oba přepínače pro Snapshot Debugger.

   ![Přidat rozšíření webu App Insights][Enablement UI]

4. Snapshot Debugger je nyní povoleno pomocí nastavení aplikace App Services.

    ![Nastavení aplikace pro Snapshot Debugger][snapshot-debugger-app-setting]

## <a name="enable-snapshot-debugger-for-other-clouds"></a>Povolit Snapshot Debugger pro ostatní cloudy

V současné době jsou v současnosti k [disAzure Government](https://docs.microsoft.com/azure/azure-government/compare-azure-government-global-azure#application-insights) a [Azure Čína](https://docs.microsoft.com/azure/china/resources-developer-guide) jedinou oblastí, které vyžadují úpravy koncového bodu, pomocí Application Insights připojovacího řetězce.

|Vlastnost připojovacího řetězce    | Cloud pro státní správu USA | Čína – Cloud |   
|---------------|---------------------|-------------|
|SnapshotEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

Další informace o dalších přepsáních připojení najdete v tématu [Application Insights dokumentaci](https://docs.microsoft.com/azure/azure-monitor/app/sdk-connection-string?tabs=net#connection-string-with-explicit-endpoint-overrides).

## <a name="disable-snapshot-debugger"></a>Zakázat Snapshot Debugger

Použijte stejný postup jako u **možnost povolit Snapshot Debugger**, ale u Snapshot Debugger **vypnout** přepínač obou přepínačů.

Doporučujeme, abyste Snapshot Debugger povolili ve všech aplikacích, abyste mohli snadno diagnostikovat výjimky aplikací.

## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

V případě Azure App Service můžete nastavit nastavení aplikace v rámci šablony Azure Resource Manager tak, aby povolovala Snapshot Debugger a Profiler, a to v následujícím fragmentu šablony:

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  "tags": { 
    "[concat('hidden-related:', resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName')))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[parameters('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "name": "appsettings",
      "type": "config",
      "dependsOn": [
        "[parameters('webSiteName')]",
        "[concat('AppInsights', parameters('webSiteName'))]"
      ],
      "properties": {
        "APPINSIGHTS_INSTRUMENTATIONKEY": "[reference(resourceId('Microsoft.Insights/components', concat('AppInsights', parameters('webSiteName'))), '2014-04-01').InstrumentationKey]",
        "APPINSIGHTS_PROFILERFEATURE_VERSION": "1.0.0",
        "APPINSIGHTS_SNAPSHOTFEATURE_VERSION": "1.0.0",
        "DiagnosticServices_EXTENSION_VERSION": "~3",
        "ApplicationInsightsAgent_EXTENSION_VERSION": "~2"
      }
    }
  ]
},
```

## <a name="next-steps"></a>Další kroky

- Vygenerujte provoz do vaší aplikace, který může aktivovat výjimku. Potom počkejte 10 až 15 minut, než se snímky odešlou do instance Application Insights.
- Podívejte se na [snímky](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) v Azure Portal.
- Nápovědu k řešení potíží s Snapshot Debugger najdete v tématu [řešení potíží s Snapshot Debugger](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

