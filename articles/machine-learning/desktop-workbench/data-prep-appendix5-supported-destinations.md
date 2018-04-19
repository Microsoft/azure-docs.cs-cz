---
title: "Podporované cíle dat a výstupy, které jsou k dispozici přípravy dat Azure Machine Learning | Microsoft Docs"
description: "Tento dokument poskytuje úplný seznam podporovaných cíle a výstupy k dispozici pro přípravu Azure Machine Learning dat."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 5451a5f525da845f97141a97241efb53ec42ff58
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="supported-data-exports-for-this-preview"></a>Podporované exportuje data pro tuto verzi preview 
Je možné exportovat do několika různých formátech. Tyto formáty můžete zachovat mezilehlých výsledků přípravy data před integrací výsledky do zbytku Machine Learning pracovního postupu.

## <a name="types"></a>Typy 
### <a name="csv-file"></a>Soubor CSV 
Zápis do souboru čárkami oddělených hodnot do úložiště.

#### <a name="options"></a>Možnosti
- Konce řádků
- Nahraďte hodnoty Null s
- Nahraďte chyby s 
- Oddělovač


### <a name="parquet"></a>Parquet 
Zapsat datovou sadu jako Parquet do úložiště.

Parquet jako formátu můžou mít různé formy v úložišti. Pro menší datové sady se někdy používá .parquet jeden soubor. Různé Python knihovny podporují čtení a zápis do jednoho .parquet souborů. 

V současné době Azure Machine Learning Workbench spoléhá na knihovně PyArrow Python pro vypsání Parquet během místní interaktivní používání. Znamená, že jedním souborem Parquet je aktuálně pouze formát výstupu Parquet podporovanou během místní interaktivní používání.

Během spuštění Škálováním na více systémů (na Spark) Azure Machine Learning Workbench spoléhá na Spark Parquet čtení a zápis funkce. Spark výchozí formát výstupu Parquet (aktuálně pouze jaký podporuje) je podobný ve struktuře datové sady Hive. To znamená, že složka obsahuje mnoho .parquet soubory, které jsou všechny menší oddíl větší datové sady. 

#### <a name="caveats"></a>Upozornění 
Parquet formátu je poměrně malí a má některé implementace nekonzistence mezi různé knihovny. Pro instanci Spark umístí omezení, na kterých se znaky v názvy sloupců, při zápisu do Parquet. Toto PyArrow neprovede. V názvu sloupce nemůže být tyto znaky: 
- ,
- ;
- {}
- ()
- \\n
- \\t
- =

>[!NOTE]
>- Pro zajištění kompatibility s Spark, při každém zápisu dat do Parquet, výskyty tyto znaky v názvy sloupců jsou nahrazeny a podtržítko (_).
>- K zachování konzistence napříč místním a Škálováním na více systémů běží, jakákoliv data zapsána do Parquet, prostřednictvím aplikace, Python nebo Spark, má názvy sloupců upravený pro zajištění kompatibility Spark. Aby názvů očekávaných sloupců při zápisu do Parquet znaků Spark, odeberte neplatné nastavte ze sloupce před jejich zápis.



## <a name="locations"></a>Umístění 
### <a name="local"></a>Místní 
Místní pevný disk nebo namapované síťové umístění úložiště.

### <a name="azure-blob-storage"></a>Azure Blob Storage
Úložiště objektů Blob Azure vyžaduje předplatné Azure.

