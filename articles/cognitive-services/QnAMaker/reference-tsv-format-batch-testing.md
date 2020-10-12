---
title: Dávkový test formátu TSV – QnA Maker
titleSuffix: Azure Cognitive Services
description: Vysvětlení formátu TSV pro dávkové testování
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.openlocfilehash: 0ed948b4bf69fc672a59a7825279a12868d10521
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132122"
---
# <a name="batch-testing-tsv-format"></a>Dávkové testování formátu TSV

Dávkové testování je dostupné ze [zdrojového kódu](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) nebo [spustitelného souboru zip pro stažení](https://aka.ms/qna_btzip). Formát příkazu pro spuštění dávkového testu je:

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|Očekávaná hodnota|
|--|--|
|1|název souboru TSV naformátovaného [vstupními poli TSV](#tsv-input-fields)|
|2|Identifikátor URI pro koncový bod s hostitelem ze stránky publikování na portálu QnA Maker.|
|3|KLÍČ KONCOVÉho bodu, který se našel na stránce Publikovat na portálu QnA Maker.|
|4|název souboru TSV vytvořeného dávkovým testem pro výsledky|

Následující informace použijte k pochopení a implementaci formátu TSV pro dávkové testování. 

## <a name="tsv-input-fields"></a>Vstupní pole TSV

|Pole vstupního souboru TSV|Poznámky|
|--|--|
|KBID|ID znalostní báze bylo nalezeno na stránce publikování.|
|Otázka|Otázka, kterou uživatel zadal.|
|Značky metadat|optional|
|První parametr|optional| 
|Očekávané ID odpovědi|optional|

![Vstupní formát souboru TSV pro dávkové testování.](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>Výstupní pole TSV 

|Parametry výstupního souboru TSV|Poznámky|
|--|--|
|KBID|ID znalostní báze bylo nalezeno na stránce publikování.|
|Otázka|Otázka zadaná ze vstupního souboru|
|Odpověď|Nejlepší odpověď ze znalostní báze|
|ID odpovědi|ID odpovědi|
|Skóre|Skóre předpovědi pro odpověď. |
|Značky metadat|přidruženo k vrácené odpovědi|
|Očekávané ID odpovědi|volitelné (jenom v případě, že je zadané očekávané ID odpovědi)|
|Popisek rozhodnutí|nepovinné hodnoty můžou být: správné nebo nesprávné (jenom v případě, že se zadala očekávaná odpověď).|
