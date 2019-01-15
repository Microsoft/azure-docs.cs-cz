---
title: Rychlý start pro Azure Application Insights | Dokumentace Microsoftu
description: Pokyny pro rychlé nastavení webové aplikace ASP.NET Core pro monitorování s využitím Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/11/2018
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 14295bca12e64174d03cfd1c2bd8d214d801e8e5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54261056"
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

Pokud webovou aplikaci ASP.NET Core nemáte, můžete použít našeho podrobného průvodce k [vytvoření aplikace ASP.NET Core a přidání Application Insights](../../azure-monitor/app/asp-net-core.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Povolení Application Insights

Application Insights může shromažďovat telemetrická data ze všech aplikací připojených k internetu bez ohledu na to, jestli jsou spuštěné místně nebo v cloudu. Pokud chcete tato data začít zobrazovat, použijte následující kroky.

1. Vyberte **Vytvořit prostředek** > **Monitorování a správa** > **Application Insights**.

   ![Přidání prostředku Application Insights](./media/dotnetcore-quick-start/0001-dc.png)

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

    ![Přidání Telemetrie Application Insights](./media/dotnetcore-quick-start/0001.png)

2. Klikněte na tlačítko **Začít zdarma**, vyberte **Existující prostředek**, který jste vytvořili na webu Azure Portal, a klikněte na **Zaregistrovat**.

3. Vyberte **Ladění** > **Spustit bez ladění** (Ctrl + F5) a spusťte vaši aplikaci.

> [!NOTE]
> Trvá 3–5 minut, než se na portálu začnou zobrazovat data. Pokud používáte testovací aplikaci s nízkým provozem, nezapomínejte, že většina metrik se zachycuje, jenom když jsou nějaké aktivní požadavky nebo operace.

## <a name="start-monitoring-in-the-azure-portal"></a>Zahájení monitorování na webu Azure Portal

1. Teď můžete znovu otevřít stránku **Přehled** služby Application Insights na webu Azure Portal výběrem **Projekt** > **Application Insights** > **Otevřít portál Application Insights** a zobrazit podrobné informace o aktuálně spuštěné aplikaci.

   ![Nabídka Přehled služby Application Insights](./media/dotnetcore-quick-start/overview-001.png)

2. Po kliknutí na **Mapa aplikace** se zobrazí rozložení vztahů závislosti mezi komponentami vaší aplikace. U každé komponenty se zobrazují klíčové ukazatele výkonu, jako je zatížení, výkon, selhání a upozornění.

   ![Mapa aplikace](./media/dotnetcore-quick-start/application-map.png)

3. Klikněte na ikonu **Analýza aplikace** ![Ikona Mapa aplikace](./media/dotnetcore-quick-start/006.png).  Otevře se **Application Insights – Analytics** s bohatým dotazovacím jazykem pro analýzu všech dat shromážděných službou Application Insights. V tomto případě jsme za vás vytvořili dotaz, který vykreslí počet požadavků ve formě grafu. Můžete psát své vlastní dotazy pro analýzu dalších dat.

   ![Graf analýzy uživatelských požadavků za časové období](./media/dotnetcore-quick-start/0007-dc.png)

4. Vraťte se **přehled** stránce a prozkoumejte řídicích panelů klíčového ukazatele výkonu.  Tento řídicí panel poskytuje statistické údaje o stavu vaší aplikace, včetně počtu příchozích požadavků, doby jejich trvání a všech chyb, ke kterým došlo. 

   ![Graf s časovou osou přehledu stavu](./media/dotnetcore-quick-start/overview-graphs.png)

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

5. Klikněte na **Prohlížeč** pod záhlavím **Prozkoumat**. Tady najdete metriky související s výkonem stránek vaší aplikace. Můžete kliknout na **Přidat nový graf** a vytvořit další vlastní zobrazení nebo vybrat **Upravit** a upravit existující typy grafů, jejich výšku, paletu barev, seskupení a metriky.

   ![Graf metrik serveru](./media/dotnetcore-quick-start/009-Black.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v práci s dalšími rychlými starty nebo kurzy, nevymazávejte prostředky vytvořené v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujících kroků odstraňte všechny prostředky vytvořené tímto rychlým startem na webu Azure Portal.

1. Na webu Azure Portal v nabídce vlevo klikněte na **Skupiny prostředků** a pak na **myResourceGroup**.
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte **myResourceGroup** a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vyhledání a diagnostika běhových výjimek](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)
