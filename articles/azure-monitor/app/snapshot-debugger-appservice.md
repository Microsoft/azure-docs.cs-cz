---
title: Povolit Snapshot Debugger pro aplikace .NET v Azure App Service | Dokumentace Microsoftu
description: Povolit Snapshot Debugger pro aplikace .NET v Azure App Service
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: bfung
ms.openlocfilehash: 3e8ce3c2eff7b1f7184bb37f141e62563d4fe714
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612680"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Povolit Snapshot Debugger pro aplikace .NET v Azure App Service

Snapshot Debugger aktuálně funguje pro aplikace ASP.NET a ASP.NET Core, na kterých běží ve službě Azure App Service v plánech služby Windows service.

## <a id="installation"></a> Povolit ladicí program snímků
Pokud chcete povolit Snapshot Debugger pro aplikace, postupujte podle pokynů níže. Pokud používáte jiný typ služby Azure, tady jsou pokyny pro povolení Snapshot Debugger na jiné podporované platformy:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Služby Azure Service Fabric](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines a virtuálních počítačů škálovací sady](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Místní virtuální nebo fyzické počítače](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Pokud používáte verzi preview sady .NET Core, postupujte podle pokynů pro [povolit Snapshot Debugger pro jiná prostředí](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) nejprve chcete zahrnout [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet balíček s aplikací a pak dokončete následující pokyny. 

Application Insights Snapshot debuggeru je předinstalovaná jako součást modulu runtime služeb App Services, ale musíte vypnout k získání snímky pro aplikaci služby App Service. Jakmile jste nasadili aplikaci, i v případě, že jste zahrnuli sadu SDK Application Insights ve zdrojovém kódu, postupujte podle postupem uvedeným níže povolte snapshot debugger.

1. Přejděte **App Services** podokně webu Azure Portal.
2. Přejděte do **Nastavení > Application Insights** podokně.

   ![Povolit App Insights na portálu služby App Services](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Buď podle pokynů a vytvořte nový prostředek nebo vyberte existující prostředek App Insights pro sledování aplikace podokna. Také ujistěte se, že jsou oba přepínače pro Snapshot Debugger **na**.

   ![Přidat rozšíření webu App Insights][Enablement UI]

4. Snapshot Debugger se teď aktivuje pomocí nastavení aplikace služby App.

    ![Nastavení aplikace, které pro Snapshot Debugger][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Zakázání ladicího programu snímků

Postupujte stejným způsobem jako u **povolit Snapshot Debugger**, ale přepnout oba přepínače pro Snapshot Debugger k **vypnout**.
Doporučujeme, abyste měli Snapshot Debugger povolená na všech svých aplikací pro usnadnění diagnostiky výjimky aplikací.

## <a name="next-steps"></a>Další postup

- Generovat provozu do vaší aplikace, který může vyvolat výjimku. Vyčkejte 10 až 15 minut, než se snímky k odeslání do instance služby Application Insights.
- Zobrazit [snímky](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) na webu Azure Portal.
- Vám pomůžou při řešení potíží Snapshot Debugger [řešení potíží s Snapshot Debugger](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

