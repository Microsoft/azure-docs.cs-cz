---
title: Azure Stream Analytics vlastní dělení výstupu objektů BLOB
description: Tento článek popisuje vlastní vzorce cesty DateTime a funkce vlastního pole nebo atributů pro výstup služby Blob Storage z Azure Stream Analytics úloh.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: seodec18
ms.openlocfilehash: 7239c2e3cb42cb17b01904e8fc226ae2408dbb47
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617421"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Azure Stream Analytics vlastní dělení výstupu objektů BLOB

Azure Stream Analytics podporuje vytváření oddílů vlastního výstupu objektů BLOB s vlastními poli nebo atributy a vlastními vzory cesty DateTime. 

## <a name="custom-field-or-attributes"></a>Vlastní pole nebo atributy

Vlastní pole nebo vstupní atributy zlepšují pracovní postupy pro zpracování dat a vytváření sestav tím, že umožňují větší kontrolu nad výstupem.

### <a name="partition-key-options"></a>Možnosti klíče oddílu

Klíč oddílu nebo název sloupce, který se používá k dělení vstupních dat, může obsahovat libovolný znak, který je přijatý pro [názvy objektů BLOB](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata). Vnořené pole nejde použít jako klíč oddílu, pokud se nepoužívá ve spojení s aliasy, ale můžete použít určité znaky k vytvoření hierarchie souborů. Pomocí následujícího dotazu můžete například vytvořit sloupec, který kombinuje data ze dvou dalších sloupců, a vytvořit tak jedinečný klíč oddílu.

```sql
SELECT name, id, CONCAT(name, "/", id) AS nameid
```

Klíč oddílu musí být NVARCHAR (MAX), BIGINT, FLOAT nebo BIT (úroveň kompatibility 1,2 nebo vyšší). Typy DateTime, Array a Record nejsou podporovány, ale lze je použít jako klíče oddílu, pokud jsou převedeny na řetězce. Další informace najdete v tématu [Azure Stream Analytics datových typů](/stream-analytics-query/data-types-azure-stream-analytics).

### <a name="example"></a>Příklad

Předpokládejme, že úloha přebírá vstupní data z živých uživatelských relací připojených ke službě externí videohry, kde ingestovaná data obsahují sloupec **client_id** k identifikaci relací. Při vytváření oddílů dat **client_id** nastavte pole vzor cesty objektů BLOB tak, aby zahrnovalo token oddílu **{client_id}** v výstupních vlastnostech objektu BLOB při vytváření úlohy. Jelikož data s různými **client_idmi** hodnotami přecházejí prostřednictvím úlohy Stream Analytics, výstupní data se ukládají do samostatných složek na základě jedné **client_id** hodnoty na složku.

![Vzor cesty s ID klienta](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

Podobně platí, že pokud byla vstupem úlohy senzorická data z milionů senzorů, ve kterých měl každý senzor **sensor_id**, bude vzor cesty **{sensor_id}** rozdělit jednotlivé údaje ze senzorů do různých složek.  


Pomocí REST API oddíl Output souboru JSON, který se používá pro tento požadavek, může vypadat takto:  

![Výstup REST API](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

Po spuštění úlohy může kontejner *klienti* vypadat takto:  

![Kontejner klientů](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Každá složka může obsahovat několik objektů blob, kde každý objekt BLOB obsahuje jeden nebo víc záznamů. V předchozím příkladu je ve složce s názvem "06000000" jeden objekt BLOB s následujícím obsahem:

![Obsah objektu BLOB](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Všimněte si, že každý záznam v objektu BLOB má **client_id** sloupec odpovídající názvu složky, protože sloupec použitý k vytvoření oddílu výstupu ve výstupní cestě byl **client_id**.

### <a name="limitations"></a>Omezení

1. Ve vlastnosti Output objektu BLOB vzoru cesty je povolený jenom jeden vlastní klíč oddílu. Všechny následující vzory cest jsou platné:

   * Cluster1/{Date}/{aFieldInMyData}  
   * Cluster1/{Time}/{aFieldInMyData}  
   * Cluster1/{aFieldInMyData}  
   * Cluster1/{Date}/{Time}/{aFieldInMyData} 
   
2. V klíčích oddílů se nerozlišují malá a velká písmena, takže klíče oddílů jako Jan a Jan jsou ekvivalentní. Výrazy se navíc nedají použít jako klíče oddílu. Například **{Column + columnB}** nefunguje.  

3. Když vstupní datový proud sestává z záznamů s mohutnosti klíče oddílu v 8000, záznamy se připojí k existujícím objektům blob a v případě potřeby vytvoří jenom nové objekty blob. Pokud mohutnost překračuje 8000, nebudou se do nich zapisovat existující objekty BLOB a nevytvoří se nové objekty blob pro libovolný počet záznamů se stejným klíčem oddílu.

4. Pokud je výstup objektu BLOB [nakonfigurován jako neměnný](../storage/blobs/storage-blob-immutable-storage.md), Stream Analytics vytvoří nový objekt BLOB při každém odeslání dat.

## <a name="custom-datetime-path-patterns"></a>Vlastní vzory cesty DateTime

Vlastní vzorce pro cestu DateTime umožňují zadat výstupní formát, který bude odpovídat konvencím pro streamování, což dává Azure Stream Analytics schopnost odesílat data do služby Azure HDInsight a Azure Databricks pro zpracování pro příjem dat. Vlastní vzorce cesty DateTime se snadno implementují pomocí `datetime` klíčového slova v poli Předpona cesty pro výstup objektu BLOB spolu s specifikátorem formátu. Například `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Podporované tokeny

Následující tokeny specifikátoru formátu lze použít samostatně nebo v kombinaci k dosažení vlastních formátů data a času:

|Specifikátor formátu   |Popis   |Výsledky pro příklad času 2018-01-02T10:06:08|
|----------|-----------|------------|
|{DateTime: rrrr}|Rok jako čtyřmístné číslo|2018|
|{DateTime: MM}|Měsíc od 01 do 12|01|
|{DateTime: M}|Měsíc od 1 do 12|1|
|{DateTime: DD}|Den od 01 do 31|02|
|{DateTime: d}|Den od 1 do 31|2|
|{DateTime: HH}|Hodiny ve 24hodinovém formátu, od 00 do 23|10|
|{DateTime: mm}|Minuty od 00 do 60|06|
|{DateTime: m}|Minuty od 0 do 60|6|
|{DateTime: SS}|Sekundy od 00 do 60|08|

Pokud nechcete používat vlastní vzory DateTime, můžete přidat token {Date} a/nebo {Time} k předponě cesty pro vygenerování rozevíracího seznamu s vestavěnými formáty data a času.

![Stream Analytics staré formáty data a času](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>Rozšiřitelnost a omezení

Můžete použít tolik tokenů, `{datetime:<specifier>}` jak budete chtít ve vzoru cesty, dokud nedosáhnete limitu znaků předpony cesty. Specifikátory formátu nelze kombinovat v rámci jednoho tokenu nad kombinacemi, které jsou již uvedeny v rozevíracích seznamech data a času. 

Pro oddíl cesty `logs/MM/dd` :

|Platný výraz   |Neplatný výraz   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

V předponě cesty můžete několikrát použít stejný specifikátor formátu. Token se musí pokaždé zopakovat.

### <a name="hive-streaming-conventions"></a>Konvence streamování podregistru

Vlastní vzory cest pro úložiště objektů BLOB lze použít s konvencí pro streamování podregistru, která očekává, že složky budou označeny `column=` v názvu složky.

Například `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Vlastní výstup eliminuje nepříjemnosti při změnách tabulek a Ruční přidávání oddílů do dat portů mezi Azure Stream Analytics a podregistru. Místo toho je možné automaticky přidat mnoho složek pomocí:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Příklad

Vytvořte účet úložiště, skupinu prostředků, úlohu Stream Analytics a vstupní zdroj podle příručky Průvodce rychlým startem pro [Azure Stream Analytics Azure Portal](stream-analytics-quick-create-portal.md) . Použijte stejná vzorová data, která se používají v průvodci rychlým startem, k dispozici také na [GitHubu](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).

Vytvořte výstupní jímku objektu BLOB s následující konfigurací:

![Stream Analytics vytvoření výstupní jímky objektu BLOB](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

Vzor úplné cesty je následující:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


Při spuštění úlohy se v kontejneru objektů BLOB vytvoří struktura složky založená na vzoru cesty. Můžete přejít k podrobnostem na úrovni dne.

![Stream Analytics výstup objektu BLOB s vlastním vzorem cesty](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Další kroky

* [Porozumění výstupům z Azure Stream Analytics](stream-analytics-define-outputs.md)
