---
title: Profilování živých webových aplikací Azure pomocí Application Insights | Dokumentace Microsoftu
description: Profil živých webových aplikací v Azure pomocí Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 2f726c9c70e6e46ad2e82e9d6f15dae2c9d3d008
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724685"
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profilování živých webových aplikací Azure pomocí Application Insights

Profiler v současné době používá pro webové aplikace ASP.NET a ASP.NET Core, které běží ve službě Web Apps. Základní úroveň služby nebo vyšší je potřeba použít Profiler.

## <a id="installation"></a> Povolit Profiler pro svoje webové aplikace
Pokud chcete povolit Profiler pro webovou aplikaci, postupujte podle pokynů níže. Pokud používáte jiný typ služby Azure, tady jsou pokyny pro povolení Profiler na jiné podporované platformy:
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Aplikace Service Fabric](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


Application Insights Profiler je předinstalovaná jako součást modulu runtime služeb App Services, ale musíte vypnout k získání profily pro Azure Web Apps. Jakmile nasadíte webovou aplikaci, i v případě, že jste zahrnuli App Insights SDK do zdrojového kódu, postupujte podle následujících kroků, abyste povolení profileru.

1. Přejděte **App Services** podokně webu Azure Portal.
1. Přejděte do **Nastavení > monitorování** podokně.

   ![Povolit App Insights na portálu služby App Services](./media/app-insights-profiler/AppInsights-AppServices.png)

1. Buď postupujte podle pokynů v podokně a vytvořte nový prostředek nebo vyberte existující prostředek App Insights monitorovat webové aplikace. Také se ujistěte, Profiler je **na**.

   ![Přidat rozšíření webu App Insights][Enablement UI]

1. Profiler se teď aktivuje pomocí nastavení aplikace služby App.

    ![Nastavení aplikace, které pro Profiler][profiler-app-setting]

## <a name="disable-profiler"></a>Zakázat Profiler

Pro zastavení nebo restartování Profiler pro instance jednotlivých webovou aplikaci, v části **webové úlohy**, přejděte do prostředku webové aplikace. Chcete-li odstranit Profiler, přejděte na **rozšíření**.

![Zakázat Profiler pro webové úlohy][disable-profiler-webjob]

Doporučujeme, abyste měli Profiler povolena na všech svých webových aplikací se co nejdříve zjistit problémy s výkonem.

Pokud pomocí nasazení webu nasadit změny do vaší webové aplikace, ujistěte se, že se že můžete vyloučit složku App_Data odstranit během nasazení. V opačném případě rozšíření Profiler soubory jsou odstraněny při příštím nasazení webové aplikace do Azure.



## <a name="next-steps"></a>Další postup

* [Práce s Application Insights v sadě Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[Enablement UI]: ./media/app-insights-profiler/Enablement_UI.png
[profiler-app-setting]:./media/app-insights-profiler/profiler-app-setting.png
[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png

