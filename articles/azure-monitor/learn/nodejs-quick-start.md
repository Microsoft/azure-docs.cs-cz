---
title: 'Úvodní příručka: Monitorování souboru Node.js pomocí přehledů aplikací Azure Monitor'
description: Obsahuje pokyny k rychlému nastavení webové aplikace Node.js pro monitorování pomocí přehledů aplikací Azure Monitor.
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/12/2019
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 694d2ae529202223869fcbb2a084e32bccaedbf1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77660219"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>Úvodní příručka: Začněte sledovat webovou aplikaci Node.js pomocí Azure Application Insights

V tomto rychlém startu přidáte sady Application Insights SDK verze 0.22 pro Node.js do existující webové aplikace Node.js.

Služba Azure Application Insights umožňuje monitorovat webové aplikace z hlediska dostupnosti, výkonu a využití. Můžete také rychle identifikovat a diagnostikovat chyby ve vaší aplikaci a nečekat na to, až je nahlásí uživatelé. Pomocí sady SDK verze 0.20 a novější můžete monitorovat běžné balíčky třetích stran, včetně MongoDB, MySQL a Redis.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Funkční aplikace Node.js.

## <a name="enable-application-insights"></a>Povolení Application Insights

Application Insights můžete shromažďovat telemetrická data z libovolné aplikace připojené k internetu, bez ohledu na to, zda běží místně nebo v cloudu. Pokud chcete tato data začít zobrazovat, použijte následující kroky.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

2. Vyberte **Vytvořit nástroje** > **Developer tools** > pro vývojáře prostředků Application**Insights**.

   ![Přidání prostředku Azure Application Insights](./media/nodejs-quick-start/azure-app-insights-create-resource.png)

   > [!NOTE]
   >Pokud je to poprvé, co vytváříte prostředek Application Insights, můžete se dozvědět více pomocí dokumentu Vytvořit dokument [o prostředku Resource Resource.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)

   Zobrazí se konfigurační stránka. Pomocí následující tabulky vyplňte vstupní pole. 

    | Nastavení        | Hodnota           | Popis  |
   | ------------- |:-------------|:-----|
   | **Název**      | Globálně jedinečná hodnota | Název, který identifikuje aplikaci, kterou sledujete |
   | **Skupina prostředků**     | myResourceGroup      | Název pro novou skupinu prostředků pro hostování dat AppInsights. Můžete vytvořit novou skupinu prostředků nebo použít existující. |
   | **Umístění** | USA – východ | Vyberte umístění ve vaší blízkosti nebo v blízkosti místa, kde se vaše aplikace hostuje. |

3. Vyberte **Vytvořit**.

## <a name="configure-appinsights-sdk"></a>Konfigurace sady AppInsights SDK

1. Vyberte **přehled** a zkopírujte **klíč instrumentace**aplikace .

   ![Zobrazit klíč instrumentace Přehledy aplikací](./media/nodejs-quick-start/azure-app-insights-instrumentation-key.png)

2. Přidejte sadu Application Insights pro Node.js do vaší aplikace. Z kořenové složky vaší aplikace spusťte:

   ```bash
   npm install applicationinsights --save
   ```

3. Upravte první soubor *JS* aplikace a přidejte dva řádky níže do nejvyšší části skriptu. Pokud používáte [aplikaci rychlého spuštění Node.js](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs), upravíte soubor *index.js.* Nahraďte `<instrumentation_key>` pomocí klíče instrumentace aplikace. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Restartujte aplikaci.

> [!NOTE]
> Trvá 3–5 minut, než se na portálu začnou zobrazovat data. Pokud používáte testovací aplikaci s nízkým provozem, nezapomínejte, že většina metrik se zachycuje, jenom když dochází k nějakým aktivním požadavkům nebo operacím.

## <a name="start-monitoring-in-the-azure-portal"></a>Zahájení monitorování na webu Azure Portal

1. Teď můžete znovu otevřít stránku **Přehled** služby Application Insights na webu Azure Portal, kde jste získali svůj instrumentační klíč, a zobrazit podrobné informace o aktuálně spuštěné aplikaci.

   ![Nabídka Přehled y přehledů aplikací](./media/nodejs-quick-start/azure-app-insights-overview-menu.png)

2. Vyberte **mapu aplikace** pro vizuální rozložení vztahů závislostí mezi součástmi aplikace. U každé komponenty se zobrazují klíčové ukazatele výkonu, jako je zatížení, výkon, selhání a upozornění.

   ![Mapa aplikací Přehledy aplikací](./media/nodejs-quick-start/azure-app-insights-application-map.png)

3. V yberte zobrazení ![ikony App](./media/nodejs-quick-start/azure-app-insights-analytics-icon.png) **Analytics** na mapě aplikace **v Analytics**.  Tato akce otevře **Application Insights Analytics**, který poskytuje rozšířený dotazovací jazyk pro analýzu všech dat shromážděných Application Insights. V tomto případě jsme za vás vytvořili dotaz, který vykreslí počet požadavků ve formě grafu. Můžete psát své vlastní dotazy pro analýzu dalších dat.

   ![Grafy analýzy přehledů aplikací](./media/nodejs-quick-start/azure-app-insights-analytics-queries.png)

4. Vraťte se na stránku **Přehled** a prozkoumejte grafy klíčových ukazatelů výkonu.  Tento řídicí panel poskytuje statistické údaje o stavu vaší aplikace, včetně počtu příchozích požadavků, doby jejich trvání a všech chyb, ke kterým došlo.

   ![Grafy časové osy Přehled přehledů aplikací](./media/nodejs-quick-start/azure-app-insights-health-overview.png)

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

5. Na levé straně vyberte **Metriky**. Pomocí průzkumníka metrik prozkoumejte stav a využití vašeho prostředku. Můžete vybrat **Přidat nový graf** a vytvořit další vlastní zobrazení nebo vybrat **Upravit** a upravit existující typy grafů, výšku, paletu barev, seskupení a metriky. Můžete například vytvořit graf, který zobrazuje průměrnou dobu načítání stránky prohlížeče výběrem možnosti "Doba načítání stránky prohlížeče" z rozbalovacího seznamu metrik a "Průmak" z agregace. Další informace o Průzkumníku metrik Azure najdete v najdete v najdete v najdete v najdete v najdete [v najdete v najdete v navěšení začínáme s Průzkumníkem metrik Azure](../../azure-monitor/platform/metrics-getting-started.md).

   ![Graf metrik serveru Application Insights Server](./media/nodejs-quick-start/azure-app-insights-server-metrics.png)

Další informace o sledování souboru Node.js naleznete v [další dokumentaci k aplikaci AppInsights Node.js](../../azure-monitor/app/nodejs.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení testování můžete odstranit skupinu prostředků a všechny související prostředky. Postupujte podle následujících kroků.

> [!NOTE]
> Pokud jste použili existující skupinu prostředků, níže uvedené pokyny nebudou fungovat a budete muset odstranit jednotlivé prostředky Application Insights. Mějte na paměti, že kdykoli odstraníte skupinu prostředků, všechny podzásobené prostředky, které jsou členy této skupiny, budou odstraněny.

1. V levé nabídce na portálu Azure vyberte **skupiny prostředků** a pak vyberte **myResourceGroup**.
2. Na stránce skupiny prostředků vyberte **Odstranit**, zadejte do textového pole **myResourceGroup** a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyhledání a diagnostika potíží s výkonem](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
