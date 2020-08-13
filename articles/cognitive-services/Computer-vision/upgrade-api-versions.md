---
title: Upgrade na verzi v 3.0 rozhraní API pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak upgradovat z verze 2.0 a v 2.1 na v 3.0 rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: 16add0dce88d0f809dc291d3c9de33e1a853f257
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136500"
---
# <a name="upgrade-to-v30-of-computer-vision-api-from-v20-and-v21"></a>Upgrade na verzi v 3.0 rozhraní API pro počítačové zpracování obrazu z verze 2.0 a v 2.1

Tato příručka ukazuje, jak upravit existující kód pro migraci z verze v 2.0 nebo v 2.1 rozhraní API pro počítačové zpracování obrazu do v 3.0 pro uživatele REST API. 

## <a name="upgrade-batch-read-file-to-read"></a>Upgradovat `Batch Read File` na`Read`


1. Změňte cestu rozhraní API `Batch Read File` 2. x následujícím způsobem:


    |Číst 2. x |Přečíst 3,0  |
    |----------|-----------|
    |https://{Endpoint}/Vision/**v 2.0/čtení/jádro/asyncBatchAnalyze**     |https://{Endpoint}/Vision/**v 3.0/číst/analyzovat**[? jazyk]|
    
    K dispozici je nový volitelný parametr _jazyka_ . Pokud neznáte jazyk dokumentu, nebo může být vícejazyčný, nezahrnujte ho. 

2. Změňte cestu rozhraní API pro `Get Read Results` v 2. x následujícím způsobem:

    |Číst 2. x |Přečíst 3,0  |
    |----------|-----------|
    |https://{Endpoint}/Vision/**v 2.0**/Read/**Operations**/{operationId}     |https://{Endpoint}/Vision/**v 3.0**/Read/**analyzeResults**/{operationId}|

3. Změňte kód pro kontrolu výsledků JSON z `Get Read Operation Result` . Pokud `Get Read Operation Result` je volání úspěšné, vrátí pole stavového řetězce v těle JSON. Následující hodnoty z verze 2.0 byly změněny, aby lépe odpovídaly ostatním rozhraním REST API služby pro rozpoznávání. 
 
    |Číst 2. x |Přečíst 3,0  |
    |----------|-----------|
    |`"NotStarted"` |   `"notStarted"`|
    |`"Running"` | `"running"`|
    |`"Failed"` | `"failed"`|
    |`"Succeeded"` | `"succeeded"`|
    
4. Změňte kód pro interpretaci konečného výsledku rozpoznávání JSON z `Get Read Operation Result` . 

    Všimněte si následujících změn ve formátu JSON:
    
    - V v2. x `"Get Read Operation Result"` vrátí JSON pro rozpoznávání OCR, pokud je stav `"Succeeded"` . V v 3.0 je toto pole `"succeeded"` .
    - Chcete-li získat kořen pro pole stránky, změňte hierarchii JSON z `"recognitionResults"` na `"analyzeResult"` / `"readResults"` . Hierarchie JSON řádku a slov nezůstane beze změny, takže se nevyžadují žádné změny kódu.
    -   Úhel stránky byl `"clockwiseOrientation"` přejmenován na `"angle"` a rozsah byl změněn z 0-360 stupňů na-180 na 180 stupňů. V závislosti na vašem kódu může nebo nemusí být nutné provádět změny, protože většina matematických funkcí může zvládnout kterýkoli rozsah.
    -   Rozhraní API v 3.0 také zavádí následující vylepšení, která můžete volitelně využít:- `"createdDateTime"` a, `"lastUpdatedDateTime"` abyste mohli sledovat dobu trvání zpracování. Další podrobnosti najdete v dokumentaci. 
        - `"version"`oznamuje verzi rozhraní API používaného k vygenerování výsledků.
        - Bylo přidáno jednotlivé slovo `"confidence"` . Tato hodnota se kalibruje tak, že hodnota 0,95 znamená, že se rozpoznávání vyřeší na 95%. Skóre spolehlivosti lze použít k výběru textu, který se má odeslat do lidské recenze. 
    
    
        Ve 2. X je výstupní formát následující: 
    
    ```json
    {
        {
                "status": "Succeeded",
                "recognitionResults": [
                    {
                    "page": 1,
                    "language": "en",
                    "clockwiseOrientation": 349.59,
                    "width": 2661,
                    "height": 1901,
                    "unit": "pixel",
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
    ```
    
    V 3.0 bylo upraveno:
    
    ```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "language": "en",
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
    ```

## <a name="upgrade-from-recognize-text-to-read"></a>Upgrade z `Recognize Text` na verzi`Read`
`Recognize Text`je operace *verze Preview* , která se *už nepoužívá ve všech verzích rozhraní API pro počítačové zpracování obrazu*. Je nutné provést migraci z nástroje `Recognize Text` na `Read` verzi (v 3.0) nebo `Batch Read File` (v 2.0, v 2.1). v 3.0 `Read` obsahuje novější, lepší modely pro rozpoznávání textu a další funkce, takže se doporučuje. Upgrade z `Recognize Text` na verzi `Read` :

1. Cestu rozhraní API pro `Recognize Text` v2. x změňte takto:


    |Rozpoznávání textu 2. x |Přečíst 3,0  |
    |----------|-----------|
    |https://{Endpoint}/Vision/**v 2.0/recognizeText [? Mode]**|https://{Endpoint}/Vision/**v 3.0/číst/analyzovat**[? jazyk]|
    
    Parametr _Mode_ není v nástroji podporován `Read` . Rukopisný i vytištěný text bude automaticky podporován.
    
    Nový volitelný parametr _jazyka_ je k dispozici v v 3.0. Pokud neznáte jazyk dokumentu, nebo může být vícejazyčný, nezahrnujte ho. 

2. Cestu rozhraní API pro `Get Recognize Text Operation Result` v2. x změňte takto:

    |Rozpoznávání textu 2. x |Přečíst 3,0  |
    |----------|-----------|
    |https://{Endpoint}/Vision/**v 2.0/textOperations/**{operationId}|https://{Endpoint}/Vision/**v 3.0/Read/analyzeResults**/{operationId}|

3. Změňte kód pro kontrolu výsledků JSON z `Get Recognize Text Operation Result` . Pokud `Get Read Operation Result` je volání úspěšné, vrátí pole stavového řetězce v těle JSON. 
 
    |Rozpoznávání textu 2. x |Přečíst 3,0  |
    |----------|-----------|
    |`"NotStarted"` |   `"notStarted"`|
    |`"Running"` | `"running"`|
    |`"Failed"` | `"failed"`|
    |`"Succeeded"` | `"succeeded"`|


4. Změňte kód pro interpretaci finálního výsledku rozpoznávání JSON z `Get Recognize Text Operation Result` na podporu `Get Read Operation Result` .

    Všimněte si následujících změn ve formátu JSON:    

    - V v2. x `"Get Read Operation Result"` vrátí JSON pro rozpoznávání OCR, pokud je stav `"Succeeded"` . V v 3.0 je toto pole `"succeeded"` .
    - Chcete-li získat kořen pro pole stránky, změňte hierarchii JSON z `"recognitionResult"` na `"analyzeResult"` / `"readResults"` . Hierarchie JSON řádku a slov nezůstane beze změny, takže se nevyžadují žádné změny kódu.
    -   Rozhraní API v 3.0 také zavádí následující vylepšení, která můžete volitelně využít. Další podrobnosti najdete v referenčních informacích k rozhraní API:- `"createdDateTime"` a `"lastUpdatedDateTime"` jsou přidané, abyste mohli sledovat dobu trvání zpracování. Další podrobnosti najdete v dokumentaci. 
        - `"version"`oznamuje verzi rozhraní API používaného k vygenerování výsledků.
        - Bylo přidáno jednotlivé slovo `"confidence"` . Tato hodnota se kalibruje tak, že hodnota 0,95 znamená, že se rozpoznávání vyřeší na 95%. Skóre spolehlivosti lze použít k výběru textu, který se má odeslat do lidské recenze. 
        - `"angle"`Obecná orientace textu ve směru po směru hodinových ručiček měřená ve stupních mezi (-180, 180].
        -  `"width"`a `"height"` Poskytněte vám rozměry dokumentu a `"unit"` poskytuje jednotky (v pixelech nebo palcích) v závislosti na typu dokumentu.
        - `"page"`vícestránkové dokumenty jsou podporovány.
        - `"language"`vstupní jazyk dokumentu (z volitelného parametru _jazyka_ )


    Ve 2. X je výstupní formát následující: 
    
    ```json
    {
        {
                "status": "Succeeded",
                "recognitionResult": [
                    {
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
    ```
    
    V 3.0 bylo upraveno:
    
    ```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
    ```
    
## <a name="all-other-operations"></a>Všechny ostatní operace

Mezi v2 neexistují žádné další zásadní změny. X a v 3.0 rozhraní API pro počítačové zpracování obrazu. Můžete jednoduše upravit cestu k rozhraní API, kterou chcete nahradit `v2.0` `v3.0` .