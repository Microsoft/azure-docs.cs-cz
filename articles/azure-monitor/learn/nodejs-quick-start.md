---
title: 'Rychlý Start: monitorování pomocí Application Insights Azure'
description: Poskytuje pokyny pro rychlé nastavení webové aplikace Node. js pro monitorování pomocí Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/12/2019
ms.service: application-insights
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 79bd0ce90c76f95ce12662e0d496b481382e805a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177683"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>Rychlý Start: spuštění sledování webové aplikace v Node. js pomocí Azure Application Insights

Tento rychlý Start vás provede přidáním verze 0,22 Application Insights SDK pro Node. js do existující webové aplikace Node. js.

Pomocí Azure Application Insights můžete snadno monitorovat webovou aplikaci, aby byla dostupná, výkon a využití. V aplikaci můžete také rychle identifikovat a diagnostikovat chyby bez čekání na jejich nahlášení uživatele. V rámci verze 0,20 SDK můžete monitorovat běžné balíčky třetích stran, včetně MongoDB, MySQL a Redis.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu:

- Potřebujete předplatné Azure a stávající webovou aplikaci Node. js.

Pokud nemáte webovou aplikaci Node. js, můžete ji vytvořit pomocí [rychlého startu vytvoření webové aplikace Node. js](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlaste se k Azure Portal

Přihlaste se k [Azure Portal](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Povolit Application Insights

Application Insights můžou shromažďovat data telemetrie z jakékoli aplikace připojené k Internetu bez ohledu na to, jestli běží místně nebo v cloudu. Chcete-li spustit zobrazení těchto dat, použijte následující postup.

1. Vyberte **vytvořit prostředek** > **vývojářské nástroje** > **Application Insights**.

   ![Přidání prostředku Application Insights](./media/nodejs-quick-start/1createresourseappinsights.png)

   > [!NOTE]
   >Pokud Application Insights prostředek vytvoříte poprvé, můžete si o tom přečíst další informace v dokumentu [vytvoření prostředku Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) .

   Zobrazí se stránka konfigurace; k vyplnění vstupních polí použijte následující tabulku. 

    | Nastavení        | Hodnota           | Popis  |
   | ------------- |:-------------|:-----|
   | **Jméno**      | Globálně jedinečná hodnota | Název, který identifikuje monitorovanou aplikaci |
   | **Typ aplikace** | Aplikace Node. js | Typ aplikace, kterou sledujete |
   | **Poloha** | Východní USA | Vyberte umístění poblíž vaší aplikace nebo poblíž místa, kde je vaše aplikace hostovaná. |

2. Vyberte **vytvořit**.

## <a name="configure-app-insights-sdk"></a>Konfigurace sady App Insights SDK

1. Vyberte **Přehled** a zkopírujte **klíč instrumentace**vaší aplikace.

   ![Nový formulář prostředku App Insights](./media/nodejs-quick-start/3key.png)

2. Přidejte sadu Application Insights SDK pro Node. js do aplikace. Z kořenové složky vaší aplikace spusťte:

   ```bash
   npm install applicationinsights --save
   ```

3. Upravte první soubor. js aplikace a přidejte dva řádky níže do nejvyšší části skriptu. Pokud používáte [aplikaci pro rychlý Start Node. js](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs), upravili byste soubor index. js. Nahraďte &lt;instrumentation_key @ no__t-1 pomocí klíče instrumentace vaší aplikace. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Restartujte aplikaci.

> [!NOTE]
> Zabere 3-5 minut, než se data začnou zobrazovat na portálu. Pokud se jedná o testovací aplikaci s nízkým provozem, pamatujte, že většina metrik se zachycuje jenom v případě, že dojde k aktivním požadavkům nebo operacím.

## <a name="start-monitoring-in-the-azure-portal"></a>Spustit monitorování v Azure Portal

1. Teď můžete znovu otevřít stránku **přehled** Application Insights v Azure Portal, kde jste získali klíč instrumentace, abyste si zobrazili podrobnosti o aktuálně spuštěné aplikaci.

   ![Nabídka přehledu Application Insights](./media/nodejs-quick-start/4overview.png)

2. Vyberte možnost **Mapa aplikace** pro vizuální rozložení vztahů závislosti mezi komponentami vaší aplikace. Každá součást zobrazuje klíčové ukazatele výkonu, jako je například zatížení, výkon, chyby a výstrahy.

   ![Mapa aplikace](./media/nodejs-quick-start/5appmap.png)

3. Vyberte ikonu **analýzy aplikace** @no__t 1Application – ikona mapy @ no__t-2 **zobrazení v části analýza**.  Otevře se **Application Insights Analytics**, která poskytuje bohatý dotazovací jazyk pro analýzu všech dat shromážděných pomocí Application Insights. V takovém případě se pro vás vygeneruje dotaz, který vykreslí počet požadavků jako graf. Můžete napsat vlastní dotazy a analyzovat ostatní data.

   ![Graf analýz požadavků uživatelů v časovém intervalu](./media/nodejs-quick-start/6analytics.png)

4. Vraťte se na stránku **Přehled** a Projděte si grafy klíčových ukazatelů výkonu.  Tento řídicí panel poskytuje statistické údaje o stavu vaší aplikace, včetně počtu příchozích požadavků, doby trvání těchto požadavků a všech selhání, ke kterým dojde.

   ![Grafy pro časovou osu přehledu stavu](./media/nodejs-quick-start/7kpidashboards.png)

   Pokud chcete povolit, aby se v grafu pro **zobrazení stránky** s daty telemetrie na straně klienta naplnila data **telemetrie na straně klienta** , přidejte tento skript na každou stránku, kterou chcete sledovat:

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

Další informace o monitorování Node. js najdete v [další dokumentaci k Node. js pro App Insights](../../azure-monitor/app/nodejs.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s testováním hotovi, můžete odstranit skupinu prostředků a všechny související prostředky. Provedete to podle následujících kroků.

1. V nabídce na levé straně Azure Portal vyberte **skupiny prostředků** a pak vyberte **myResourceGroup**.
2. Na stránce skupiny prostředků vyberte **Odstranit**, do textového pole zadejte **myResourceGroup** a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vyhledání a Diagnostika problémů s výkonem](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
