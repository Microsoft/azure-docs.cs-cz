---
title: Datum a čas vzorů cest pro Azure Stream Analytics blob výstup (Preview)
description: Tento článek popisuje funkce vzory cesta vlastní data a času pro výstup úložiště objektů blob z úloh Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: ba386539c3f3c6740b843575bbccd4b028b8a5a7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090774"
---
# <a name="custom-datetime-path-patterns-for-azure-stream-analytics-blob-storage-output-preview"></a>Vlastní data a času vzorů cest pro Azure Stream Analytics služby blob storage výstup (Preview)

Azure Stream Analytics podporuje vlastní datum a čas specifikátory formátu v cestě k souboru pro výstupy úložiště objektů blob. Vlastní data a času vzorů cest vám umožňují určit výstupní formát, který souladu s konvencemi Hive streamování, že umožňuje posílat data do Azure HDInsight a Azure Databricks pro směru server-klient zpracování Azure Stream Analytics. Vlastní data a času vzorů cest se snadno implementují pomocí `datetime` – klíčové slovo v poli Předpona cesty objektu výstup, spolu s specifikátor formátu. Například, `{datetime:yyyy}`.

Pomocí tohoto odkazu pro [webu Azure Portal](https://portal.azure.com/?Microsoft_Azure_StreamAnalytics_bloboutputcustomdatetimeformats=true) Chcete-li přepnout příznak funkce, který umožňuje vlastní vzorů cest datum a čas pro náhled výstupu úložiště objektů blob. Tato funkce bude brzy povolená hlavní portálu.

## <a name="supported-tokens"></a>Podporované tokeny

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

## <a name="extensibility-and-restrictions"></a>Rozšíření a omezení

Můžete použít libovolný počet tokenů `{datetime:<specifier>}`, jak potřebujete v vzor cesty, dokud se nedostanete limit počtu znaků předpona cesty. Specifikátory formátu nejde kombinovat v rámci jednoho tokenu nad rámec kombinace již uveden v rozevíracích seznamech data a času. 

Pro rozdělení cesta `logs/MM/dd`:

|Platný výraz   |Neplatný výraz   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Můžete použít stejné specifikátor formátu více než jednou v předpona cesty. Pokaždé, když musíte opakovat token.

## <a name="hive-streaming-conventions"></a>Vytváření datových proudů Hive

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
