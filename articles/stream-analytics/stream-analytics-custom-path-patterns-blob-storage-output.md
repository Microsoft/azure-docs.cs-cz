---
title: Azure Stream Analytics vlastní dělení výstupu objektů BLOB
description: Tento článek popisuje vlastní vzorce cesty DateTime a funkce vlastního pole nebo atributů pro výstup služby Blob Storage z Azure Stream Analytics úloh.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: e978771eaafafe4120f9eec802525c293fb9c7c9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426389"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Azure Stream Analytics vlastní dělení výstupu objektů BLOB

Azure Stream Analytics podporuje vytváření oddílů vlastního výstupu objektů BLOB s vlastními poli nebo atributy a vlastními vzory cesty DateTime. 

## <a name="custom-field-or-attributes"></a>Vlastní pole nebo atributy

Vlastní pole nebo vstupní atributy zlepšují pracovní postupy pro zpracování dat a vytváření sestav tím, že umožňují větší kontrolu nad výstupem.

### <a name="partition-key-options"></a>Možnosti klíče oddílu

Klíč oddílu nebo název sloupce, který se používá k dělení vstupních dat, můžou obsahovat alfanumerické znaky s pomlčkami, podtržítky a mezerami. Pokud se nepoužívají ve spojení s aliasy, není možné použít vnořená pole jako klíč oddílu. Klíč oddílu musí být NVARCHAR (MAX).

### <a name="example"></a>Příklad:

Předpokládejme, že úloha přebírá vstupní data z živých uživatelských relací připojených ke službě externí videohry, kde ingestovaná data obsahují sloupec **client_id** k identifikaci relací. Při vytváření oddílů dat **client_id**nastavte pole vzor cesty objektů BLOB tak, aby zahrnovalo token oddílu **{client_id}** v výstupních vlastnostech objektu BLOB při vytváření úlohy. Jelikož data s různými **client_idmi** hodnotami přecházejí prostřednictvím úlohy Stream Analytics, výstupní data se ukládají do samostatných složek na základě jedné **client_id** hodnoty na složku.

![Vzor cesty s ID klienta](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

Podobně platí, že pokud byla vstupem úlohy senzorická data z milionů senzorů, ve kterých měl každý senzor **sensor_id**, bude vzor cesty **{sensor_id}** rozdělit jednotlivé údaje ze senzorů do různých složek.  


Pomocí REST API oddíl Output souboru JSON, který se používá pro tento požadavek, může vypadat takto:  

![Výstup REST API](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

Po spuštění úlohy může kontejner *klienti* vypadat takto:  

![Kontejner klientů](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Každá složka může obsahovat několik objektů blob, kde každý objekt BLOB obsahuje jeden nebo víc záznamů. V předchozím příkladu je ve složce označené jako "06000000" jeden objekt BLOB s následujícím obsahem:

![Obsah objektu BLOB](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Všimněte si, že každý záznam v objektu BLOB má **client_id** sloupec odpovídající názvu složky, protože sloupec použitý k vytvoření oddílu výstupu ve výstupní cestě byl **client_id**.

### <a name="limitations"></a>Omezení

1. Ve vlastnosti Output objektu BLOB vzoru cesty je povolený jenom jeden vlastní klíč oddílu. Všechny následující vzory cest jsou platné:

   * cluster1/{date}/{aFieldInMyData}  
   * Cluster1/{Time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * Cluster1/{Date}/{Time}/{aFieldInMyData} 
   
2. V klíčích oddílů se nerozlišují malá a velká písmena, takže klíče oddílů jako Jan a Jan jsou ekvivalentní. Výrazy se navíc nedají použít jako klíče oddílu. Například **{Column + columnB}** nefunguje.  

3. Když vstupní datový proud sestává z záznamů s mohutnosti klíče oddílu v 8000, záznamy se připojí k existujícím objektům blob a v případě potřeby vytvoří jenom nové objekty blob. Pokud mohutnost překračuje 8000, nebudou se do nich zapisovat existující objekty BLOB a nevytvoří se nové objekty blob pro libovolný počet záznamů se stejným klíčem oddílu.

## <a name="custom-datetime-path-patterns"></a>Vlastní vzory cesty DateTime

Vlastní data a času vzorů cest vám umožňují určit výstupní formát, který souladu s konvencemi Hive streamování, že umožňuje posílat data do Azure HDInsight a Azure Databricks pro směru server-klient zpracování Azure Stream Analytics. Vlastní data a času vzorů cest se snadno implementují pomocí `datetime` – klíčové slovo v poli Předpona cesty objektu výstup, spolu s specifikátor formátu. Například, `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Podporované tokeny

Následující klíčová slova specifikátoru formátu lze použít samostatně nebo v kombinaci k dosažení vlastní formáty data a času:

|Specifikátor formátu   |Popis   |Výsledky v příkladu time 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datetime:yyyy}|Rok jako čtyřmístné číslo|2018|
|{datetime:MM}|Měsíc od 01 do 12|01|
|{datetime:M}|Měsíc od 1 do 12|1\. místo|
|{datetime:dd}|Den od 01 do 31.|02|
|{datetime:d}|Den od 1 do 12|2|
|{datetime:HH}|Pomocí 24 h formátu, od 00 do 23 hodin|10|
|{datetime:mm}|Minut od 00 do 24|06|
|{datetime:m}|Minut od 0 do 24|6|
|{datetime:ss}|Počet sekund od 00 do 60|08|

Pokud nechcete, aby využít vlastní data a času, můžete přidat {date} nebo {time} token předpona cesty ke generování rozevírací seznam s předdefinovaných formátů data a času.

![Stream Analytics staré formáty data a času](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>Rozšíření a omezení

Můžete použít libovolný počet tokenů `{datetime:<specifier>}`, jak potřebujete v vzor cesty, dokud se nedostanete limit počtu znaků předpona cesty. Specifikátory formátu nejde kombinovat v rámci jednoho tokenu nad rámec kombinace již uveden v rozevíracích seznamech data a času. 

Pro rozdělení cesta `logs/MM/dd`:

|Platný výraz   |Neplatný výraz   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Můžete použít stejné specifikátor formátu více než jednou v předpona cesty. Pokaždé, když musíte opakovat token.

### <a name="hive-streaming-conventions"></a>Vytváření datových proudů Hive

Vlastní cesta vzory pro úložiště objektů blob je možné s konvence Hive datových proudů, která očekává složky, které mají být označené `column=` ve složce s názvem.

Například, `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Vlastní výstup pracně změny tabulek a ručně přidávat oddíly na port data mezi Azure Stream Analytics a Hive. Místo toho počet složek mohou být přidány automaticky pomocí:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Příklad:

Vytvoření účtu úložiště, skupinu prostředků, úlohu Stream Analytics a vstupní zdroj podle [Azure Stream Analytics Azure Portal](stream-analytics-quick-create-portal.md) příručky rychlý start. Použít stejná vzorová data používají v úvodní příručky, a také k dispozici na [Githubu](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).

Vytvoření výstupní jímky objektu blob s následující konfigurací:

![Stream Analytics vytvořit objekt blob výstupní jímky](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

Úplná cesta vzor je následujícím způsobem:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


Při spuštění úlohy se vytvoří strukturu složek podle vzor cesty v kontejnerech objektů blob. Můžete zobrazit podrobnosti na úrovni den.

![Stream Analytics blob výstup s vlastní vzorek cesty](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Další kroky

* [Vysvětlení vytvořené jako výstupy z Azure Stream Analytics](stream-analytics-define-outputs.md)
