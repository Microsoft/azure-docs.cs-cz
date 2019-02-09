---
title: Azure Stream Analytics, vlastní blob výstup dělení
description: Tento článek popisuje vlastní vzorů cest data a času a vlastní atributy pole nebo funkce pro výstup úložiště objektů blob z úloh Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9cdf99884845a9cb83ac26723c3ea0e7a779ebff
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55982671"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Azure Stream Analytics, vlastní blob výstup dělení

Azure Stream Analytics podporuje dělení s vlastními poli nebo atributy a vlastní data a času vzorů cest výstupu vlastní objekt blob. 

## <a name="custom-field-or-attributes"></a>Vlastní pole nebo atributy

Vlastní pole nebo vstupní atributy zlepšit směru server-klient zpracování dat a vytváření sestav a pracovních postupů umožňující větší kontrolu nad výstup.

### <a name="partition-key-options"></a>Klíč oddílu, možnosti

Klíč oddílu, nebo název sloupce, umožňují rozdělit vstupní data mohou obsahovat alfanumerické znaky, pomlčky, podtržítka a mezery. Není možné používat vnořená pole jako klíč oddílu, není-li použít ve spojení s aliasy.

### <a name="example"></a>Příklad:

Předpokládejme, že úloha přijímá vstupní data z živé uživatelské relace, které jsou připojené ke službě videohru externí kde přijatých dat obsahuje sloupec **client_id** k identifikaci relace. Rozdělit data podle **client_id**, nastavit pole vzor cesty objektu Blob, které chcete zahrnout token oddílu **{client_id}** ve výstupní vlastnosti objektu blob při vytváření projektu. Jako data s využitím různých **client_id** hodnoty procházet skrz úlohy Stream Analytics, výstupní data se uloží do samostatné složky podle jedné **client_id** hodnotu na složku.

![Vzor cesty s id klienta](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

Podobně pokud úloha vstup byl dat snímačů ze miliony senzorů, kde má každý ze senzorů **sensor_id**, bude vzor cesty **{sensor_id}** rozdělení jednotlivých data ze senzorů do různých složek.  


Pomocí rozhraní REST API, výstupní sekce JSON soubor použitý pro tuto žádost může vypadat nějak takto:  

![Výstup rozhraní REST API](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

Po spuštění úlohy, *klienti* kontejner může vypadat třeba takto:  

![Klienti kontejneru](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Všechny složky, může obsahovat více objektů BLOB, kde každý objekt blob obsahuje jeden nebo více záznamů. V předchozím příkladu je jeden objekt blob ve složce označené "06000000" s následujícím obsahem:

![Obsah objektu BLOB](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Všimněte si, že má každý záznam v objektu blob **client_id** název sloupce odpovídající složce od sloupec použitý k rozdělení výstupu do zadané výstupní cesty **client_id**.

### <a name="limitations"></a>Omezení

1. Ve vlastnosti blob výstup vzor cesty smí obsahovat pouze jeden vlastním klíčem oddílu. Všechny následující vzorů cest, které jsou platné:

   * cluster1/{date}/{aFieldInMyData}  
   * cluster1/{time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{date}/{time}/{aFieldInMyData}  

2. Klíče oddílů jsou malá a velká písmena, takže klíče oddílů, jako je "John" a "john" jsou ekvivalentní. Výrazy navíc nelze použít jako klíče oddílu. Například **{columnA + columnB}** nefunguje.  

3. Pokud vstupní datový proud se skládá ze záznamů s kardinalitou klíče oddílu v části 8000, záznamy se připojí k existující objekty BLOB a vytvářet jenom nové objekty BLOB, pokud je to nezbytné. Pokud je Kardinalita přes 8000 neexistuje žádná záruka existující objekty BLOB se zapíšou do a nové objekty BLOB se nevytvoří pro libovolný počet záznamů se stejným klíčem oddílu.  

## <a name="custom-datetime-path-patterns"></a>Vlastní data a času vzorů cest

Vlastní data a času vzorů cest vám umožňují určit výstupní formát, který souladu s konvencemi Hive streamování, že umožňuje posílat data do Azure HDInsight a Azure Databricks pro směru server-klient zpracování Azure Stream Analytics. Vlastní data a času vzorů cest se snadno implementují pomocí `datetime` – klíčové slovo v poli Předpona cesty objektu výstup, spolu s specifikátor formátu. Například, `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Podporované tokeny

Následující klíčová slova specifikátoru formátu lze použít samostatně nebo v kombinaci k dosažení vlastní formáty data a času:

|Specifikátor formátu   |Popis   |Výsledky v příkladu time 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datetime:yyyy}|Rok jako čtyřmístné číslo|2018|
|{datetime:MM}|Měsíc od 01 do 12|01|
|{datetime:M}|Měsíc od 1 do 12|1|
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

## <a name="next-steps"></a>Další postup

* [Vysvětlení vytvořené jako výstupy z Azure Stream Analytics](stream-analytics-define-outputs.md)
