---
title: Rychlý start pro Azure Application Insights | Dokumentace Microsoftu
description: Pokyny pro rychlé nastavení webové aplikace ASP.NET Core pro monitorování pomocí Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/29/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: eae9dc6447dd8211a3919c52beaea64274fc0ec5
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58801075"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Zahájení monitorování webové aplikace ASP.NET Core

Azure Application Insights umožňuje snadné monitorování webové aplikace z hlediska dostupnosti, výkonu a využití. Můžete také rychle identifikovat a diagnostikovat chyby ve vaší aplikaci a nečekat na to, až je nahlásí uživatelé. 

Tento rychlý start vás provede přidáním sady Application Insights SDK do existující webové aplikace ASP.NET Core. 

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

- [Nainstalovat sadu Visual Studio 2017](https://www.visualstudio.com/downloads/) s následujícími sadami funkcí:
  - Vývoj pro ASP.NET a web
  - Vývoj pro Azure
- [Nainstalovat sadu .NET Core 2.0 SDK](https://www.microsoft.com/net/core)
- Budete potřebovat předplatné Azure a webovou aplikaci v .NET Core.

Pokud webová aplikace ASP.NET Core nemáte, můžete použít naši podrobného průvodce můžete [vytvoření aplikace ASP.NET Core a přidání služby Application Insights.](../../azure-monitor/app/asp-net-core.md)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Povolení Application Insights

Application Insights může shromažďovat telemetrická data ze všech aplikací připojených k internetu bez ohledu na to, jestli jsou spuštěné místně nebo v cloudu. Pokud chcete tato data začít zobrazovat, použijte následující kroky.

1. Vyberte **Vytvořit prostředek** > **Vývojářské nástroje** > **Application Insights**.

   ![Přidání prostředku Application Insights](./media/dotnetcore-quick-start/1createresourceappinsight.png)

    Zobrazí se konfigurační pole. K vyplnění vstupních polí použijte následující tabulku.

    | Nastavení        |  Hodnota           | Popis  |
   | ------------- |:-------------|:-----|
   | **Název**      | Globálně jedinečná hodnota | Název identifikující aplikaci, kterou monitorujete |
   | **Typ aplikace** | Webová aplikace ASP.NET | Typ aplikace, kterou monitorujete |
   | **Skupina prostředků**     | myResourceGroup      | Název pro novou skupinu prostředků, která bude hostovat data App Insights |
   | **Umístění** | USA – východ | Vyberte umístění ve vaší blízkosti nebo v blízkosti místa, kde se vaše aplikace hostuje. |

2. Klikněte na možnost **Vytvořit**.

## <a name="configure-app-insights-sdk"></a>Konfigurace sady App Insights SDK

1. Otevřete svůj **projekt** webové aplikace ASP.NET Core v sadě Visual Studio, klikněte pravým tlačítkem na název aplikace v **Průzkumníku řešení** a vyberte **Přidat** > **Telemetrie Application Insights**.

    ![Přidání Telemetrie Application Insights](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Klikněte na tlačítko **Začínáme** tlačítko

3. Vyberte svůj účet a předplatné > vyberte **existující prostředek** jste vytvořili na webu Azure Portal > klikněte na tlačítko **zaregistrovat**.

4. Vyberte **Ladění** > **Spustit bez ladění** (Ctrl + F5) a spusťte vaši aplikaci.

    ![Nabídka Přehled služby Application Insights](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> Trvá 3–5 minut, než se na portálu začnou zobrazovat data. Pokud používáte testovací aplikaci s nízkým provozem, nezapomínejte, že většina metrik se zachycuje, jenom když jsou nějaké aktivní požadavky nebo operace.

## <a name="start-monitoring-in-the-azure-portal"></a>Zahájení monitorování na webu Azure Portal

1. Znovu otevřít Application Insights **přehled** stránky na webu Azure Portal tak, že vyberete **Domů** a v části poslední prostředků vyberte prostředek, který jste vytvořili dříve, chcete-li zobrazit podrobnosti o vaše aktuálně spuštěné aplikace.

   ![Nabídka Přehled služby Application Insights](./media/dotnetcore-quick-start/4overview.png)

2. Po kliknutí na **Mapa aplikace** se zobrazí rozložení vztahů závislosti mezi komponentami vaší aplikace. U každé komponenty se zobrazují klíčové ukazatele výkonu, jako je zatížení, výkon, selhání a upozornění.

   ![Mapa aplikace](./media/dotnetcore-quick-start/5appmap.png)

3. Klikněte na **analýzy aplikací** ikonu ![ikona Mapa aplikace](./media/dotnetcore-quick-start/006.png) **zobrazit v Analytics**. Otevře se **Application Insights – Analytics** s bohatým dotazovacím jazykem pro analýzu všech dat shromážděných službou Application Insights. V tomto případě jsme za vás vytvořili dotaz, který vykreslí počet požadavků ve formě grafu. Můžete psát své vlastní dotazy pro analýzu dalších dat.

   ![Graf analýzy uživatelských požadavků za časové období](./media/dotnetcore-quick-start/6analytics.png)

4. Vraťte se **přehled** stránce a prozkoumejte řídicích panelů klíčového ukazatele výkonu.  Tento řídicí panel poskytuje statistické údaje o stavu vaší aplikace, včetně počtu příchozích požadavků, doby jejich trvání a všech chyb, ke kterým došlo. 

   ![Graf s časovou osou přehledu stavu](./media/dotnetcore-quick-start/7kpidashboards.png)

   Pokud chcete povolit, aby se graf **Doba načtení zobrazení stránky** naplnil **telemetrií na straně klienta**, na každou stránku, kterou chcete sledovat, přidejte následující skript:

   ```HTML
   <!-- 
   To collect user behavior analytics about your application, 
   insert the following script into each page you want to track.
   Place this code immediately before the closing </head> tag,
   and before any other scripts. Your first data will appear 
   automatically in just a few seconds.
   -->
   <script type="text/javascript">
     var appInsights=window.appInsights||function(config){
       function i(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s="AuthenticatedUserContext",h="start",c="stop",l="Track",a=l+"Event",v=l+"Page",y=u.createElement(o),r,f;y.src=config.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(y);try{t.cookie=u.cookie}catch(p){}for(t.queue=[],t.version="1.0",r=["Event","Exception","Metric","PageView","Trace","Dependency"];r.length;)i("track"+r.pop());return i("set"+s),i("clear"+s),i(h+a),i(c+a),i(h+v),i(c+v),i("flush"),config.disableExceptionTracking||(r="onerror",i("_"+r),f=e[r],e[r]=function(config,i,u,e,o){var s=f&&f(config,i,u,e,o);return s!==!0&&t["_"+r](config,i,u,e,o),s}),t
       }({
           instrumentationKey:"<insert instrumentation key>"
       });
       
       window.appInsights=appInsights;
       appInsights.trackPageView();
   </script>
   ```

5. Na levém klikněte na **metriky**. Pomocí Průzkumníka metrik k prozkoumání stavu a využití vašich prostředků. Můžete kliknout na **Přidat nový graf** a vytvořit další vlastní zobrazení nebo vybrat **Upravit** a upravit existující typy grafů, jejich výšku, paletu barev, seskupení a metriky. Například můžete vytvořit graf, který zobrazuje čas načítání stránky prohlížeče průměrné výběrem "Doba načítání stránek prohlížečem" z metrik rozevíracího seznamu a "Avg" z agregace. Další informace o Průzkumníku metrik Azure návštěvě [Začínáme s Průzkumníkem metrik Azure](../../azure-monitor/platform/metrics-getting-started.md).

     ![Karta metriky: Průměrná prohlížeči stránku zatížení grafu](./media/dotnetcore-quick-start/8metrics.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Po dokončení testování, můžete odstranit skupinu prostředků a všechny související prostředky. Chcete proto podle následujících pokynů.

1. Na webu Azure Portal v nabídce vlevo klikněte na **Skupiny prostředků** a pak na **myResourceGroup**.
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte **myResourceGroup** a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vyhledání a diagnostika běhových výjimek](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)
