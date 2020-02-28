---
title: Povolit Snapshot Debugger pro aplikace .NET v Azure App Service | Microsoft Docs
description: Povolit Snapshot Debugger pro aplikace .NET v Azure App Service
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: c23da585034e74d85be5a3c41b124f00408a0f4a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671422"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Povolit Snapshot Debugger pro aplikace .NET v Azure App Service

Snapshot Debugger v současné době funguje pro aplikace ASP.NET a ASP.NET Core, které běží na Azure App Service v plánech služby Windows.

## <a id="installation"></a>Povolit Snapshot Debugger
Pokud chcete povolit Snapshot Debugger pro aplikaci, postupujte podle následujících pokynů. Pokud používáte jiný typ služby Azure, najdete tady pokyny k povolení Snapshot Debugger na jiných podporovaných platformách:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Služby Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines a Virtual Machine Scale Sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Místní virtuální nebo fyzické počítače](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Pokud používáte verzi Preview rozhraní .NET Core, postupujte podle pokynů pro [povolení Snapshot debugger pro další prostředí](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) , aby se do aplikace zahrnul balíček NuGet [Microsoft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) a pak se dokončí zbytek níže uvedených pokynů. 

Application Insights Snapshot Debugger je předem nainstalován jako součást modulu runtime App Services, ale je nutné ji zapnout, abyste získali snímky pro aplikaci App Service. Po nasazení aplikace, a to i v případě, že jste zahrnuli sadu Application Insights SDK ve zdrojovém kódu, použijte následující postup, chcete-li povolit nástroj snapshot Debugger.

1. V Azure Portal otevřete podokno **App Services** .
2. Přejděte do **nastavení > Application Insights** podokně.

   ![Povolit App Insights na portálu služby App Services](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Podle pokynů v podokně vytvořte nový prostředek nebo vyberte existující prostředek App Insights, abyste mohli svoji aplikaci monitorovat. Také se ujistěte, že jsou **zapnuté**oba přepínače pro Snapshot Debugger.

   ![Přidat rozšíření webu App Insights][Enablement UI]

4. Snapshot Debugger je nyní povoleno pomocí nastavení aplikace App Services.

    ![Nastavení aplikace pro Snapshot Debugger][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Zakázat Snapshot Debugger

Použijte stejný postup jako u **možnost povolit Snapshot Debugger**, ale u Snapshot Debugger **vypnout**přepínač obou přepínačů.
Doporučujeme, abyste Snapshot Debugger povolili ve všech aplikacích, abyste usnadnili diagnostiku výjimek aplikací.

## <a name="next-steps"></a>Další kroky

- Vygenerujte provoz do vaší aplikace, který může aktivovat výjimku. Potom počkejte 10 až 15 minut, než se snímky odešlou do instance Application Insights.
- Podívejte se na [snímky](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) v Azure Portal.
- Nápovědu k řešení potíží s Snapshot Debugger najdete v tématu [řešení potíží s Snapshot Debugger](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

