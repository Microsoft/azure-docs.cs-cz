---
title: 'Rychlý start: Monitorovat pomocí Azure Application Insights'
description: Pokyny pro rychlé nastavení webové aplikace Node.js pro monitorování s využitím Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/12/2019
ms.service: application-insights
ms.custom: mvc, seo-javascript-2019
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 3312822d3e4f74a2db39712d61880c8dd0dd8d5e
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001167"
---
# <a name="start-monitoring-your-nodejs-web-application"></a>Zahájení monitorování webové aplikace Node.js

Azure Application Insights umožňuje snadné monitorování webové aplikace z hlediska dostupnosti, výkonu a využití. Můžete také rychle identifikovat a diagnostikovat chyby ve vaší aplikaci a nečekat na to, až je nahlásí uživatelé. Pomocí sady SDK verze 0.20 a novější můžete monitorovat běžné balíčky třetích stran, včetně MongoDB, MySQL a Redis.

Tento rychlý start vás provede přidáním sady Application Insights SDK pro Node.js verze 0.22 do existující webové aplikace Node.js.

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

- Potřebujete předplatné Azure a webovou aplikaci Node.js.

Pokud webovou aplikaci Node.js nemáte, můžete si ji vytvořit pomocí [kurzu Rychlý start pro vytvoření webové aplikace Node.js](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Povolit Application Insights

Application Insights může shromažďovat telemetrická data ze všech aplikací připojených k internetu bez ohledu na to, jestli jsou spuštěné místně nebo v cloudu. Pokud chcete tato data začít zobrazovat, použijte následující kroky.

1. Vyberte **Vytvořit prostředek** > **Vývojářské nástroje** > **Application Insights**.

   ![Přidání prostředku Application Insights](./media/nodejs-quick-start/1createresourseappinsights.png)

   > [!NOTE]
   >Pokud Application Insights prostředek vytvoříte poprvé, můžete si o tom přečíst další informace v dokumentu [vytvoření prostředku Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) .

   Zobrazí se stránka konfigurace; k vyplnění vstupních polí použijte následující tabulku. 

    | Nastavení        | Value           | Popis  |
   | ------------- |:-------------|:-----|
   | **Název**      | Globálně jedinečná hodnota | Název identifikující aplikaci, kterou monitorujete |
   | **Typ aplikace** | Aplikace Node.js | Typ aplikace, kterou monitorujete |
   | **Location** | East US | Vyberte umístění ve vaší blízkosti nebo v blízkosti místa, kde se vaše aplikace hostuje. |

2. Vyberte **Vytvořit**.

## <a name="configure-app-insights-sdk"></a>Konfigurace sady App Insights SDK

1. Vyberte **Přehled** a zkopírujte **klíč instrumentace**vaší aplikace.

   ![Formulář Nový prostředek App Insights](./media/nodejs-quick-start/3key.png)

2. Přidejte sadu Application Insights pro Node.js do vaší aplikace. Z kořenové složky vaší aplikace spusťte:

   ```bash
   npm install applicationinsights --save
   ```

3. Upravte první soubor .js vaší aplikace a do horní části skriptu přidejte následující dva řádky. Pokud používáte [aplikaci Rychlý start pro Node.js](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs), upravíte soubor index.js. Nahraďte &lt;instrumentační klíč&gt; klíčem vaší aplikace. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Restartujte aplikaci.

> [!NOTE]
> Trvá 3–5 minut, než se na portálu začnou zobrazovat data. Pokud používáte testovací aplikaci s nízkým provozem, nezapomínejte, že většina metrik se zachycuje, jenom když dochází k nějakým aktivním požadavkům nebo operacím.

## <a name="start-monitoring-in-the-azure-portal"></a>Zahájení monitorování na webu Azure Portal

1. Teď můžete znovu otevřít stránku **Přehled** služby Application Insights na webu Azure Portal, kde jste získali svůj instrumentační klíč, a zobrazit podrobné informace o aktuálně spuštěné aplikaci.

   ![Nabídka Přehled služby Application Insights](./media/nodejs-quick-start/4overview.png)

2. Vyberte možnost **Mapa aplikace** pro vizuální rozložení vztahů závislosti mezi komponentami vaší aplikace. U každé komponenty se zobrazují klíčové ukazatele výkonu, jako je zatížení, výkon, selhání a upozornění.

   ![Mapa aplikace](./media/nodejs-quick-start/5appmap.png)

3. Vyberte ![ikonu **Analýza** aplikace ikona mapa](./media/nodejs-quick-start/006.png) aplikace **Zobrazit v části analýzy**.  Otevře se **Application Insights – Analytics** s bohatým dotazovacím jazykem pro analýzu všech dat shromážděných službou Application Insights. V tomto případě jsme za vás vytvořili dotaz, který vykreslí počet požadavků ve formě grafu. Můžete psát své vlastní dotazy pro analýzu dalších dat.

   ![Graf analýzy uživatelských požadavků za časové období](./media/nodejs-quick-start/6analytics.png)

4. Vraťte se na stránku **Přehled** a prozkoumejte grafy klíčových ukazatelů výkonu.  Tento řídicí panel poskytuje statistické údaje o stavu vaší aplikace, včetně počtu příchozích požadavků, doby jejich trvání a všech chyb, ke kterým došlo.

   ![Graf s časovou osou přehledu stavu](./media/nodejs-quick-start/7kpidashboards.png)

   Pokud chcete povolit, aby se graf **Doba načtení zobrazení stránky** naplnil **telemetrií na straně klienta**, na každou stránku, kterou chcete sledovat, přidejte následující skript:

   ```HTML
   <!-- 
   To collect user behavior analytics tools about your application, 
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

5. Na levé straně vyberte **metriky**. Pomocí Průzkumníka metrik můžete prozkoumat stav a využití vašeho prostředku. Můžete vybrat možnost **Přidat nový graf** a vytvořit další vlastní zobrazení nebo vybrat **Upravit** a upravit existující typy grafů, výšku, paletu barev, seskupení a metriky. Můžete například vytvořit graf, který zobrazuje průměrnou dobu načítání stránek prohlížeče, a to tak, že v rozevíracím seznamu metriky a průměr z agregace vyberete "čas načtení stránky". Další informace o Azure Průzkumník metrik najdete [v článku Začínáme s azure Průzkumník metrik](../../azure-monitor/platform/metrics-getting-started.md).

   ![Graf metrik serveru](./media/nodejs-quick-start/8metrics.png)

Další informace o monitorování Node.js najdete v [další dokumentaci k Node.js pro App Insights](../../azure-monitor/app/nodejs.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s testováním hotovi, můžete odstranit skupinu prostředků a všechny související prostředky. Provedete to podle následujících kroků.

1. V nabídce na levé straně Azure Portal vyberte **skupiny prostředků** a pak vyberte **myResourceGroup**.
2. Na stránce skupiny prostředků vyberte **Odstranit**, do textového pole zadejte **myResourceGroup** a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyhledání a diagnostika potíží s výkonem](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
