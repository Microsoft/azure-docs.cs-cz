---
title: Úvod do služby Azure Stream Analytics geoprostorové funkce
description: Tento článek popisuje geoprostorové funkce, které se používají v úlohách Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 02d1f551c7ec2856bbfce65c5397f454f6b9d5be
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43703599"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Úvod do služby Stream Analytics geoprostorové funkce

Geoprostorové funkce ve službě Azure Stream Analytics umožňují v reálném čase analýzy datových proudů Geoprostorová data. Pomocí několika řádků kódu můžete vyvíjet řešení na podnikové úrovni produkčního prostředí pro komplexní scénáře. 

Příklady scénářů, které může přinést geoprostorové funkce:

* Sdílením
* Správa vozového parku
* Sledování prostředků
* Geograficky monitorování geografických zón
* Sledování Phone napříč lokalitami buňky

Dotazovací jazyk Stream Analytics je sedm geoprostorové funkce: **CreateLineString**, **CreatePoint**, **: CreatePolygon**, **ST_DISTANCE** , **ST_OVERLAPS**, **ST_INTERSECTS**, a **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

`CreateLineString` Funkce přijímá body a vrátí GeoJSON LineString, které mohou být vykreslena jako řádek na mapě. Musí mít alespoň dva body vytvořit typ LineString. LineString body se budou připojovat k pořadí.

Následující dotaz používá `CreateLineString` vytvořit LineString pomocí tři body. První bod je vytvořen ze streamovaných vstupních dat, zatímco se další dva ručně vytvořené.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Příklad vstupu  
  
|Zeměpisná šířka|Zeměpisná délka|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Příklad výstupu  

 {"type": "LineString", "coordinates": [[-10.2, 3.0], [10.0, 10.0], [10.5, 10.5]]}

 {"type": "LineString", "coordinates": [[20.2321,-87.33], [10.0, 10.0], [10.5, 10.5]]}

Další informace najdete [CreateLineString](https://msdn.microsoft.com/azure/stream-analytics/reference/createlinestring) odkaz.

## <a name="createpoint"></a>CreatePoint

`CreatePoint` Funkce přijímá zeměpisné šířky a délky a vrátí bod GeoJSON, což mohou být vykreslena na mapě. Zeměpisná šířka a délka musí být **float** datového typu.

Následující příklad dotazu používá `CreatePoint` -li vytvořit pomocí zeměpisná šířka a délka ze streamovaných dat vstupní bod.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Příklad vstupu  
  
|Zeměpisná šířka|Zeměpisná délka|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Příklad výstupu
  
 {"type": "Point", "coordinates": [-10.2, 3.0]}  
  
 {"type": "Point", "coordinates": [20.2321,-87.33]}  

Další informace najdete [CreatePoint](https://msdn.microsoft.com/azure/stream-analytics/reference/createpoint) odkaz.

## <a name="createpolygon"></a>: CreatePolygon

`CreatePolygon` Funkce přijímá body a vrátí záznam mnohoúhelník GeoJSON. Pořadí bodů musí odpovídat orientaci pravém kanál, nebo proti směru hodinových ručiček. Představte si walking z jednoho místa do jiného v pořadí, ve kterém byly deklarovány. System center mnohoúhelníku by levé po celou dobu.

Následující příklad dotazu používá `CreatePolygon` vytvořit mnohoúhelníku z tři body. První dva body jsou vytvářeny ručně, a poslední bod je vytvořený vstupní data.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Příklad vstupu  
  
|Zeměpisná šířka|Zeměpisná délka|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Příklad výstupu  

 {"type": "Mnohoúhelníku", "coordinates": [[[-10.2, 3.0], [10.0, 10.0], [10.5, 10.5], [-10.2, 3.0]]]}
 
 {"type": "Mnohoúhelníku", "coordinates": [[[20.2321,-87.33], [10.0, 10.0], [10.5, 10.5], [20.2321,-87.33]]]}

Další informace najdete [: CreatePolygon](https://msdn.microsoft.com/azure/stream-analytics/reference/createpolygon) odkaz.


## <a name="stdistance"></a>ST_DISTANCE
`ST_DISTANCE` Funkce vrací vzdálenost mezi dvěma body v metrech. 

Následující dotaz používá `ST_DISTANCE` generovat událost, když je menší než 10 km od auta čerpací stanici.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Další informace najdete [ST_DISTANCE](https://msdn.microsoft.com/azure/stream-analytics/reference/st-distance) odkaz.

## <a name="stoverlaps"></a>ST_OVERLAPS
`ST_OVERLAPS` Funkce porovná dva mnohoúhelníku. Pokud polygonů překrývají, funkce vrátí hodnotu 1. Funkce vrátí hodnotu 0, pokud polygonů nepřekrývají. 

Následující dotaz používá `ST_OVERLAPS` generovat událost v případě, že v budově se v rámci možný výskyt zahlcení zóny.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

Následující příklad dotazu vygeneruje událost, když je storm záhlaví směrem k automobilu.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

Další informace najdete [ST_OVERLAPS](https://msdn.microsoft.com/azure/stream-analytics/reference/st-overlaps) odkaz.

## <a name="stintersects"></a>ST_INTERSECTS
`ST_INTERSECTS` Funkce porovná dva LineString. Pokud LineString intersect, funkce vrátí hodnotu 1. Funkce vrátí hodnotu 0, pokud LineString nepřekrývají.

Následující příklad dotazu používá `ST_INTERSECTS` k určení, pokud silnici protíná nečistoty cestách.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Příklad vstupu  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{"type": "LineString", "coordinates": [[-10.0, 0.0], [0.0, 0.0], [10.0, 0.0]]}|{"type": "LineString", "coordinates": [[0.0, 10.0], [0.0, 0.0], [0.0,-10.0]]}|  
|{"type": "LineString", "coordinates": [[-10.0, 0.0], [0.0, 0.0], [10.0, 0.0]]}|{"type": "LineString", "coordinates": [[-10.0, 10.0], [0.0, 10.0], [10.0, 10.0]]}|  
  
### <a name="output-example"></a>Příklad výstupu  

 1  
  
 0  

Další informace najdete [ST_INTERSECTS](https://msdn.microsoft.com/azure/stream-analytics/reference/st-intersects) odkaz.

## <a name="stwithin"></a>ST_WITHIN
`ST_WITHIN` Funkce určuje, jestli je v rámci mnohoúhelníku bodu nebo mnohoúhelníku. Obsahuje-li mnohoúhelníku bodu nebo mnohoúhelníku, funkce vrátí hodnotu 1. Funkce vrátí 0, pokud není umístěn v rámci deklarovaný mnohoúhelníku bodu nebo mnohoúhelníku.

Následující příklad dotazu používá `ST_WITHIN` zjistit, jestli je cílový bod doručování v rámci dané skladu mnohoúhelníku.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Příklad vstupu  
  
|deliveryDestination|Sklad|  
|-------------------------|---------------|  
|{"type": "Point", "coordinates": [76.6, 10,1]}|{"type": "Mnohoúhelníku", "coordinates": [[0.0, 0.0], [10.0, 0.0], [10.0, 10.0], [0.0, 10.0], [0.0, 0.0]]}|  
|{"type": "Point", "coordinates": [15.0, 15.0]}|{"type": "Mnohoúhelníku", "coordinates": [[10.0, 10.0], [20,0, 10.0], [20.0, 20.0], [10.0, 20,0], [10.0, 10.0]]}|  
  
### <a name="output-example"></a>Příklad výstupu  

 0  
  
 1  

Další informace najdete [ST_WITHIN](https://msdn.microsoft.com/azure/stream-analytics/reference/st-within) odkaz.

## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)