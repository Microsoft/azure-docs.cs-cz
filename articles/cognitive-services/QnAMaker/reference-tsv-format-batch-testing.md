---
title: Dávkový test formátu TSV - QnA Maker
titleSuffix: Azure Cognitive Services
description: Principy formátu TSV pro dávkové testování
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: dccf034b6497651db70b6cc09fff3f1b1d645a1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73507822"
---
# <a name="batch-testing-tsv-format"></a>Dávkové testování formátu TSV

Dávkové testování je k dispozici ze [zdrojového kódu](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) nebo jako [spustitelný spustitelný zip ke stažení](https://aka.ms/qna_btzip). Formát příkazu pro spuštění dávkového testu je:

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|Očekávaná hodnota|
|--|--|
|1|název souboru tsv formátovaného [vstupními poli TSV](#tsv-input-fields)|
|2|Identifikátor URI pro koncový bod s YOUR-HOST na stránce Publikovat portálu QnA Maker.|
|3|ENDPOINT-KEY, najdete na stránce Publikovat portálu QnA Maker.|
|4|název souboru tsv vytvořeného dávkovým testem pro výsledky.|

Následující informace slouží k pochopení a implementaci formátu TSV pro dávkové testování. 

## <a name="tsv-input-fields"></a>Vstupní pole TSV

|Pole vstupního souboru TSV|Poznámky|
|--|--|
|KBID|ID KB najdete na stránce Publikovat.|
|Otázka|Otázka, kterou by uživatel zadá.|
|Značky metadat|optional|
|Horní parametr|optional| 
|ID očekávané odpovědi|optional|

![Vstupní formát souboru TSV pro dávkové testování.](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>Výstupní pole TSV 

|Parametry výstupního souboru TSV|Poznámky|
|--|--|
|KBID|ID KB najdete na stránce Publikovat.|
|Otázka|Otázka, jak byla zadána ze vstupního souboru.|
|Odpověď|Nejlepší odpověď z vaší znalostní báze.|
|ID odpovědi|ID odpovědi|
|Skóre|Predikční skóre pro odpověď. |
|Značky metadat|spojená s vrácenou odpovědí|
|ID očekávané odpovědi|volitelné (pouze v případě, že je uvedeno očekávané ID odpovědi)|
|Označení úsudku|volitelné, hodnoty mohou být: správné nebo nesprávné (pouze v případě, že je uvedena očekávaná odpověď)|
