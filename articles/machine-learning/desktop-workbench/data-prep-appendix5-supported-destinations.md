---
title: Podporované datové cíle a výstupy, které jsou k dispozici s přípravou dat Azure Machine Learning | Dokumentace Microsoftu
description: Tento dokument poskytuje úplný seznam podporovaných cílů a vypíše dostupné pro přípravu dat Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: f3d7d10db8cdeb4a1a409650f17d2fc39bf7971f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969481"
---
# <a name="supported-data-exports-for-this-preview"></a>Pro tuto verzi preview nepodporuje export dat 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Je možné exportovat do několika různých formátů. Pokud chcete zachovat mezivýsledků přípravu dat před začlenit výsledky do ostatních pracovního postupu, Machine Learning můžete použít tyto formáty.

## <a name="types"></a>Typy 
### <a name="csv-file"></a>Soubor CSV 
Zápis do souboru čárkami oddělených hodnot do úložiště.

#### <a name="options"></a>Možnosti
- Konce řádků
- Nahraďte hodnoty Null s
- Nahradit chyby 
- Oddělovač


### <a name="parquet"></a>Parquet 
Zapište datovou sadu jako Parquet do úložiště.

Parquet jako formátu můžou mít různé formy v úložišti. Pro menší datové sady se někdy používá .parquet jeden soubor. Různé knihovny jazyka Python podporují čtení a zápis do jednoho .parquet soubory. 

V současné době aplikace Azure Machine Learning Workbench spoléhá na knihovny PyArrow Python pro vypsání Parquet během interaktivní místní použití. To znamená, Parquet jeden soubor je aktuálně pouze Parquet výstupní formát, který je podporovaný během interaktivní místní použití.

Během spuštění horizontální navýšení kapacity (na Sparku) Azure Machine Learning Workbench spoléhá na Spark Parquet čtení a zápis funkce. Spark výchozí výstupní formát Parquet (aktuálně pouze jaký podporuje) je podobné struktury datové sady Hive. To znamená, že složka obsahuje mnoho souborů .parquet, které jsou menší oddílu větší datové sady. 

#### <a name="caveats"></a>Upozornění 
Parquet formátu je relativně mladé a má některé implementace nekonzistence mezi různé knihovny. Pro instanci Spark umístí omezení, na kterých znaky jsou platné v názvech sloupce, při zápisu do Parquet navýšení kapacity. PyArrow, proveďte to. V názvu sloupce nemůže být tyto znaky: 
- ,
- ;
- {}
- ()
- \\n
- \\t
- =

>[!NOTE]
>- K zajištění kompatibility se Sparkem pokaždé, když zapíšete data do Parquet, výskyty tyto znaky v názvy sloupců jsou nahrazeny a znaky podtržítka (_).
>- K zachování konzistence napříč místním a horizontální navýšení kapacity spuštění, všechna data zapsána do Parquet, prostřednictvím aplikací, Python nebo Spark, má názvy sloupců upravený k zajištění kompatibility Spark. Aby názvů očekávaných sloupců při zápisu do Parquet znaků ve Sparku, odeberte neplatné nastavení ze sloupce před zápisem.



## <a name="locations"></a>Umístění 
### <a name="local"></a>Místní 
Místní pevný disk nebo namapované síťové umístění úložiště.

### <a name="azure-blob-storage"></a>Azure Blob Storage
Azure Blob storage, musíte mít předplatné Azure.

