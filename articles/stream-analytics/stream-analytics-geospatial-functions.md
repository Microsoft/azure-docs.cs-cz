---
title: Úvod ke geoprostorovým funkcím Azure Stream Analytics
description: Tento článek popisuje geoprostorové funkce, které se používají v azure stream analytics úlohy.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: f47f34b60c858bb9a0feafd25176e4a811046630
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426228"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Úvod do geoprostorových funkcí Stream Analytics

Geoprostorové funkce ve službě Azure Stream Analytics umožňují analýzu streamování geoprostorových dat v reálném čase. Pomocí několika řádků kódu můžete vyvinout řešení výrobní třídy pro složité scénáře. 

Příklady scénářů, které mohou těžit z geoprostorových funkcí:

* Sdílení jízd
* Správa vozového parku
* Sledování prostředků
* Geografické oplocení
* Sledování telefonu na mobilních webech

Dotazovací jazyk Stream Analytics má sedm vestavěných geoprostorových funkcí: **CreateLineString**, **CreatePoint**, **CreatePolygon**, **ST_DISTANCE**, **ST_OVERLAPS**, **ST_INTERSECTS**a **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

Funkce `CreateLineString` přijímá body a vrací GeoJSON LineString, který může být vykreslen jako řádek na mapě. Chcete-li vytvořit linestring, musíte mít alespoň dva body. Body LineString budou připojeny v pořadí.

Následující dotaz `CreateLineString` používá k vytvoření LineString pomocí tří bodů. První bod je vytvořen z streamování vstupních dat, zatímco další dva jsou vytvořeny ručně.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Vstupní příklad  
  
|Šířky|Délky|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Příklad výstupu  

 {"type" : "LineString", "coordinates" : [ [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5] ]}

 {"type" : "LineString", "coordinates" : [ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5] ]}

Další informace naleznete na odkazu [CreateLineString.](https://docs.microsoft.com/stream-analytics-query/createlinestring)

## <a name="createpoint"></a>CreatePoint

Funkce `CreatePoint` přijme zeměpisnou šířku a délku a vrátí bod GeoJSON, který lze vykreslit na mapě. Zeměpisné šířky a délky musí být **plovoucí** datový typ.

Následující ukázkový `CreatePoint` dotaz používá k vytvoření bodu pomocí zeměpisných šířky a délky z streamování vstupních dat.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Vstupní příklad  
  
|Šířky|Délky|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Příklad výstupu
  
 {"typ" : "Point", "coordinates" : [-10.2, 3.0]}  
  
 {"typ" : "Point", "coordinates" : [20.2321, -87.33]}  

Další informace najdete v odkazu [na vytvoření pointy.](https://docs.microsoft.com/stream-analytics-query/createpoint)

## <a name="createpolygon"></a>CreatePolygon

Funkce `CreatePolygon` přijme body a vrátí polygonový záznam GeoJSON. Pořadí bodů musí následovat orientace pravého kroužku nebo proti směru hodinových ručiček. Představte si, že chodíte z jednoho místa do druhého v pořadí, v jakém byly deklarovány. Střed polyga by byl po celou dobu po vaší levici.

Následující příklad dotazu používá `CreatePolygon` k vytvoření polygon ze tří bodů. První dva body jsou vytvořeny ručně a poslední bod je vytvořen ze vstupních dat.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Vstupní příklad  
  
|Šířky|Délky|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Příklad výstupu  

 {"typ" : "Polygon", "souřadnice" : [[ -10,2, 3,0], [10,0, 10,0], [10,5, 10,5], [-10,2, 3,0] ]]}
 
 {"typ" : "Polygon", "souřadnice" : [[ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5], [20.2321, -87.33] ]]}

Další informace naleznete v odkazu [Vytvořit polygon.](https://docs.microsoft.com/stream-analytics-query/createpolygon)


## <a name="st_distance"></a>ST_DISTANCE
Funkce `ST_DISTANCE` vrátí vzdálenost mezi dvěma body v metrech. 

Následující dotaz `ST_DISTANCE` používá ke generování události, pokud je čerpací stanice méně než 10 km od vozu.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Chcete-li se dozvědět více, navštivte [odkaz na ST_DISTANCE.](https://docs.microsoft.com/stream-analytics-query/st-distance)

## <a name="st_overlaps"></a>ST_OVERLAPS
Funkce `ST_OVERLAPS` porovná dva polygony. Pokud se polygony překrývají, funkce vrátí hodnotu 1. Funkce vrátí hodnotu 0, pokud se vícenoly nepřekrývají. 

Následující dotaz `ST_OVERLAPS` používá ke generování události, pokud je budova v rámci možné zaplavovací zóny.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

Následující příklad dotazu generuje událost, když bouře směřuje k vozu.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

Chcete-li se dozvědět více, navštivte [odkaz na ST_OVERLAPS.](https://docs.microsoft.com/stream-analytics-query/st-overlaps)

## <a name="st_intersects"></a>ST_INTERSECTS
Funkce `ST_INTERSECTS` porovná dva LineString. Pokud se linestring protíná, funkce vrátí hodnotu 1. Funkce vrátí hodnotu 0, pokud se protínají řetězce LineString.

Následující příklad dotazu používá `ST_INTERSECTS` k určení, zda zpevněné silnice protíná prašné cesty.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Vstupní příklad  
  
|oblast datového centra|bouřeOblast|  
|--------------------|---------------|  
|{"type":"LineString", "coordinates": [ [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{"type":"LineString", "coordinates": [ [0.0, 10.0], [0.0, 0.0], [0.0, -10.0] ]}|  
|{"type":"LineString", "coordinates": [ [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{"type":"LineString", "coordinates": [ [-10.0, 10.0], [0.0, 10.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>Příklad výstupu  

 1  
  
 0  

Chcete-li se dozvědět více, navštivte odkaz na [ST_INTERSECTS.](https://docs.microsoft.com/stream-analytics-query/st-intersects)

## <a name="st_within"></a>ST_WITHIN
Funkce `ST_WITHIN` určuje, zda je bod nebo polygon v polygonu. Pokud polygon obsahuje bod nebo polygon, funkce vrátí 1. Funkce vrátí 0, pokud bod nebo polygon není umístěn v deklarovaném polygu.

Následující ukázkový `ST_WITHIN` dotaz používá k určení, zda je cílový bod dodávky v rámci daného polygonu skladu.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Vstupní příklad  
  
|deliveryDestination|Skladu|  
|-------------------------|---------------|  
|{"type":"Point", "coordinates": [76.6, 10.1]}|{"type":"Polygon", "coordinates": [ [0.0, 0.0], [10.0, 0.0], [10.0, 10.0], [0.0, 10.0], [0.0, 0.0] ]}|  
|{"type":"Point", "coordinates": [15.0, 15.0]}|{"type":"Polygon", "coordinates": [ [10.0, 10.0], [20.0, 10.0], [20.0, 20.0], [10.0, 20.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>Příklad výstupu  

 0  
  
 1  

Další informace naleznete v [ST_WITHIN](https://docs.microsoft.com/stream-analytics-query/st-within) odkazu.

## <a name="next-steps"></a>Další kroky

* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
