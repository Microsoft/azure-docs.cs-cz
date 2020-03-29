---
title: Azure Stream Analytics vlastní dělení výstupu objektu blob
description: Tento článek popisuje vlastní vzory cesty DateTime a vlastní pole nebo atributy funkce pro výstup úložiště objektů blob z úloh Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: e978771eaafafe4120f9eec802525c293fb9c7c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426389"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Azure Stream Analytics vlastní dělení výstupu objektu blob

Azure Stream Analytics podporuje vlastní dělení výstupu objektu blob s vlastními poli nebo atributy a vlastní mise DateTime cesta vzory. 

## <a name="custom-field-or-attributes"></a>Vlastní pole nebo atributy

Vlastní pole nebo vstupní atributy zlepšují následné zpracování dat a vytváření sestav tím, že umožňují větší kontrolu nad výstupem.

### <a name="partition-key-options"></a>Možnosti klíče oddílu

Klíč oddílu nebo název sloupce používaný k rozdělení vstupních dat může obsahovat alfanumerické znaky s pomlčkami, podtržítka a mezerami. Není možné použít vnořená pole jako klíč oddílu, pokud není použit ve spojení s aliasy. Klíč oddílu musí být NVARCHAR(MAX).

### <a name="example"></a>Příklad

Předpokládejme, že úloha přebírá vstupní data z živých uživatelských relací připojených k externí službě videoher, kde příjem dat obsahuje sloupec **client_id** k identifikaci relací. Chcete-li data rozdělit podle **client_id**, nastavte pole Vzorek cesty k objektům blob tak, aby při vytváření úlohy obsahovalo token oddílu **{client_id}** ve výstupních vlastnostech objektu blob. Jak data s různými **hodnotami client_id** procházejí úlohou Stream Analytics, výstupní data se ukládají do samostatných složek na základě jedné **client_id** hodnoty na složku.

![Vzor cesty s ID klienta](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

Podobně, pokud byl vstup eminaze senzorem data z milionů senzorů, kde každý senzor měl **sensor_id**, cesta vzor by **{sensor_id}** rozdělit data senzoru do různých složek.  


Pomocí rozhraní REST API může výstupní část souboru JSON použitého pro tento požadavek vypadat takto:  

![Výstup rozhraní REST API](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

Jakmile úloha spustí, kontejner *klientů* může vypadat takto:  

![Kontejner klientů](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Každá složka může obsahovat více objektů BLOB, kde každý objekt blob obsahuje jeden nebo více záznamů. Ve výše uvedeném příkladu je jeden objekt blob ve složce označené "06000000" s následujícím obsahem:

![Obsah objektu blob](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Všimněte si, že každý záznam v objektu blob má **client_id** sloupec odpovídající názvu složky, protože sloupec použitý k rozdělení výstupu ve výstupní cestě byl **client_id**.

### <a name="limitations"></a>Omezení

1. Ve vlastnosti výstupu objektu blob cesty je povolen pouze jeden vlastní klíč oddílu. Všechny následující vzorky cesty jsou platné:

   * cluster1/{date}/{aFieldInMyData}  
   * cluster1/{time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{date}/{time}/{aFieldInMyData} 
   
2. Klíče oddílu jsou malá a velká písmena, takže klíče oddílu jako "John" a "john" jsou ekvivalentní. Výrazy také nelze použít jako klíče oddílu. Například **{columnA + columnB}** nefunguje.  

3. Pokud vstupní datový proud se skládá ze záznamů s mohutnostklíče klíče oddílu pod 8000, záznamy budou připojeny k existující objekty BLOB a pouze vytvořit nové objekty BLOB v případě potřeby. Pokud mohutnost je více než 8000 neexistuje žádná záruka, existující objekty BLOB budou zapsány do a nové objekty BLOB nebudou vytvořeny pro libovolný počet záznamů se stejným klíčem oddílu.

## <a name="custom-datetime-path-patterns"></a>Vlastní vzory cesty DateTime

Vzory vlastních cest DateTime umožňují určit výstupní formát, který je zarovnán s konvencemi streamování Hive, což dává Azure Stream Analytics možnost odesílat data do Azure HDInsight a Azure Databricks pro následné zpracování. Vlastní datatime cesta vzory jsou `datetime` snadno implementovat pomocí klíčového slova v poli předpona cesty výstupu objektu blob, spolu s specifikátorformátu. Například, `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Podporované tokeny

Následující tokeny specifikátoru formátu lze použít samostatně nebo v kombinaci k dosažení vlastní formáty DateTime:

|Specifikátor formátu   |Popis   |Výsledky na příklad čas 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datetime:yyyy}|Rok jako čtyřmístné číslo|2018|
|{datetime:MM}|Měsíc od 01 do 12|01|
|{datetime:M}|Měsíc od 1 do 12|1|
|{datetime:dd}|Den od 01 do 31|02|
|{datetime:d}|Den od 1 do 12|2|
|{datetime:HH}|Hodina ve 24hodinovém formátu od 00 do 23 hodin|10|
|{datetime:mm}|Zápisy z 00 na 24|06|
|{datetime:m}|Minuty od 0 do 24|6|
|{datetime:ss}|Sekundy od 00 do 60|08|

Pokud nechcete používat vlastní vzory DateTime, můžete přidat token {date} a/nebo {time} do předpony cesty a vygenerovat rozbalovací seznam s vestavěnými formáty DateTime.

![Stream Analytics staré formáty DateTime](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>Rozšiřitelnost a omezení

Můžete použít tolik tokenů `{datetime:<specifier>}`, , jak se vám líbí ve vzoru cesty, dokud nedosáhnete limitu znaku předpony cesty. Specifikátory formátu nelze kombinovat v rámci jednoho tokenu nad rámec kombinací, které jsou již uvedeny v rozevíracích seznamech data a času. 

Pro cestu rozdělení `logs/MM/dd`:

|Platný výraz   |Neplatný výraz   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

V předponě cesty můžete použít stejný specifikátor formátu vícekrát. Token musí být opakován pokaždé.

### <a name="hive-streaming-conventions"></a>Konvence streamování úlu

Vlastní cesty vzory pro úložiště objektů blob lze použít s Hive Streaming konvence, která očekává, že složky, které mají být označeny `column=` v názvu složky.

Například, `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Vlastní výstup eliminuje potíže se změnou tabulek a ruční přidávání oddílů do dat portů mezi Azure Stream Analytics a Hive. Místo toho lze automaticky přidat mnoho složek pomocí:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Příklad

Vytvořte účet úložiště, skupinu prostředků, úlohu Stream Analytics a vstupní zdroj podle průvodce rychlým startem [portálu Azure Portal Azure Služby Azure.](stream-analytics-quick-create-portal.md) Použijte stejná ukázková data použitá v příručce pro rychlý start, která jsou k dispozici také na [GitHubu](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).

Vytvořte jímku výstupu objektu blob s následující konfigurací:

![Analýza datového proudu vytváří jímku pro výstup objektu blob](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

Úplný vzor cesty je následující:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


Při spuštění úlohy se vytvoří struktura složek založená na vzoru cesty v kontejneru objektů blob. Můžete přejít na úroveň dne.

![Výstup objektu blob Služby Stream Analytics s vlastním vzorem cesty](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Další kroky

* [Principy výstupů z Azure Stream Analytics](stream-analytics-define-outputs.md)
