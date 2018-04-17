---
title: Simulace vysokofrekvenčního obchodování pomocí Azure Stream Analytics
description: Provádění trénování a vyhodnocování modelu lineární regrese v úloze Azure Stream Analytics
services: stream-analytics
author: zhongc
ms.author: zhongc
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: get-started-article
ms.date: 11/05/2017
ms.openlocfilehash: cf04cf92f204b89c0641a23ba38b05dbcad409b2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="high-frequency-trading-simulation-with-stream-analytics"></a>Simulace vysokofrekvenčního obchodování pomocí Stream Analytics
Kombinace jazyka SQL s uživatelem definovanými funkcemi (UDF) a uživatelem definovanými agregacemi (UDA) JavaScriptu v Azure Stream Analytics umožňuje uživatelům provádět pokročilé analýzy. Mezi pokročilé analýzy může patřit online trénování a vyhodnocování machine learningu a také simulace stavových procesů. Tento článek popisuje, jak provádět lineární regresi v úloze Azure Stream Analytics, která provádí průběžné trénování a vyhodnocování ve scénáři vysokofrekvenčního obchodování.

## <a name="high-frequency-trading"></a>Vysokofrekvenční obchodování
Logický tok vysokofrekvenčního obchodování spočívá v:
1. Získávání nabídek z burzy cenných papírů v reálném čase.
2. Sestavení prediktivního modelu z těchto nabídek, abychom mohli předvídat pohyb cen.
3. Zadávání pokynů k nákupu nebo prodeji, abychom na úspěšné predikci pohybu cen vydělali. 

Ve výsledku potřebujeme:
* Informační kanál nabídek v reálném čase.
* Prediktivní model schopný pracovat s nabídkami v reálném čase.
* Simulaci obchodování ukazující zisk nebo ztrátu algoritmu obchodování.

### <a name="real-time-quote-feed"></a>Informační kanál nabídek v reálném čase
IEX nabízí bezplatně [nabídky kupních a prodejních cen v reálném čase](https://iextrading.com/developer/docs/#websockets) pomocí socket.io. Je možné napsat jednoduchý konzolový program, který přijímá nabídky v reálném čase a nabízí je jako zdroj dat do služby Azure Event Hubs. Následující kód představuje kostru programu. Kód pro zkrácení vynechává zpracování chyb. Do svého projektu také musíte zahrnout balíčky NuGet SocketIoClientDotNet a WindowsAzure.ServiceBus.


    using Quobject.SocketIoClientDotNet.Client;
    using Microsoft.ServiceBus.Messaging;

    var symbols = "msft,fb,amzn,goog";
    var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
    var socket = IO.Socket("https://ws-api.iextrading.com/1.0/tops");

    socket.On(Socket.EVENT_MESSAGE, (message) =>
    {
        eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes((string)message)));
    });

    socket.On(Socket.EVENT_CONNECT, () =>
    {
        socket.Emit("subscribe", symbols);
    });

Tady je několik vygenerovaných ukázkových událostí:

    {"symbol":"MSFT","marketPercent":0.03246,"bidSize":100,"bidPrice":74.8,"askSize":300,"askPrice":74.83,"volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,"lastUpdated":1506953357170,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"GOOG","marketPercent":0.04825,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,"volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,"lastUpdated":1506953357633,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"MSFT","marketPercent":0.03244,"bidSize":100,"bidPrice":74.8,"askSize":100,"askPrice":74.83,"volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,"lastUpdated":1506953359118,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"FB","marketPercent":0.01211,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.67,"volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,"lastUpdated":1506953359641,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"GOOG","marketPercent":0.04795,"bidSize":100,"bidPrice":959.19,"askSize":0,"askPrice":0,"volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,"lastUpdated":1506953360949,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"FB","marketPercent":0.0121,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.7,"volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,"lastUpdated":1506953362205,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"GOOG","marketPercent":0.04795,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,"volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,"lastUpdated":1506953362629,"sector":"softwareservices","securityType":"commonstock"}

>[!NOTE]
>Časové razítko události je hodnota **lastUpdated** v unixovém čase.

### <a name="predictive-model-for-high-frequency-trading"></a>Prediktivní model pro vysokofrekvenční obchodování
Pro účely ukázky používáme lineární model, který ve [své studii](http://eprints.maths.ox.ac.uk/1895/1/Darryl%20Shen%20%28for%20archive%29.pdf) popsal Darryl Shen.

VOI (Volume Order Imbalance) je funkce aktuální kupní/prodejní ceny a objemu a kupní/prodejní ceny a objemu z posledního impulzu. Studie identifikuje korelaci mezi funkcí VOI a budoucím pohybem ceny. Sestavuje lineární model mezi posledními 5 hodnotami VOI a změnou ceny v dalších 10 impulzech. Model se trénuje pomocí dat z předchozího dne a lineární regrese. 

Natrénovaný model se pak použije k předvídání změny cen u nabídek v aktuálním obchodním dni v reálném čase. Pokud je předpokládaná změna ceny dostatečně velká, uzavře se obchod. V závislosti na nastavení prahové hodnoty se dají během trénovacího dne očekávat tisíce obchodů jedné akcie.

![Definice funkce VOI](./media/stream-analytics-high-frequency-trading/voi-formula.png)

Teď vyjádříme operace trénování a predikce v úloze Azure Stream Analytics.

Nejprve se vyčistí vstupy. Unixový čas se prostřednictvím funkce **DATEADD** převede na datový typ datetime. Funkce **TRY_CAST** slouží k vynucení datových typů, aniž by došlo k selhání dotazu. Vstupní pole je vždy vhodné přetypovat na očekávané datové typy, aby nedocházelo k neočekávanému chování při manipulaci s poli nebo jejich porovnávání.

    WITH
    typeconvertedquotes AS (
        /* convert all input fields to proper types */
        SELECT
            System.Timestamp AS lastUpdated,
            symbol,
            DATEADD(millisecond, CAST(lastSaleTime as bigint), '1970-01-01T00:00:00Z') AS lastSaleTime,
            TRY_CAST(bidSize as bigint) AS bidSize,
            TRY_CAST(bidPrice as float) AS bidPrice,
            TRY_CAST(askSize as bigint) AS askSize,
            TRY_CAST(askPrice as float) AS askPrice,
            TRY_CAST(volume as bigint) AS volume,
            TRY_CAST(lastSaleSize as bigint) AS lastSaleSize,
            TRY_CAST(lastSalePrice as float) AS lastSalePrice
        FROM quotes TIMESTAMP BY DATEADD(millisecond, CAST(lastUpdated as bigint), '1970-01-01T00:00:00Z')
    ),
    timefilteredquotes AS (
        /* filter between 7am and 1pm PST, 14:00 to 20:00 UTC */
        /* clean up invalid data points */
        SELECT * FROM typeconvertedquotes
        WHERE DATEPART(hour, lastUpdated) >= 14 AND DATEPART(hour, lastUpdated) < 20 AND bidSize > 0 AND askSize > 0 AND bidPrice > 0 AND askPrice > 0
    ),

Dále použijeme funkci **LAG** k získání hodnot z posledního impulzu. Jedna hodina pro hodnotu **LIMIT DURATION** je zvolena náhodně. S ohledem na frekvenci nabídek se dá předpokládat, že předchozí impulz najdete v poslední hodině.  

    shiftedquotes AS (
        /* get previous bid/ask price and size in order to calculate VOI */
        SELECT
            symbol,
            (bidPrice + askPrice)/2 AS midPrice,
            bidPrice,
            bidSize,
            askPrice,
            askSize,
            LAG(bidPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidPricePrev,
            LAG(bidSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidSizePrev,
            LAG(askPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askPricePrev,
            LAG(askSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askSizePrev
        FROM timefilteredquotes
    ),

Následně můžeme vypočítat hodnotu VOI. Pro jistotu vyfiltrujeme hodnoty null, pokud předchozí impulz neexistuje.

    currentPriceAndVOI AS (
        /* calculate VOI */
        SELECT
            symbol,
            midPrice,
            (CASE WHEN (bidPrice < bidPricePrev) THEN 0
                ELSE (CASE WHEN (bidPrice = bidPricePrev) THEN (bidSize - bidSizePrev) ELSE bidSize END)
             END) -
            (CASE WHEN (askPrice < askPricePrev) THEN askSize
                ELSE (CASE WHEN (askPrice = askPricePrev) THEN (askSize - askSizePrev) ELSE 0 END)
             END) AS VOI
        FROM shiftedquotes
        WHERE
            bidPrice IS NOT NULL AND
            bidSize IS NOT NULL AND
            askPrice IS NOT NULL AND
            askSize IS NOT NULL AND
            bidPricePrev IS NOT NULL AND
            bidSizePrev IS NOT NULL AND
            askPricePrev IS NOT NULL AND
            askSizePrev IS NOT NULL
    ),

Teď znovu použijeme funkci **LAG** k vytvoření sekvence se 2 po sobě jdoucími hodnotami VOI následovanými 10 po sobě jdoucími hodnotami střední ceny.

    shiftedPriceAndShiftedVOI AS (
        /* get 10 future prices and 2 previous VOIs */
        SELECT
            symbol,
            midPrice AS midPrice10,
            LAG(midPrice, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice9,
            LAG(midPrice, 2) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice8,
            LAG(midPrice, 3) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice7,
            LAG(midPrice, 4) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice6,
            LAG(midPrice, 5) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice5,
            LAG(midPrice, 6) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice4,
            LAG(midPrice, 7) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice3,
            LAG(midPrice, 8) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice2,
            LAG(midPrice, 9) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice1,
            LAG(midPrice, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice,
            LAG(VOI, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI1,
            LAG(VOI, 11) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
        FROM currentPriceAndVOI
    ),

Následně data strukturujeme do vstupů pro lineární model se dvěma proměnnými. Znovu vyfiltrujeme události, pro které nemáme všechna data.

    modelInput AS (
        /* create feature vector, x being VOI, y being delta price */
        SELECT
            symbol,
            (midPrice1 + midPrice2 + midPrice3 + midPrice4 + midPrice5 + midPrice6 + midPrice7 + midPrice8 + midPrice9 + midPrice10)/10.0 - midPrice AS y,
            VOI1 AS x1,
            VOI2 AS x2
        FROM shiftedPriceAndShiftedVOI
        WHERE
            midPrice1 IS NOT NULL AND
            midPrice2 IS NOT NULL AND
            midPrice3 IS NOT NULL AND
            midPrice4 IS NOT NULL AND
            midPrice5 IS NOT NULL AND
            midPrice6 IS NOT NULL AND
            midPrice7 IS NOT NULL AND
            midPrice8 IS NOT NULL AND
            midPrice9 IS NOT NULL AND
            midPrice10 IS NOT NULL AND
            midPrice IS NOT NULL AND
            VOI1 IS NOT NULL AND
            VOI2 IS NOT NULL
    ),

Vzhledem k tomu, že Azure Stream Analytics neobsahuje integrovanou funkci lineární regrese, koeficienty pro lineární model vypočítáme pomocí agregací **SUM** a **AVG**.

![Vzorec lineární regrese](./media/stream-analytics-high-frequency-trading/linear-regression-formula.png)

    modelagg AS (
        /* get aggregates for linear regression calculation,
         http://faculty.cas.usf.edu/mbrannick/regression/Reg2IV.html */
        SELECT
            symbol,
            SUM(x1 * x1) AS x1x1,
            SUM(x2 * x2) AS x2x2,
            SUM(x1 * y) AS x1y,
            SUM(x2 * y) AS x2y,
            SUM(x1 * x2) AS x1x2,
            AVG(y) AS avgy,
            AVG(x1) AS avgx1,
            AVG(x2) AS avgx2
        FROM modelInput
        GROUP BY symbol, TumblingWindow(hour, 24, -4)
    ),
    modelparambs AS (
        /* calculate b1 and b2 for the linear model */
        SELECT
            symbol,
            (x2x2 * x1y - x1x2 * x2y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b1,
            (x1x1 * x2y - x1x2 * x1y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b2,
            avgy,
            avgx1,
            avgx2
        FROM modelagg
    ),
    model AS (
        /* calculate a for the linear model */
        SELECT
            symbol,
            avgy - b1 * avgx1 - b2 * avgx2 AS a,
            b1,
            b2
        FROM modelparambs
    ),

Abychom pro vyhodnocení aktuálních událostí mohli použít model z předchozího dne, chceme nabídky spojit s modelem. Ale místo použití operátoru **JOIN** sjednotíme události modelu a události nabídek pomocí operátoru **UNION**. Potom pomocí funkce **LAG** spárujeme události s modelem z předchozího dne, abychom získali přesně jednu shodu. Kvůli víkendu se musíme podívat o tři dny zpět. Kdybychom použili přímé spojení operátorem **JOIN**, dostali bychom pro každou událost nabídky tři modely.

    shiftedVOI AS (
        /* get two consecutive VOIs */
        SELECT
            symbol,
            midPrice,
            VOI AS VOI1,        
            LAG(VOI, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
        FROM currentPriceAndVOI
    ),
    VOIAndModel AS (
        /* combine VOIs and models */
        SELECT
            'voi' AS type,
            symbol,
            midPrice,
            VOI1,
            VOI2,
            0.0 AS a,
            0.0 AS b1,
            0.0 AS b2
        FROM shiftedVOI
        UNION
        SELECT
            'model' AS type,
            symbol,
            0.0 AS midPrice,
            0 AS VOI1,
            0 AS VOI2,
            a,
            b1,
            b2
        FROM model
    ),
    VOIANDModelJoined AS (
        /* match VOIs with the latest model within 3 days (72 hours, to take the weekend into account) */
        SELECT
            symbol,
            midPrice,
            VOI1 as x1,
            VOI2 as x2,
            LAG(a, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS a,
            LAG(b1, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b1,
            LAG(b2, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b2
        FROM VOIAndModel
        WHERE type = 'voi'
    ),

Teď můžeme provést předpovědi a na základě modelu vygenerovat signály pro nákup nebo prodej s prahovou hodnotou 0,02. Hodnota obchodu 10 znamená nákup. Hodnota obchodu −10 znamená prodej.

    prediction AS (
        /* make prediction if there is a model */
        SELECT
            symbol,
            midPrice,
            a + b1 * x1 + b2 * x2 AS efpc
        FROM VOIANDModelJoined
        WHERE
            a IS NOT NULL AND
            b1 IS NOT NULL AND
            b2 IS NOT NULL AND
            x1 IS NOT NULL AND
            x2 IS NOT NULL
    ),
    tradeSignal AS (
        /* generate buy/sell signals */
        SELECT
            DateAdd(hour, -7, System.Timestamp) AS time,
            symbol,     
            midPrice,
            efpc,
            CASE WHEN (efpc > 0.02) THEN 10 ELSE (CASE WHEN (efpc < -0.02) THEN -10 ELSE 0 END) END AS trade,
            DATETIMEFROMPARTS(DATEPART(year, System.Timestamp), DATEPART(month, System.Timestamp), DATEPART(day, System.Timestamp), 0, 0, 0, 0) as date
        FROM prediction
    ),

### <a name="trading-simulation"></a>Simulace obchodování
Jakmile budeme mít obchodní signály, chceme otestovat efektivitu této strategie obchodování, aniž bychom skutečně obchodovali. 

Tento test provedeme pomocí UDA se skákajícím oknem, kdy se skok provádí každou minutu. Další seskupení podle data a klauzule Having umožňují zahrnout do okna pouze účty pro události, které patří do stejného dne. V případě skákajícího okna trvajícího dva dny se pomocí seskupení podle data operátorem **GROUP BY** toto seskupení rozdělí na předchozí den a aktuální den. Klauzule **HAVING** vyfiltruje okna, která končí aktuálním dnem, ale byla seskupená předchozí den.

    simulation AS
    (
        /* perform trade simulation for the past 7 hours to cover an entire trading day, and generate output every minute */
        SELECT
            DateAdd(hour, -7, System.Timestamp) AS time,
            symbol,
            date,
            uda.TradeSimulation(tradeSignal) AS s
        FROM tradeSignal
        GROUP BY HoppingWindow(minute, 420, 1), symbol, date
        Having DateDiff(day, date, time) < 1 AND DATEPART(hour, time) < 13
    )

UDA JavaScriptu ve funkci `init` inicializuje všechny průběžné součty, při každém přidání události do okna vypočítá přechod stavu a na konci okna vrátí výsledky simulace. Obecný průběh obchodování je:

- Nákup akcií, když se obdrží signál k nákupu a žádné akcie nedržíme.
- Prodej akcií, když se obdrží signál k prodeji a nějaké akcie držíme.
- Otevření krátké pozice, pokud žádné akcie nedržíme. 

Pokud je otevřená krátká pozice a obdrží se signál k nákupu, provedeme nákup a uzavřeme pozici. V této simulaci nikdy nedržíme ani nemáme krátkou pozici na 10 stejných akcií. Transakční náklady jsou pevně nastavené na 8 USD.


    function main() {
        var TRADE_COST = 8.0;
        var SHARES = 10;

        this.init = function () {
            this.own = false;
            this.pos = 0;
            this.pnl = 0.0;
            this.tradeCosts = 0.0;
            this.buyPrice = 0.0;
            this.sellPrice = 0.0;
            this.buySize = 0;
            this.sellSize = 0;
            this.buyTotal = 0.0;
            this.sellTotal = 0.0;
        }

        this.accumulate = function (tradeSignal, timestamp) {

            if(!this.own && tradeSignal.trade == 10) {
              // Buy to open
              this.own = true;
              this.pos = 1;
              this.buyPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.buySize += SHARES;
              this.buyTotal += SHARES * tradeSignal.midprice;
            } else if(!this.own && tradeSignal.trade == -10) {
              // Sell to open
              this.own = true;
              this.pos = -1
              this.sellPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.sellSize += SHARES;
              this.sellTotal += SHARES * tradeSignal.midprice;
            } else if(this.own && this.pos == 1 && tradeSignal.trade == -10) {
              // Sell to close
              this.own = false;
              this.pos = 0;
              this.sellPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
              this.sellSize += SHARES;
              this.sellTotal += SHARES * tradeSignal.midprice;

              // Sell to open
              this.own = true;
              this.pos = -1;
              this.sellPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.sellSize += SHARES;        
              this.sellTotal += SHARES * tradeSignal.midprice;
            } else if(this.own && this.pos == -1 && tradeSignal.trade == 10) {
              // Buy to close
              this.own = false;
              this.pos = 0;
              this.buyPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
              this.buySize += SHARES;
              this.buyTotal += SHARES * tradeSignal.midprice;

              // Buy to open
              this.own = true;
              this.pos = 1;
              this.buyPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.buySize += SHARES;         
              this.buyTotal += SHARES * tradeSignal.midprice;
            }
        }

        this.computeResult = function () {
            var result = {
                "pnl": this.pnl,
                "buySize": this.buySize,
                "sellSize": this.sellSize,
                "buyTotal": this.buyTotal,
                "sellTotal": this.sellTotal,
                "tradeCost": this.tradeCost
                };
            return result;
        }
    }

Nakonec odešleme výstup na řídicí panel Power BI, který zobrazí vizualizaci.

    SELECT * INTO tradeSignalDashboard FROM tradeSignal /* output tradeSignal to PBI */
    SELECT
        symbol,
        time,
        date,
        TRY_CAST(s.pnl as float) AS pnl,
        TRY_CAST(s.buySize as bigint) AS buySize,
        TRY_CAST(s.sellSize as bigint) AS sellSize,
        TRY_CAST(s.buyTotal as float) AS buyTotal,
        TRY_CAST(s.sellTotal as float) AS sellTotal
        INTO pnlDashboard
    FROM simulation /* output trade simulation to PBI */

![Obchody](./media/stream-analytics-high-frequency-trading/trades.png)

![Zisky a ztráty](./media/stream-analytics-high-frequency-trading/pnl.png)


## <a name="summary"></a>Souhrn
Realistický model vysokofrekvenčního obchodování můžeme implementovat pomocí mírně složitého dotazu v Azure Stream Analytics. Vzhledem k chybějící integrované funkci lineární regrese musíme model zjednodušit a místo pěti vstupních proměnných použít dvě. Odhodlaný uživatel však možná dokáže jako UDA JavaScriptu implementovat i sofistikovanější algoritmy vyšších dimenzí. 

Za zmínku stojí, že většinu dotazu, kromě UDA JavaScriptu, je možné testovat a ladit v sadě Visual Studio prostřednictvím [nástrojů Azure Stream Analytics pro Visual Studio](stream-analytics-tools-for-visual-studio.md). Od napsání počátečního dotazu strávil autor testováním a laděním dotazu v sadě Visual Studio méně než 30 minut. 

UDA v současné době není možné ladit v sadě Visual Studio. Pracujeme na povolení této funkce s možností procházet kód JavaScriptu. Navíc si všimněte, že názvy polí přicházejících do UDA jsou malými písmeny. Během testování dotazu to nebylo zřejmé chování. Díky úrovni kompatibility Azure Stream Analytics 1.1 však zachováváme velikosti písmen v názvech polí, takže je chování přirozenější.

Doufám, že tento článek poslouží jako inspirace pro všechny uživatele Azure Stream Analytics, kteří můžou naši službu využít k průběžnému provádění pokročilých analýz v reálném čase. Podělte se s námi o svůj názor, usnadníte nám tím implementaci dotazů pro scénáře pokročilých analýz.
