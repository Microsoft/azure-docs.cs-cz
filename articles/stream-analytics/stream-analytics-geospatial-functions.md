---
title: Úvod do Azure Stream Analytics geoprostorové funkce
description: Tento článek popisuje geoprostorové funkce, které se používají v Azure Stream Analytics úlohách.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: f47f34b60c858bb9a0feafd25176e4a811046630
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75426228"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Úvod do Stream Analytics geoprostorové funkce

Geoprostorové funkce v Azure Stream Analytics umožňují analýzu dat v reálném čase streamování geoprostorového obsahu. Pouze pár řádků kódu, můžete vyvíjet řešení produkčních stupňů pro složité scénáře. 

Mezi příklady scénářů, které můžou využívat geoprostorové funkce, patří:

* Jízdní sdílení
* Správa loďstva
* Sledování prostředků
* Geografické oplocení
* Sledování telefonů napříč weby s buňkami

Jazyk Stream Analytics dotazů má sedm integrovaných geoprostorovéch funkcí: **CreateLineString**, **CreatePoint**, **CreatePolygon**, **ST_DISTANCE**, **ST_OVERLAPS**, **ST_INTERSECTS**a **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

`CreateLineString`Funkce akceptuje body a vrátí LineString pro inline JSON, který lze vykreslit jako čáru na mapě. Abyste mohli vytvořit LineString, musíte mít aspoň dva body. Body LineString budou připojené v daném pořadí.

Následující dotaz slouží `CreateLineString` k vytvoření LineString pomocí tří bodů. První bod je vytvořen ze streamování vstupních dat, zatímco ostatní dva jsou vytvořeny ručně.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Příklad vstupu  
  
|šířce|bodu|  
|--------------|---------------|  
|3,0|-10,2|  
|-87,33|20,2321|  
  
### <a name="output-example"></a>Příklad výstupu  

 {"Type": "LineString"; "souřadnice": [[-10,2, 3,0], [10,0, 10,0], [10,5, 10,5]]}

 {"Type": "LineString"; "souřadnice": [[20,2321,-87,33], [10,0, 10,0], [10,5, 10,5]]}

Další informace najdete v referenčních informacích k [CreateLineString](https://docs.microsoft.com/stream-analytics-query/createlinestring) .

## <a name="createpoint"></a>CreatePoint

`CreatePoint`Funkce přijímá zeměpisnou šířku a délku a vrací bodový bod JSON, který lze vykreslit na mapě. Vaše Latitudes a zeměpisná délka musí být typu **float** .

Následující příklad dotazu používá `CreatePoint` k vytvoření bodu pomocí Latitudes a délky ze streamování vstupních dat.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Příklad vstupu  
  
|šířce|bodu|  
|--------------|---------------|  
|3,0|-10,2|  
|-87,33|20,2321|  
  
### <a name="output-example"></a>Příklad výstupu
  
 {"Type": "Point", "souřadnice": [-10,2, 3,0]}  
  
 {"Type": "Point"; "souřadnice": [20,2321,-87,33]}  

Další informace najdete v referenčních informacích k [CreatePoint](https://docs.microsoft.com/stream-analytics-query/createpoint) .

## <a name="createpolygon"></a>CreatePolygon

`CreatePolygon`Funkce akceptuje body a vrátí záznam mnohoúhelníku pro injson. Pořadí bodů musí následovat po orientaci na pravé straně nebo proti směru hodinových ručiček. Představte si procházení od jednoho bodu k druhému v pořadí, ve kterém byly deklarovány. Střed mnohoúhelníku by byl pro celý čas.

Následující příklad dotazu používá `CreatePolygon` k vytvoření mnohoúhelníku ze tří bodů. První dva body jsou vytvořeny ručně a poslední bod je vytvořen ze vstupních dat.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Příklad vstupu  
  
|šířce|bodu|  
|--------------|---------------|  
|3,0|-10,2|  
|-87,33|20,2321|  
  
### <a name="output-example"></a>Příklad výstupu  

 {"Type": "mnohoúhelník"; "souřadnice": [[[-10,2, 3,0], [10,0, 10,0], [10,5, 10,5], [-10,2, 3,0]]]}
 
 {"Type": "mnohoúhelník"; "souřadnice": [[[20,2321,-87,33], [10,0, 10,0], [10,5, 10,5], [20,2321,-87,33]]]}

Další informace najdete v referenčních informacích k [CreatePolygon](https://docs.microsoft.com/stream-analytics-query/createpolygon) .


## <a name="st_distance"></a>ST_DISTANCE
`ST_DISTANCE`Funkce vrací vzdálenost mezi dvěma body v metrech. 

Následující dotaz `ST_DISTANCE` vygeneruje událost, když je čerpací stanice menší než 10 km od auta.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Další informace najdete v referenčních informacích k [ST_DISTANCE](https://docs.microsoft.com/stream-analytics-query/st-distance) .

## <a name="st_overlaps"></a>ST_OVERLAPS
`ST_OVERLAPS`Funkce porovná dva mnohoúhelníky. Pokud se mnohoúhelníky překrývají, funkce vrátí hodnotu 1. Funkce vrátí hodnotu 0, pokud se mnohoúhelníky nepřekrývají. 

Následující dotaz `ST_OVERLAPS` vygeneruje událost, když je budova v rámci možné zóny zahlcení.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

Následující příklad dotazu vygeneruje událost, pokud je v názvu objektu auto.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

Další informace najdete v referenčních informacích k [ST_OVERLAPS](https://docs.microsoft.com/stream-analytics-query/st-overlaps) .

## <a name="st_intersects"></a>ST_INTERSECTS
`ST_INTERSECTS`Funkce porovná dvě LineString. Pokud se LineString protínají, funkce vrátí hodnotu 1. Funkce vrátí hodnotu 0, pokud se LineString neprotínají.

Následující příklad dotazu používá `ST_INTERSECTS` k určení, jestli se pavedá cesta překládá na nečistotě.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Příklad vstupu  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{"Type": "LineString"; "souřadnice": [[-10,0, 0,0], [0,0, 0,0], [10,0, 0,0]]}|{"Type": "LineString"; "souřadnice": [[0,0, 10,0], [0,0, 0,0], [0,0,-10,0]]}|  
|{"Type": "LineString"; "souřadnice": [[-10,0, 0,0], [0,0, 0,0], [10,0, 0,0]]}|{"Type": "LineString"; "souřadnice": [[-10,0, 10,0], [0,0, 10,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Příklad výstupu  

 1  
  
 0  

Další informace najdete v referenčních informacích k [ST_INTERSECTS](https://docs.microsoft.com/stream-analytics-query/st-intersects) .

## <a name="st_within"></a>ST_WITHIN
`ST_WITHIN`Funkce určuje, zda je bod nebo mnohoúhelník v rámci mnohoúhelníku. Pokud mnohoúhelník obsahuje bod nebo mnohoúhelník, funkce vrátí hodnotu 1. Funkce vrátí 0, pokud se bod nebo mnohoúhelník nenachází v deklarovaném mnohoúhelníku.

Následující příklad dotazu používá `ST_WITHIN` k určení, zda je cílový bod doručení v rámci daného mnohoúhelníku datového skladu.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Příklad vstupu  
  
|deliveryDestination|skladu|  
|-------------------------|---------------|  
|{"Type": "Point", "souřadnice": [76,6, 10,1]}|{"Type": "mnohoúhelník"; "souřadnice": [[0,0, 0,0], [10,0, 0,0], [10,0, 10,0], [0,0, 10,0], [0,0, 0,0]]}|  
|{"Type": "Point", "souřadnice": [15,0, 15,0]}|{"Type": "mnohoúhelník"; "souřadnice": [[10,0, 10,0], [20,0, 10,0], [20,0, 20,0], [10,0, 20,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Příklad výstupu  

 0  
  
 1  

Další informace najdete v referenčních informacích k [ST_WITHIN](https://docs.microsoft.com/stream-analytics-query/st-within) .

## <a name="next-steps"></a>Další kroky

* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
