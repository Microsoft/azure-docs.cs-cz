---
title: Geofencing a geoprostorová agregace pomocí Azure Stream Analytics
description: Tento článek popisuje, jak používat Azure Stream Analytics pro geofencing a geoprostorové agregace.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 5a3aa3786469c3df37b53cb82bdd396871689297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443644"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>Scénáře geofencingu a geoprostorové agregace pomocí Azure Stream Analytics

Díky integrovaným geoprostorovým funkcím můžete pomocí Azure Stream Analytics vytvářet aplikace pro scénáře, jako je správa vozového parku, sdílení jízd, propojená auta a sledování majetku.

## <a name="geofencing"></a>Geofencing (monitorování geografických zón)

Azure Stream Analytics podporuje výpočty geografického oplocení v reálném čase s nízkou latencí v reálném čase v cloudu a na runtime IoT Edge.

### <a name="geofencing-scenario"></a>Scénář geofencingu

Výrobní společnost musí sledovat majetek na svých budovách. Vybavili každé zařízení GPS a chtějí dostávat oznámení, pokud zařízení opustí určitou oblast.

Referenční data použitá v tomto příkladu mají informace o geografické plotě pro budovy a zařízení, která jsou povolena v každé z budov. Nezapomeňte, že referenční data mohou být statická nebo pomalá. Pro tento scénář se používají statická referenční data. Datový proud nepřetržitě vysílá ID zařízení a jeho aktuální polohu.

### <a name="define-geofences-in-reference-data"></a>Definování geografických zón v referenčních datech

Geofence lze definovat pomocí geojson objektu. Pro úlohy s kompatibilitou verze 1.2 a vyšší, geofences lze také `NVARCHAR(MAX)`definovat pomocí dobře známý text (WKT) jako . WKT je standard Open Geospatial Consortium (OGC), který se používá k reprezentaci prostorových dat v textovém formátu.

Vestavěné geoprostorové funkce mohou pomocí definovaných geografických zón zjistit, zda je prvek v určitém polygonu geofence nebo z jeho opuštění.

Následující tabulka je příkladem referenčních dat geografické zóny, která se mohou ukládat v úložišti objektů blob Azure nebo v tabulce Azure SQL. Každá lokalita je reprezentována geoprostorovým mnohonoly a každé zařízení je přidruženo k povolenému ID lokality.

|ID serveru SiteID|NázevLokality|Geofence|AllowedDeviceID|
|------|--------|--------|---------------|
|1|"Budova Redmond 41"|"Polygon((-122.1337357922017 47.63782998329432,-122.13373042778369 47.637634793257305,-122.13337304234.63.6363 2022530954,-122.13348902897235 47.637508280806806,-122.133617777500506 47.637508280806806,-122.1333612410588 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432))"|"B"|
|2|"Budova Redmond 40"|"Polygon((-122.1336154507967 47.6366745947009,-122.133610086378668674 79400347675,-122.13349743360004 47.6363637372927573,-122.13372810357532 47.63636372927573,-122.1337334669933535 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009))"|"A"|
|3|"Budova Redmond 22"|"POLYGON((-122.13611660248233 47.63758544698554,-122.13635263856660248293018,-13622.136352384843373 3603619712,-122.13622389084293 47.63717699101473,-122.1358161950726647.63692757827657,-122.135596253933444 47.637046862778135,-122.13569281345798 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554))"|"C"|

### <a name="generate-alerts-with-geofence"></a>Generování výstrah pomocí geografické zóny

Zařízení mohou vyzařovat své ID `DeviceStreamInput`a umístění každou minutu prostřednictvím datového proudu s názvem . Následující tabulka je datový proud vstupu.

|Deviceid|Geopozice|
|--------|-----------|
|"A"|"POINT(-122.13292341559497 47.636318374032726)"|
|"B"|"POINT(-122.133384755545553 47.63743531308874)"|
|"C"|"POINT(-122.13354001095752 47.63627622505007)"|

Můžete napsat dotaz, který spojuje datový proud zařízení s referenčními daty geofence a generuje výstrahu pokaždé, když je zařízení mimo povolenou budovu.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

Následující obrázek představuje geofences. Můžete vidět, kde jsou zařízení v souladu se vstupem dat datového proudu.

![Budování geoplotů](./media/geospatial-scenarios/building-geofences.png)

Zařízení "C" se nachází uvnitř ID budovy 2, což není podle referenčních údajů povoleno. Toto zařízení by mělo být umístěno uvnitř id budovy 3. Spuštění této úlohy vygeneruje výstrahu pro toto konkrétní porušení.

### <a name="site-with-multiple-allowed-devices"></a>Web s více povolenými zařízeními

Pokud lokalita umožňuje více zařízení, může být definována `AllowedDeviceID` pole ID zařízení a uživatelem definovaná funkce může být použita na `WHERE` klauzuli k ověření, zda ID zařízení datového proudu odpovídá jakémukoli ID zařízení v tomto seznamu. Další informace naleznete v kurzu [UDF javascriptu](stream-analytics-javascript-user-defined-functions.md) pro úlohy v cloudu a kurz [C# UDF](stream-analytics-edge-csharp-udf.md) pro hraniční úlohy.

## <a name="geospatial-aggregation"></a>Geoprostorová agregace

Azure Stream Analytics podporuje geoprostorovou agregaci v reálném čase s nízkou latencí v cloudu a na modulu runtime IoT Edge.

### <a name="geospatial-aggregation-scenario"></a>Scénář geoprostorové agregace

Taxi společnost chce vybudovat real-time aplikace, aby vedl své taxikáře, kteří hledají jízdu směrem k oblastem měst v současné době zažívá vyšší poptávku.

Společnost ukládá logické oblasti města jako referenční data. Každá oblast je definována RegionID, RegionName a Geofence.

### <a name="define-the-geofences"></a>Definování geografických zón

Následující tabulka je příkladem referenčních dat geografické zóny, která se mohou ukládat v úložišti objektů blob Azure nebo v tabulce Azure SQL. Každá oblast je reprezentována geoprostorovým mnohonoly, který se používá ke korelaci s požadavky přicházejícími z datových proudů dat.

Tyto polygony jsou pouze orientační a nepředstavují skutečné logické nebo fyzické oddělení města.

|ID regionu|Regionname|Geofence|
|--------|----------|--------|
|1|"SoHo"|"POLYGON((-74.00279525078275 40.72833625216264,-74.0054774597 9765 40.721929158663244,-74.00125029839018 40.71893680218 9994,-73.995785919998 40.72521409075776,-73.9972377137039 40.72557184584898,-74.00279525078275 40.728336252162644) )"|
|2|"Čínská čtvrť"|"POLYGON((-73.9971236714876 40.7128158226713,-73.99010701236.9958840.7133688197936,-73.9902355839551 40.7145 23598633,-73.9896368966189 40.71554823078944,-73.99551434573982 40.717337246783735,-73.994806242555989 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005,-73.99849696216438 40.713450141693166,-73.99748845157478 40.71405192594819,-73.99712367114876 40.71281582267133))"|
|3|"Tribeca"|"Polygon((-74.01091641815208 40.7258312006787,-74.01338405044578 40.71436586362705,-74.0137055275 1777023415,-74.0086204447235333 40.71308107057235,-74.00194711120628 40.7194238654018,-74.01091641815208 40.72583120006787))"|

### <a name="aggregate-data-over-a-window-of-time"></a>Agregace dat za časové období

Následující tabulka obsahuje streamovaná data "jízd".

|UserID|Z umístění|Umístění|TripRequestedTime|
|------|------------|----------|-----------------|
|"A"|"POINT(-74.00726861389182 40.71610611981975)"|"POINT(-73.98615095917779 40.703107386025835)"|"2019-03-12T07:00:00Z"|
|"B"|"POINT(-74.00249841021645 40.723827238895666)"|"POINT(-74.01160699942085 40.71378884930115)"|"2019-03-12T07:01:00Z"|
|"C"|"POINT(-73.99680120565864 40.716439898624024)"|"POINT(-73.9828963412544 40.72582343969828)"|"2019-03-12T07:02:00Z"|
|"D"|"POINT(-74.00741090068288 40.71615626755086)"|"POINT(-73.97999843120539 40.73477895807408)"|"2019-03-12T07:03:00Z"|

Následující dotaz spojuje datový proud zařízení s referenčními daty geografické zóny a vypočítá počet požadavků na oblast v časovém okně 15 minut každou minutu.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

Tento dotaz vyprovodí počet požadavků každou minutu za posledních 15 minut podle každé oblasti ve městě. Tyto informace lze snadno zobrazit na řídicím panelu Power BI nebo je lze vysílat všem ovladačům jako textové zprávy SMS prostřednictvím integrace se službami, jako jsou funkce Azure.

Obrázek níže znázorňuje výstup dotazu na řídicí panel Power BI. 

![Výstup výsledků na řídicím panelu Power BI](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>Další kroky

* [Úvod do geoprostorových funkcí Stream Analytics](stream-analytics-geospatial-functions.md)
* [Geoprostorové funkce (Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/geospatial-functions)
