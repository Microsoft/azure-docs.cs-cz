---
title: Monitorování webové aplikace v ASP.NET pomocí Azure Application Insights | Dokumentace Microsoftu
description: Poskytuje pokyny pro rychlé nastavení webové aplikace v ASP.NET pro monitorování pomocí Application Insights
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.custom: mvc
ms.openlocfilehash: ed7cec34b4bca1882d069997d4f89677cda32447
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88080945"
---
# <a name="start-monitoring-your-aspnet-web-application"></a>Zahájení monitorování webové aplikace v ASP.NET

Služba Azure Application Insights umožňuje monitorovat webové aplikace z hlediska dostupnosti, výkonu a využití.  Můžete také rychle identifikovat a diagnostikovat chyby ve vaší aplikaci a nečekat na to, až je nahlásí uživatelé.  Na základě informací o výkonu a efektivitu vaší aplikace, které získáte ze služby Application Insights, můžete informovaně rozhodovat o údržbě a vylepšení vaší aplikace.

V tomto kurzu Rychlý start se dozvíte, jak přidat Application Insights do už existující webové aplikace v ASP.NET a začít analyzovat živé statistiky. Je to jedna z řady různých metod, které můžete využít k analýze vaší aplikace. Pokud nemáte webovou aplikaci ASP.NET, můžete si ji vytvořit v [rychlém startu vytvoření webové aplikace ASP.NET](../../app-service/quickstart-dotnet-framework.md).

## <a name="prerequisites"></a>Předpoklady
K provedení kroků v tomto kurzu Rychlý start je potřeba:

- Nainstalujte [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s následujícími úlohami:
    - Vývoj pro ASP.NET a web
    - Vývoj pro Azure


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="enable-application-insights"></a>Povolení Application Insights

1. Otevřete projekt v aplikaci Visual Studio 2019.
2. V nabídce Projekt vyberte **Nakonfigurovat Application Insights**. Visual Studio přidá Application Insights SDK do vaší aplikace.

    > [!IMPORTANT]
    > Postup pro přidání Application Insights se liší podle typu šablony ASP.NET. Pokud používáte **prázdnou** šablonu nebo šablonu **Mobilní aplikace Azure**, vyberte **Projekt** > **Přidat Telemetrii Application Insights**. Pokyny pro všechny ostatní šablony ASP.NET najdete v kroku výše. 

3. Klikněte na **Začínáme** (starší verze sady Visual Studio mají tlačítko **Začít zdarma**).

    ![Přidání Application Insights k sadě Visual Studio](./media/quick-monitor-portal/add-application-insights-b.png)

4. Vyberte své předplatné a klikněte na **Zaregistrovat**.

5. Vyberte **projekt**  >  **Spravovat balíčky balíčků NuGet**  >  **Zdroj: NuGet.org**  >  **aktualizujte** balíček sady SDK Application Insights na nejnovější stabilní verzi.

6. Aplikaci spusťte výběrem příkazu **Spustit ladění** z nabídky **Ladit** nebo stisknutím klávesy F5.

## <a name="confirm-app-configuration"></a>Potvrzení konfigurace aplikace

Application Insights shromažďuje telemetrická data pro vaši aplikaci bez ohledu na to, kde je spuštěná. Pokud chcete tato data začít zobrazovat, použijte následující kroky.

1. Otevřete Application Insights kliknutím na tlačítko **Zobrazit**  ->  **ostatní**  ->  **Application Insights Windows Search**.  Zobrazí se telemetrie z aktuální relace.<BR><br>![Telemetrie v sadě Visual Studio](./media/quick-monitor-portal/telemetry-in-vs.png)

2. Klikněte na první požadavek v seznamu list (v tomto příkladu GET Home/Index). Zobrazí se podrobné informace o tomto požadavku. Všimněte si, že se kromě dalších cenných informací o požadavku zobrazí také stavový kód a doba odezvy.<br><br>![Podrobnosti o odpovědi v sadě Visual Studio](media/quick-monitor-portal/request-details.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Zahájení monitorování na webu Azure Portal

Teď můžete otevřít Application Insights na webu Azure Portal a prohlédnout si různé podrobnosti o spuštěné aplikaci.

1. Rozbalte složku **připojené služby** (ikona cloudu a modul plug-in) v Průzkumník řešení potom klikněte pravým tlačítkem na složku **Application Insights** a pak klikněte na **otevřít Application Insights portál**.  Zobrazí se informace o vaší aplikaci a řada různých možností.

    ![Mapa aplikace](media/quick-monitor-portal/04-overview.png)

2. Po kliknutí na **Mapa aplikace** se zobrazí rozložení vztahů závislosti mezi komponentami vaší aplikace.  U každé komponenty se zobrazují klíčové ukazatele výkonu, jako je zatížení, výkon, selhání a upozornění.

    ![Mapa aplikace](media/quick-monitor-portal/05-appmap.png)

3. Klikněte na ikonu **analýzy aplikace** ![ zobrazení mapa aplikace ](media/quick-monitor-portal/app-viewinlogs-icon.png) **v části protokoly (Analytics)** na jednu z komponent aplikace. Tím se otevře **protokol (Analytics)**, který poskytuje bohatý dotazovací jazyk pro analýzu všech dat shromážděných v Application Insights. V tomto případě jsme za vás vytvořili dotaz, který vykreslí počet požadavků ve formě grafu. Můžete psát své vlastní dotazy pro analýzu dalších dat.

    ![Analýzy](media/quick-monitor-portal/6viewanalytics.png)

4. Na levé straně v části prozkoumat klikněte na **Live Metrics Stream** . Zobrazí se provozní statistika vaší aplikace, protože je spuštěná. Zahrnují mimo jiné údaje o počtu příchozích požadavků, době jejich trvání a všech chybách, ke kterým došlo. Můžete také prohlížet nejdůležitější metriky výkonu, jako je procesor a paměť.

    ![Live Stream](media/quick-monitor-portal/7livemetrics.png)

    Pokud jste připraveni hostovat vaši aplikace v Azure, můžete ji teď publikovat. Postupujte podle kroků popsaných v [kurzu Rychlý start pro vytvoření webové aplikace v ASP.NET](../../app-service/quickstart-dotnetcore.md#update-the-app-and-redeploy).

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

## <a name="clean-up-resources"></a>Vyčištění prostředků
Až budete s testováním hotovi, můžete odstranit skupinu prostředků a všechny související prostředky. Provedete to podle následujících kroků.
1. Na webu Azure Portal v nabídce vlevo klikněte na **Skupiny prostředků** a pak na **myResourceGroup**.
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte **myResourceGroup** a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste povolili aplikaci pro monitorování pomocí služby Azure Application Insights.  Pokud chcete zjistit, jak ji používat k monitorování statistik a detekování potíží ve vaší aplikaci, přejděte k dalším kurzům.

> [!div class="nextstepaction"]
> [Kurzy k Azure Application Insights](tutorial-runtime-exceptions.md)
