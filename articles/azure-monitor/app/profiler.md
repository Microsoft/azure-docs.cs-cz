---
title: Profilování živých aplikací Azure App Service pomocí nástroje Application Insights | Dokumentace Microsoftu
description: Profilování živých aplikací ve službě Azure App Service s Application Insights Profiler.
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
ms.openlocfilehash: daba72639e190bb255dd515237d25c20df433901
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075018"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Profilování živých aplikací Azure App Service pomocí nástroje Application Insights

Profiler se aktuálně funguje pro aplikace ASP.NET a ASP.NET Core, na kterých běží ve službě Azure App Service. Základní úroveň služby nebo vyšší je potřeba použít Profiler. Povolení Profiler v Linuxu je aktuálně možné jenom přes [tato metoda](profiler-aspnetcore-linux.md).

## <a id="installation"></a> Povolit Profiler pro vaši aplikaci
Pokud chcete povolit Profiler pro aplikaci, postupujte podle pokynů níže. Pokud používáte jiný typ služby Azure, tady jsou pokyny pro povolení Profiler na jiné podporované platformy:
* [Cloud Services](../../azure-monitor/app/profiler-cloudservice.md ?toc=/azure/azure-monitor/toc.json)
* [Aplikace Service Fabric](../../azure-monitor/app/profiler-servicefabric.md ?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler je předinstalovaná jako součást modulu runtime služeb App Services, ale musíte vypnout k získání profily pro aplikace app Service. Jakmile jste nasadili aplikaci, i v případě, že jste zahrnuli App Insights SDK do zdrojového kódu, postupujte podle následujících kroků, abyste povolení profileru.

1. Přejděte **App Services** podokně webu Azure Portal.
2. Přejděte do **Nastavení > Application Insights** podokně.

   ![Povolit App Insights na portálu služby App Services](./media/profiler/AppInsights-AppServices.png)

3. Buď podle pokynů a vytvořte nový prostředek nebo vyberte existující prostředek App Insights pro sledování aplikace podokna. Také se ujistěte, Profiler je **na**.

   ![Přidat rozšíření webu App Insights][Enablement UI]

4. Profiler se teď aktivuje pomocí nastavení aplikace služby App.

    ![Nastavení aplikace, které pro Profiler][profiler-app-setting]

## <a name="disable-profiler"></a>Zakázat Profiler

Pro zastavení nebo restartování Profiler pro jednotlivé aplikace instance, v části **webové úlohy**, přejděte na prostředek aplikace. Chcete-li odstranit Profiler, přejděte na **rozšíření**.

![Zakázat Profiler pro webové úlohy][disable-profiler-webjob]

Doporučujeme, abyste měli Profiler povolená na všech svých aplikací se co nejdříve zjistit problémy s výkonem.

Pokud pomocí nasazení webu nasadit změny do vaší webové aplikace, ujistěte se, že se že můžete vyloučit složku App_Data odstranit během nasazení. V opačném případě rozšíření Profiler soubory jsou odstraněny při příštím nasazení webové aplikace do Azure.



## <a name="next-steps"></a>Další postup

* [Práce s Application Insights v sadě Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
