---
title: Povolení ladicího programu snímků pro aplikace .NET ve službě Azure App Service | Dokumenty společnosti Microsoft
description: Povolení ladicího programu snímků pro aplikace .NET ve službě Azure App Service
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 8af688e38003e0613a06d7d8622ce279a3838589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298276"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Povolení ladicího programu snímků pro aplikace .NET ve službě Azure App Service

Ladicí program snímků aktuálně funguje pro ASP.NET a ASP.NET základní aplikace, které běží ve službě Azure App Service v plánech služeb Windows.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a>Povolit ladicí program snímků
Chcete-li povolit ladicí program snímků pro aplikaci, postupujte podle následujících pokynů. Pokud používáte jiný typ služby Azure, tady jsou pokyny pro povolení debugger snímek na jiných podporovaných platformách:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Služby Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Virtuální počítače Azure a škálovací sady virtuálních počítačů](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Místní virtuální nebo fyzické počítače](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Pokud používáte verzi preview .NET Core, postupujte podle pokynů pro [povolení snímek debugger pro jiná prostředí](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) nejprve zahrnout [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet balíček s aplikací a potom dokončete zbytek níže uvedených pokynů. 

Debugger snímků Přehledy aplikací je předinstalovaný jako součást runtime služby App Services, ale musíte ho zapnout, abyste získali snímky pro vaši aplikaci App Service. Po nasazení aplikace, i když jste zahrnuli Application Insights SDK ve zdrojovém kódu, postupujte podle následujících kroků a povolte ladicí program snímku.

1. Přejděte do podokna **Služby pro aplikace** na webu Azure Portal.
2. Přejděte do podokna **Nastavení > přehledy aplikací.**

   ![Povolení přehledů aplikací na portálu služby App Services](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Podle pokynů v podokně vytvořte nový prostředek nebo vyberte existující prostředek App Insights, který bude sledovat vaši aplikaci. Také se ujistěte, že oba přepínače pro snímek debugger jsou **zapnuty**.

   ![Přidání rozšíření webu Přehledy aplikací][Enablement UI]

4. Ladicí program snímků je teď povolený pomocí nastavení aplikací služby App Services.

    ![Nastavení aplikace pro ladicí program snímků][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Zakázat ladicí program snímků

Postupujte podle stejných kroků jako u **programu Povolit ladicí program snímků**, ale přepněte oba přepínače pro ladicí program snímků na **Vypnuto**.
Doporučujeme, abyste ve všech aplikacích povolili ladicí program snímků, abyste usnadnili diagnostiku výjimek aplikací.

## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

Pro službu Azure App Service můžete nastavit nastavení aplikace v šabloně Azure Resource Manager povolit snímek debugger a Profiler. Přidáte konfigurační prostředek, který obsahuje nastavení aplikace jako podřízený prostředek webu:

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

- Generovat provoz do aplikace, která může vyvolat výjimku. Potom počkejte 10 až 15 minut pro snímky, které mají být odeslány do instance Application Insights.
- Podívejte se na [snímky](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) na webu Azure Portal.
- Nápovědu k řešení problémů s ladicím programem snímků naleznete v [tématu Odstraňování ladicích snímků snímek](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

