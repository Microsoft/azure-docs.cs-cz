---
title: Monitorování geografických zón a geoprostorové agregace scénáře se službou Azure Stream Analytics
description: Tento článek popisuje, jak používat Azure Stream Analytics pro monitorování geografických zón a geoprostorové agregace.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: cc301855e4cdcb8eb687e753835577399cfe72b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60789543"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>Monitorování geografických zón a geoprostorové agregace scénáře se službou Azure Stream Analytics

Azure Stream Analytics s geoprostorové funkce, slouží k vytváření aplikací pro scénáře, jako je Správa vozového parku, svézt, sdílení auta připojená k síti a sledování prostředků.

## <a name="geofencing"></a>Geofencing

Azure Stream Analytics podporuje výpočtů v reálném čase monitorování geografických zón s nízkou latencí v cloudu a modul runtime IoT Edge.

### <a name="geofencing-scenario"></a>Scénáře monitorování geografických zón

Výrobní společnost potřebuje sledovat prostředky na jejich budovy. Jsou vybavené všechna zařízení s systému GPS a chcete dostávat oznámení, pokud odebrání zařízení z určité oblasti.

Referenční data použít v tomto příkladu obsahuje informace o monitorové geografické zóny pro budovy a zařízení, které jsou povoleny v každém budovy. Mějte na paměti, že referenční data může být statická nebo pomalu měnit. Statická referenční data se používají pro tento scénář. Datový proud průběžně generuje ID zařízení a jeho aktuální pozice.

### <a name="define-geofences-in-reference-data"></a>Definování monitorovaná geografická zóna v referenčních dat

Monitorové geografické zóny lze definovat pomocí objektu GeoJSON. Pro úlohy se Kompatibilita verze 1.2 nebo vyšší, monitorovaná geografická zóna může být také definován pomocí dobře známých Text (Well-Known text) jako `NVARCHAR(MAX)`. Well-Known text je otevřít geoprostorové Consortium (OGC) standard, která se používá k reprezentování prostorová data v textovém formátu.

Geoprostorové funkce můžete použít definované monitorovaná geografická zóna a zjistěte, zda je prvek v nebo mimo mnohoúhelník konkrétní monitorové geografické zóny.

V následující tabulce je příkladem monitorové geografické zóny referenční data, která by mohla být uložena ve službě Azure blob storage nebo tabulky Azure SQL. Každé lokalitě je reprezentována mnohoúhelníku geoprostorové a každé zařízení je přidružený identifikátor povolený Web.

|SiteID|SiteName|Geofence|AllowedDeviceID|
|------|--------|--------|---------------|
|1|"Redmond Building 41"|"MNOHOÚHELNÍKU ((-122.1337357922017 47.63782998329432,-122.13373042778369 47.637634793257305,-122.13346757130023 47.637642022530954,-122.13348902897235 47.637508280806806,-122.13361777500506 47.637508280806806,-122.13361241058703 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432)) "|"B"|
|2|"Redmond Building 40"|"MNOHOÚHELNÍKU ((-122.1336154507967 47.6366745947009,-122.13361008637867 47.636483015064535,-122.13349206918201 47.636479400347675,-122.13349743360004 47.63636372927573,-122.13372810357532 47.63636372927573,-122.13373346799335 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009)) "|"A"|
|3|"Redmond Building 22"|"MNOHOÚHELNÍKU ((-122.13611660248233 47.63758544698554,-122.13635263687564 47.6374083293018,-122.13622389084293 47.63733603619712,-122.13622389084293 47.63717699101473,-122.13581619507266 47.63692757827657,-122.13559625393344 47.637046862778135,-122.13569281345798 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554)) "|"C"|

### <a name="generate-alerts-with-geofence"></a>Generovat výstrahy pomocí monitorové geografické zóny

Zařízení můžou posílat svoje ID a umístění prostřednictvím datového proudu volá se po minutách `DeviceStreamInput`. V následující tabulce je datový proud vstupu.

|ID zařízení|GeoPosition|
|--------|-----------|
|"A"|"POINT(-122.13292341559497 47.636318374032726)"|
|"B"|"POINT(-122.13338475554553 47.63743531308874)"|
|"C"|"POINT(-122.13354001095752 47.63627622505007)"|

Můžete napsat dotaz, který připojí zařízení datový proud s referenčními daty monitorové geografické zóny a vygeneruje výstrahu vždy, když zařízení je mimo povolený sestavování.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

Následující obrázek znázorňuje, monitorovaná geografická zóna. Můžete zobrazit, kde jsou zařízení v souladu vstupní data datového proudu.

![Monitorovaná geografická zóna sestavení](./media/geospatial-scenarios/building-geofences.png)

Zařízení "C" se nachází v budově 2 ID, což není povoleno podle referenční data. Toto zařízení se musí nacházet uvnitř vytváření ID 3. Spuštění této úlohy budou generovat výstrahy pro toto konkrétní porušení.

### <a name="site-with-multiple-allowed-devices"></a>Lokalita se několik povolených zařízení

Pokud lokalitu umožňuje více zařízení, můžete definovat pole ID zařízení v `AllowedDeviceID` a uživatelsky definovaná funkce lze použít na `WHERE` klauzule k ověření, pokud ID zařízení datového proudu odpovídá jakékoli ID zařízení v tomto seznamu. Další informace najdete [UDF JavaScriptu](stream-analytics-javascript-user-defined-functions.md) kurzu cloudové úlohy a [ C# UDF](stream-analytics-edge-csharp-udf.md) kurzu hraniční úlohy.

## <a name="geospatial-aggregation"></a>Geoprostorové agregace

Azure Stream Analytics podporuje agregaci v reálném čase geoprostorové s nízkou latencí v cloudu i na modul runtime IoT Edge.

### <a name="geospatial-aggregation-scenario"></a>Geoprostorové agregace scénář

Soubor cab společnost chce, aby se k sestavení aplikace v reálném čase a provede jejich souboru cab ovladače hledáte jízdní směrem k oblasti města právě se registruje zvýšené poptávky.

Společnost logické oblasti, města, ukládá jako referenční data. Každá oblast je definována RegionID, RegionName a monitorové geografické zóny.

### <a name="define-the-geofences"></a>Definování monitorovaná geografická zóna

V následující tabulce je příkladem monitorové geografické zóny referenční data, která by mohla být uložena ve službě Azure blob storage nebo tabulky Azure SQL. Geoprostorové mnohoúhelníku, který se používá ke korelaci s požadavky přicházející ze streamovaných dat je reprezentován každou oblast.

Tyto mnohoúhelníky slouží jenom jako reference a nepředstavují skutečné město fyzické nebo logické separace.

|RegionID|RegionName|Geofence|
|--------|----------|--------|
|1|"Malé"|"MNOHOÚHELNÍKU ((-74.00279525078275 40.72833625216264,-74.00547745979765 40.721929158663244,-74.00125029839018 40.71893680218994,-73.9957785919998 40.72521409075776,-73.9972377137039 40.72557184584898,-74.00279525078275 40.72833625216264) )"|
|2|"Chinatown"|"MNOHOÚHELNÍKU ((-73.99712367114876 40.71281582267133,-73.9901070123658 40.71336881907936,-73.99023575839851 40.71452359088633,-73.98976368961189 40.71554823078944,-73.99551434573982 40.717337246783735,-73.99480624255989 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005,-73.99849696216438 40.713450141693166,-73.99748845157478 40.71405192594819,-73.99712367114876 40.71281582267133)) "|
|3|"Tribeca"|"MNOHOÚHELNÍKU ((-74.01091641815208 40.72583120006787,-74.01338405044578 40.71436586362705,-74.01370591552757 40.713617702123415,-74.00862044723533 40.711308107057235,-74.00194711120628 40.7194238654018,-74.01091641815208 40.72583120006787))"|

### <a name="aggregate-data-over-a-window-of-time"></a>Agregovaná data přes časové okno

Následující tabulka obsahuje streamovaná data "lyžovačku."

|ID uživatele|FromLocation|ToLocation|TripRequestedTime|
|------|------------|----------|-----------------|
|"A"|"POINT(-74.00726861389182 40.71610611981975)"|"POINT(-73.98615095917779 40.703107386025835)"|"2019-03-12T07:00:00Z"|
|"B"|"POINT(-74.00249841021645 40.723827238895666)"|"POINT(-74.01160699942085 40.71378884930115)"|"2019-03-12T07:01:00Z"|
|"C"|"POINT(-73.99680120565864 40.716439898624024)"|"POINT(-73.98289663412544 40.72582343969828)"|"2019-03-12T07:02:00Z"|
|"D"|"POINT(-74.00741090068288 40.71615626755086)"|"POINT(-73.97999843120539 40.73477895807408)"|"2019-03-12T07:03:00Z"|

Následující dotaz spojí zařízení datový proud s referenčními daty monitorové geografické zóny a vypočítá počet požadavků na oblast v časovém intervalu 15 minut po minutách.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

Tento dotaz Vypíše počet požadavků každou minutu za posledních 15 minut v každé oblasti v rámci město. Tyto informace lze snadno zobrazit řídicí panel Power BI, nebo můžete vysílat pro všechny ovladače jako SMS zprávy posílané prostřednictvím integrace se službami jako Azure functions.

Následující obrázek znázorňuje výstup dotazu na řídicí panel Power BI. 

![Výstup výsledků na řídicím panelu Power BI](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>Další postup

* [Úvod do služby Stream Analytics geoprostorové funkce](stream-analytics-geospatial-functions.md)
* [Geoprostorové funkce (Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/geospatial-functions)
