---
title: 'Rychlý Start: monitorování Node.js pomocí Azure Monitor Application Insights'
description: Poskytuje pokyny pro rychlé nastavení Node.js webové aplikace pro monitorování pomocí Azure Monitor Application Insights
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/12/2019
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: 550097272f90a2f7fa06af7f06810fd3fa0ed94f
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173889"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>Rychlý Start: zahájení monitorování webové aplikace v Node.js s využitím Azure Application Insights

V tomto rychlém startu přidáte sadu Application Insights SDK verze 0,22 pro Node.js do existující Node.js webové aplikace.

Služba Azure Application Insights umožňuje monitorovat webové aplikace z hlediska dostupnosti, výkonu a využití. Můžete také rychle identifikovat a diagnostikovat chyby ve vaší aplikaci a nečekat na to, až je nahlásí uživatelé. Pomocí sady SDK verze 0.20 a novější můžete monitorovat běžné balíčky třetích stran, včetně MongoDB, MySQL a Redis.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Funkční aplikace Node.js.

## <a name="enable-application-insights"></a>Povolení Application Insights

Application Insights můžou shromažďovat data telemetrie z jakékoli aplikace připojené k Internetu, ať už běží místně nebo v cloudu. Pokud chcete tato data začít zobrazovat, použijte následující kroky.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

2. Vyberte **vytvořit prostředek pro**  >  **vývojáře**  >  **Application Insights**.

   ![Přidání prostředku Azure Application Insights](./media/nodejs-quick-start/azure-app-insights-create-resource.png)

   > [!NOTE]
   >Pokud Application Insights prostředek vytvoříte poprvé, můžete si o tom přečíst další informace v dokumentu [vytvoření prostředku Application Insights](../app/create-new-resource.md) .

   Zobrazí se stránka konfigurace; k vyplnění vstupních polí použijte následující tabulku. 

    | Nastavení        | Hodnota           | Popis  |
   | ------------- |:-------------|:-----|
   | **Název**      | Globálně jedinečná hodnota | Název, který identifikuje aplikaci, kterou sledujete |
   | **Skupina prostředků**     | myResourceGroup      | Název nové skupiny prostředků, která bude hostovat data AppInsights. Můžete vytvořit novou skupinu prostředků nebo použít existující. |
   | **Umístění** | East US | Vyberte umístění ve vaší blízkosti nebo v blízkosti místa, kde se vaše aplikace hostuje. |

3. Vyberte **Vytvořit**.

## <a name="configure-appinsights-sdk"></a>Konfigurace sady AppInsights SDK

1. Vyberte **Přehled** a zkopírujte **klíč instrumentace**vaší aplikace.

   ![Zobrazit Application Insights klíč instrumentace](./media/nodejs-quick-start/azure-app-insights-instrumentation-key.png)

2. Přidejte sadu Application Insights pro Node.js do vaší aplikace. Z kořenové složky vaší aplikace spusťte:

   ```bash
   npm install applicationinsights --save
   ```

3. Upravte první soubor *. js* aplikace a přidejte dva řádky níže do nejvyšší části skriptu. Pokud používáte [ aplikaciNode.js pro rychlý Start](../../app-service/quickstart-nodejs.md), upravili byste soubor *index.js* . Nahraďte `<instrumentation_key>` klíčem instrumentace vašeho přehledu vaší aplikace. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Restartujte aplikaci.

> [!NOTE]
> Trvá 3–5 minut, než se na portálu začnou zobrazovat data. Pokud používáte testovací aplikaci s nízkým provozem, nezapomínejte, že většina metrik se zachycuje, jenom když dochází k nějakým aktivním požadavkům nebo operacím.

## <a name="start-monitoring-in-the-azure-portal"></a>Zahájení monitorování na webu Azure Portal

1. Teď můžete znovu otevřít stránku **Přehled** služby Application Insights na webu Azure Portal, kde jste získali svůj instrumentační klíč, a zobrazit podrobné informace o aktuálně spuštěné aplikaci.

   ![Nabídka přehledu Application Insights](./media/nodejs-quick-start/azure-app-insights-overview-menu.png)

2. Vyberte možnost **Mapa aplikace** pro vizuální rozložení vztahů závislosti mezi komponentami vaší aplikace. U každé komponenty se zobrazují klíčové ukazatele výkonu, jako je zatížení, výkon, selhání a upozornění.

   ![Application Insights mapa aplikace](./media/nodejs-quick-start/azure-app-insights-application-map.png)

3. Vyberte ikonu **Analýza** aplikace ikona ![ Mapa aplikace ](./media/nodejs-quick-start/azure-app-insights-analytics-icon.png) **Zobrazit v části analýzy**.  Tato akce otevře **Application Insights Analytics**, která poskytuje bohatý dotazovací jazyk pro analýzu všech dat shromážděných pomocí Application Insights. V tomto případě jsme za vás vytvořili dotaz, který vykreslí počet požadavků ve formě grafu. Můžete psát své vlastní dotazy pro analýzu dalších dat.

   ![Grafy Application Insights Analytics](./media/nodejs-quick-start/azure-app-insights-analytics-queries.png)

4. Vraťte se na stránku **Přehled** a prozkoumejte grafy klíčových ukazatelů výkonu.  Tento řídicí panel poskytuje statistické údaje o stavu vaší aplikace, včetně počtu příchozích požadavků, doby jejich trvání a všech chyb, ke kterým došlo.

   ![Grafy časové osy přehledu Application Insightsho stavu](./media/nodejs-quick-start/azure-app-insights-health-overview.png)

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

5. Na levé straně vyberte **Metriky**. Pomocí Průzkumníka metrik můžete prozkoumat stav a využití vašeho prostředku. Můžete vybrat možnost **Přidat nový graf** a vytvořit další vlastní zobrazení nebo vybrat **Upravit** a upravit existující typy grafů, výšku, paletu barev, seskupení a metriky. Můžete například vytvořit graf, který zobrazuje průměrnou dobu načítání stránek prohlížeče, a to tak, že v rozevíracím seznamu metriky a průměr z agregace vyberete "čas načtení stránky". Další informace o službě Azure Průzkumník metrik najdete v článku [Začínáme s azure Průzkumník metrik](../platform/metrics-getting-started.md).

   ![Graf metrik Application Insights serveru](./media/nodejs-quick-start/azure-app-insights-server-metrics.png)

Další informace o monitorování Node.js najdete v [další dokumentaci k Node.js AppInsights](../app/nodejs.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s testováním hotovi, můžete odstranit skupinu prostředků a všechny související prostředky. Provedete to podle následujících kroků.

> [!NOTE]
> Pokud jste použili existující skupinu prostředků, následující pokyny nebudou fungovat a bude potřeba jenom odstranit jednotlivé prostředky Application Insights. Mějte na paměti, že kdykoli odstraníte skupinu prostředků, odstraní se všechny základního prostředky, které jsou členy této skupiny.

1. V nabídce na levé straně Azure Portal vyberte **skupiny prostředků** a pak vyberte **myResourceGroup**.
2. Na stránce skupiny prostředků vyberte **Odstranit**, do textového pole zadejte **myResourceGroup** a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyhledání a diagnostika potíží s výkonem](../log-query/log-query-overview.md)

