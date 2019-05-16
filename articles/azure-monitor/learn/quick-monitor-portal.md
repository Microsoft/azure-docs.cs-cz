---
title: Monitorování webové aplikace v ASP.NET pomocí Azure Application Insights | Dokumentace Microsoftu
description: Pokyny pro rychlé nastavení webové aplikace ASP.NET pro monitorování pomocí Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/01/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 08745c3ef3d4996340ec40af496f8f0a5e0201c3
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595739"
---
# <a name="start-monitoring-your-aspnet-web-application"></a>Zahájení monitorování webové aplikace v ASP.NET

Azure Application Insights umožňuje snadné monitorování webové aplikace z hlediska dostupnosti, výkonu a využití.  Můžete také rychle identifikovat a diagnostikovat chyby ve vaší aplikaci a nečekat na to, až je nahlásí uživatelé.  Na základě informací o výkonu a efektivitu vaší aplikace, které získáte ze služby Application Insights, můžete informovaně rozhodovat o údržbě a vylepšení vaší aplikace.

V tomto kurzu Rychlý start se dozvíte, jak přidat Application Insights do už existující webové aplikace v ASP.NET a začít analyzovat živé statistiky. Je to jedna z řady různých metod, které můžete využít k analýze vaší aplikace. Pokud webovou aplikaci ASP.NET, můžete si ji vytvořit [vytvořit rychlý start webové aplikaci ASP.NET](../../app-service/app-service-web-get-started-dotnet-framework.md).

## <a name="prerequisites"></a>Požadavky
K provedení kroků v tomto kurzu Rychlý start je potřeba:

- Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/downloads/) s následujícími sadami funkcí:
    - Vývoj pro ASP.NET a web
    - Vývoj pro Azure


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="enable-application-insights"></a>Povolit Application Insights

1. Otevřete projekt v aplikaci Visual Studio 2019.
2. V nabídce Projekt vyberte **Nakonfigurovat Application Insights**. Visual Studio přidá Application Insights SDK do vaší aplikace.

    > [!IMPORTANT]
    > Postup pro přidání Application Insights se liší podle typu šablony ASP.NET. Pokud používáte **prázdnou** šablonu nebo šablonu **Mobilní aplikace Azure**, vyberte **Projekt** > **Přidat Telemetrii Application Insights**. Pokyny pro všechny ostatní šablony ASP.NET najdete v kroku výše. 

3. Klikněte na **Začínáme** (starší verze sady Visual Studio mají tlačítko **Začít zdarma**).

    ![Přidání Application Insights k sadě Visual Studio](./media/quick-monitor-portal/add-application-insights-b.png)

4. Vyberte své předplatné a klikněte na **Zaregistrovat**.

5. Aplikaci spusťte výběrem příkazu **Spustit ladění** z nabídky **Ladit** nebo stisknutím klávesy F5.

## <a name="confirm-app-configuration"></a>Potvrzení konfigurace aplikace

Application Insights shromažďuje telemetrická data pro vaši aplikaci bez ohledu na to, kde je spuštěná. Pokud chcete tato data začít zobrazovat, použijte následující kroky.

1. Kliknutím na **Zobrazení** -> **Ostatní okna** -> **Hledání Application Insights** otevřete službu Application Insights.  Zobrazí se telemetrie z aktuální relace.<BR><br>![Telemetrie v sadě Visual Studio](./media/quick-monitor-portal/telemetry-in-vs.png)

2. Klikněte na první požadavek v seznamu list (v tomto příkladu GET Home/Index). Zobrazí se podrobné informace o tomto požadavku. Všimněte si, že se kromě dalších cenných informací o požadavku zobrazí také stavový kód a doba odezvy.<br><br>![Podrobnosti o odpovědi v sadě Visual Studio](media/quick-monitor-portal/request-details.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Zahájení monitorování na webu Azure Portal

Teď můžete otevřít Application Insights na webu Azure Portal a prohlédnout si různé podrobnosti o spuštěné aplikaci.

1. Rozbalte **připojené služby** složky (ikona cloudu a moduly) v Průzkumníku řešení klikněte pravým tlačítkem myši na **Application Insights** složky a klikněte na tlačítko **otevřít portál Application Insights** .  Zobrazí se informace o vaší aplikaci a řada různých možností.

    ![Mapa aplikace](media/quick-monitor-portal/4overview.png)

2. Po kliknutí na **Mapa aplikace** se zobrazí rozložení vztahů závislosti mezi komponentami vaší aplikace.  U každé komponenty se zobrazují klíčové ukazatele výkonu, jako je zatížení, výkon, selhání a upozornění.

    ![Mapa aplikace](media/quick-monitor-portal/5appmap.png)

3. Klikněte na **analýzy aplikací** ikonu ![Mapa aplikace](media/quick-monitor-portal/app-analytics-icon.png) **zobrazit v Analytics** na jednotlivé součásti aplikace. Otevře se **Application Insights – Analytics** s bohatým dotazovacím jazykem pro analýzu všech dat shromážděných službou Application Insights.  V tomto případě jsme za vás vytvořili dotaz, který vykreslí počet požadavků ve formě grafu. Můžete psát své vlastní dotazy pro analýzu dalších dat.

    ![Analýza](media/quick-monitor-portal/6viewanalytics.png)

4. Klikněte na **Live Metrics Stream** na levé straně v části prozkoumat. Zobrazí se provozní statistika vaší aplikace, protože je spuštěná. Zahrnují mimo jiné údaje o počtu příchozích požadavků, době jejich trvání a všech chybách, ke kterým došlo. Můžete také prohlížet nejdůležitější metriky výkonu, jako je procesor a paměť.

    ![Live Stream](media/quick-monitor-portal/7livemetrics.png)

    Pokud jste připraveni hostovat vaši aplikace v Azure, můžete ji teď publikovat. Postupujte podle kroků popsaných v [kurzu Rychlý start pro vytvoření webové aplikace v ASP.NET](../../app-service/app-service-web-get-started-dotnet.md#update-the-app-and-redeploy).

5. Pokud k přidání monitorování pomocí Application Insights použijete sadu Visual Studio, můžete automaticky přidat monitorování na straně klienta. Pokud chcete do aplikace přidat monitorování na straně klienta ručně, přidejte do aplikace následující kód JavaScriptu:

```html
<!-- 
To collect user behavior analytics about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var appInsights=window.appInsights||function(a){
  function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
  }({
      instrumentationKey:"<your instrumentation key>"
  });

window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
</script>
```

Další informace najdete v úložišti naší [open source sady JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS) na GitHubu.

## <a name="video"></a>Video

* Externí krok za krokem videu o [konfigurace Application Insights s .NET aplikace od začátku](https://www.youtube.com/watch?v=blnGAVgMAfA).

## <a name="clean-up-resources"></a>Vyčištění prostředků
Po dokončení testování, můžete odstranit skupinu prostředků a všechny související prostředky. Chcete proto podle následujících pokynů.
1. Na webu Azure Portal v nabídce vlevo klikněte na **Skupiny prostředků** a pak na **myResourceGroup**.
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte **myResourceGroup** a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další postup
V tomto rychlém startu jste povolili monitorování pomocí Azure Application Insights vaší aplikace.  Pokud chcete zjistit, jak ji používat k monitorování statistik a detekování potíží ve vaší aplikaci, přejděte k dalším kurzům.

> [!div class="nextstepaction"]
> [Kurzy k Azure Application Insights](tutorial-runtime-exceptions.md)
