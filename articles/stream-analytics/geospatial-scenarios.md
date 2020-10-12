---
title: Geografická a geoprostorové agregace pomocí Azure Stream Analytics
description: Tento článek popisuje, jak používat Azure Stream Analytics pro geografickou a geoprostorové agregace.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/02/2019
ms.openlocfilehash: 9792641da4b3aebad047179e2c02dad757027801
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86045259"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>Scénáře použití geografického a geoprostorového agregace pomocí Azure Stream Analytics

Pomocí integrovaných geoprostorových funkcí můžete použít Azure Stream Analytics k sestavování aplikací pro scénáře, jako je Správa loďstva, jízdní sdílení, připojená auta a sledování prostředků.

## <a name="geofencing"></a>Geofencing (monitorování geografických zón)

Azure Stream Analytics podporuje výpočty geografických zón s nízkou latencí v reálném čase v cloudu a na IoT Edge runtime.

### <a name="geofencing-scenario"></a>Scénář monitorování geografických zón

Výrobní společnost potřebuje sledovat prostředky ve svých budovách. Mají všechna zařízení s GPS a chtějí dostávat oznámení, když zařízení opustí určitou oblast.

Referenční data použitá v tomto příkladu obsahují informace o geografickém plotu pro budovy a zařízení, která jsou v každé z budov povolena. Pamatujte, že referenční data by mohla být statická nebo pomalá změna. V tomto scénáři se používají statická referenční data. Proud dat průběžně generuje ID zařízení a jeho aktuální pozici.

### <a name="define-geofences-in-reference-data"></a>Definice geografických zón v referenčních datech

Geografické ohraničení lze definovat pomocí objektu geografické JSON. Pro úlohy s kompatibilitou verze 1,2 a vyšší lze geografické ploty definovat i pomocí známého textu (Well) jako `NVARCHAR(MAX)` . Well je standard pro Open Geospatial Consortium (OGC), který se používá k reprezentaci prostorových dat v textovém formátu.

Předdefinované geoprostorové funkce mohou pomocí definovaných geografických zón zjistit, zda je prvek v nebo z konkrétního mnohoúhelníku geografické ploty.

Následující tabulka představuje příklad referenčních dat geografického úložiště, která by mohla být uložená ve službě Azure Blob Storage nebo v tabulce Azure SQL. Všechny lokality jsou reprezentovány pomocí geoprostorového mnohoúhelníku a každé zařízení je přidruženo k povolenému ID webu.

|Počty|NázevLokality|Monitorované geografické zóny|AllowedDeviceID|
|------|--------|--------|---------------|
|1|"Redmond budovy 41"|"MNOHOÚHELNÍK ((-122.1337357922017 47.63782998329432,-122.13373042778369 47.637634793257305,-122.13346757130023 47.637642022530954,-122.13348902897235 47.637508280806806,-122.13361777500506 47.637508280806806,-122.13361241058703 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432))"|B|
|2|"Redmond budovy 40"|"MNOHOÚHELNÍK ((-122.1336154507967 47.6366745947009,-122.13361008637867 47.636483015064535,-122.13349206918201 47.636479400347675,-122.13349743360004 47.63636372927573,-122.13372810357532 47.63636372927573,-122.13373346799335 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009))"|Určitého|
|3|"Redmond budovy 22"|"MNOHOÚHELNÍK ((-122.13611660248233 47.63758544698554,-122.13635263687564 47.6374083293018,-122.13622389084293 47.63733603619712,-122.13622389084293 47.63717699101473,-122.13581619507266 47.63692757827657,-122.13559625393344 47.637046862778135,-122.13569281345798 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554))"|R|

### <a name="generate-alerts-with-geofence"></a>Generování výstrah s geografickou ochranou

Zařízení mohou každou minutu vysílat své ID a umístění prostřednictvím datového proudu `DeviceStreamInput` . Následující tabulka je datový proud vstupu.

|DeviceID|Mikropozice|
|--------|-----------|
|Určitého|"POINT (-122.13292341559497 47.636318374032726)"|
|B|"POINT (-122.13338475554553 47.63743531308874)"|
|R|"POINT (-122.13354001095752 47.63627622505007)"|

Můžete napsat dotaz, který spojuje datový proud zařízení s referenčními daty geografické plotu a vygeneruje výstrahu pokaždé, když je zařízení mimo povolené sestavení.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

Následující obrázek představuje geografické ploty. Můžete vidět, kde jsou zařízení v souladu s datovým proudem streamování.

![Sestavování geografických zón](./media/geospatial-scenarios/building-geofences.png)

Zařízení "C" se nachází uvnitř sestavování ID 2, které není povoleno podle referenčních dat. Toto zařízení se musí nacházet uvnitř sestavení s ID 3. Spuštění této úlohy vygeneruje výstrahu pro toto konkrétní porušení.

### <a name="site-with-multiple-allowed-devices"></a>Lokalita s několika povolenými zařízeními

Pokud lokalita povoluje více zařízení, může být definováno pole ID zařízení `AllowedDeviceID` a v klauzuli User-Defined lze použít funkci `WHERE` k ověření, zda ID zařízení datového proudu odpovídá jakémukoli ID zařízení v tomto seznamu. Další informace najdete v kurzu [JavaScript UDF](stream-analytics-javascript-user-defined-functions.md) pro cloudové úlohy a v kurzu pro [C# UDF](stream-analytics-edge-csharp-udf.md) pro úlohy Edge.

## <a name="geospatial-aggregation"></a>Geoprostorové agregace

Azure Stream Analytics podporuje geoprostorové agregace v reálném čase s nízkou latencí v cloudu a na IoT Edge runtime.

### <a name="geospatial-aggregation-scenario"></a>Scénář geoprostorové agregace

Společnost CAB chce vytvořit aplikaci v reálném čase, která bude podávat ovladače kabiny, aby hledaly v oblastech, ve kterých se aktuálně vyskytují vyšší požadavky.

Společnost ukládá logické oblasti města jako referenční data. Každá oblast je definována RegionID, RegionName a geografickou ochranou.

### <a name="define-the-geofences"></a>Definice geografických zón

Následující tabulka představuje příklad referenčních dat geografického úložiště, která by mohla být uložená ve službě Azure Blob Storage nebo v tabulce Azure SQL. Všechny oblasti jsou reprezentovány pomocí geoprostorového mnohoúhelníku, který se používá ke sladění požadavků přicházejících z datových proudů.

Tyto mnohoúhelníky jsou pouze pro referenci a nepředstavují skutečné logické ani fyzické separace.

|RegionID|RegionName|Monitorované geografické zóny|
|--------|----------|--------|
|1|Malé|"MNOHOÚHELNÍK ((-74.00279525078275 40.72833625216264,-74.00547745979765 40.721929158663244,-74.00125029839018 40.71893680218994,-73.9957785919998 40.72521409075776,-73.9972377137039 40.72557184584898,-74.00279525078275 40.72833625216264))"|
|2|"Chinatown"|"MNOHOÚHELNÍK ((-73.99712367114876 40.71281582267133,-73.9901070123658 40.71336881907936,-73.99023575839851 40.71452359088633,-73.98976368961189 40.71554823078944,-73.99551434573982 40.717337246783735,-73.99480624255989 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005,-73.99849696216438 40.713450141693166,-73.99748845157478 40.71405192594819,-73.99712367114876 40.71281582267133,-))"|
|3|"Tribeca"|"MNOHOÚHELNÍK ((-74.01091641815208 40.72583120006787,-74.01338405044578 40.71436586362705,-74.01370591552757 40.713617702123415,-74.00862044723533 40.711308107057235,-74.00194711120628 40.7194238654018,-74.01091641815208 40.72583120006787))"|

### <a name="aggregate-data-over-a-window-of-time"></a>Agregovaná data v časovém intervalu

Následující tabulka obsahuje streamovaná data "jezdí".

|UserID|FromLocation|ToLocation|TripRequestedTime|
|------|------------|----------|-----------------|
|Určitého|"POINT (-74.00726861389182 40.71610611981975)"|"POINT (-73.98615095917779 40.703107386025835)"|"2019-03-12T07:00:00Z"|
|B|"POINT (-74.00249841021645 40.723827238895666)"|"POINT (-74.01160699942085 40.71378884930115)"|"2019-03-12T07:01:00Z"|
|R|"POINT (-73.99680120565864 40.716439898624024)"|"POINT (-73.98289663412544 40.72582343969828)"|"2019-03-12T07:02:00Z"|
|"D"|"POINT (-74.00741090068288 40.71615626755086)"|"POINT (-73.97999843120539 40.73477895807408)"|"2019-03-12T07:03:00Z"|

Následující dotaz spojuje datový proud zařízení s referenčními daty geografické plotu a vypočítává počet požadavků na oblast v časovém intervalu 15 minut každou minutu.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

Tento dotaz vypíše počet požadavků každou minutu za posledních 15 minut podle každé oblasti v rámci města. Tyto informace můžete snadno zobrazit pomocí řídicího panelu Power BI, nebo je můžete vysílat do všech ovladačů jako textové zprávy SMS prostřednictvím integrace se službami, jako je služba Azure Functions.

Následující obrázek ukazuje výstup dotazu na Power BI řídicí panel. 

![Výstup výsledku na řídicím panelu Power BI](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>Další kroky

* [Úvod do Stream Analytics geoprostorové funkce](stream-analytics-geospatial-functions.md)
* [Geoprostorové funkce (Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/geospatial-functions)
