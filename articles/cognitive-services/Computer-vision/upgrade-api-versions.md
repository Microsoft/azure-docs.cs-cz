---
title: Upgrade na verzi Read v 3.0 rozhraní API pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak upgradovat na Počítačové zpracování obrazu v 3.0 – rozhraní API pro čtení z verze 2.0/v 2.1.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: 5910c40729d07d5a759b2e5cc7b7a4272524c150
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91253849"
---
# <a name="upgrade-from-read-v2x-to-read-v3x"></a>Upgradujte ze přečtené verze v2. x, abyste si přečetli v3. x

V této příručce se dozvíte, jak upgradovat existující kontejner nebo cloudový kód rozhraní API z verze Read v2. x na verzi Read v 3.0 a v 3.1.

## <a name="determine-your-api-path"></a>Určení cesty k rozhraní API
Následující tabulku použijte k určení **řetězce verze** v cestě rozhraní API na základě verze Read 3. x, na kterou migrujete.

|Typ produktu| Verze | Řetězec verze v cestě k rozhraní API 3. x |
|:-----|:----|:----|
|Služba | Přečíst 3,0 | **v3.0** |
|Kontejner | Přečíst 3,0 Preview | **v3.0** |
|Služba/kontejner | Přečíst 3,1 Preview | **v 3.1 – Preview. 2** |

Dále pomocí následujících částí upřesněte operace a nahraďte **řetězec verze** v cestě rozhraní API hodnotou z tabulky. Například pro Cloud a verze kontejnerů **pro čtení v 3.1** si aktualizujte cestu rozhraní API na **https://{Endpoint}/Vision/v 3.1-Preview. 2/čtení/analýza [? Language]**.

## <a name="servicecontainer"></a>Služba/kontejner

### `Batch Read File`

|Číst 2. x |Přečíst 3. x  |
|----------|-----------|
|https://{Endpoint}/Vision/**v 2.0/čtení/jádro/asyncBatchAnalyze**     |https://{Endpoint}/Vision/<**řetězec verze**>/Read/Analyze [? Language]|
    
K dispozici je nový volitelný parametr _jazyka_ . Pokud neznáte jazyk dokumentu, nebo může být vícejazyčný, nezahrnujte ho. 

### `Get Read Results`

|Číst 2. x |Přečíst 3. x  |
|----------|-----------|
|https://{Endpoint}/Vision/**v 2.0/čtení/Operations**/{operationId}     |https://{Endpoint}/<**řetězec verze**>/Read/analyzeresults/{operationId}|

### <a name="get-read-operation-result-status-flag"></a>`Get Read Operation Result` příznak stavu

Pokud `Get Read Operation Result` je volání úspěšné, vrátí pole stavového řetězce v těle JSON.
 
|Číst 2. x |Přečíst 3. x  |
|----------|-----------|
|`"NotStarted"` |   `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|
    
### <a name="api-response-json"></a>Odpověď rozhraní API (JSON) 

Všimněte si následujících změn ve formátu JSON:
* V v2. x `Get Read Operation Result` vrátí JSON pro rozpoznávání OCR, pokud je stav `Succeeded"` . V v 3.0 je toto pole `succeeded` .
* Chcete-li získat kořen pro pole stránky, změňte hierarchii JSON z `recognitionResults` na `analyzeResult` / `readResults` . Hierarchie JSON řádku a slov nezůstane beze změny, takže se nevyžadují žádné změny kódu.
* Úhel stránky byl `clockwiseOrientation` přejmenován na `angle` a rozsah byl změněn z 0-360 stupňů na-180 na 180 stupňů. V závislosti na vašem kódu může nebo nemusí být nutné provádět změny, protože většina matematických funkcí může zvládnout kterýkoli rozsah.

Rozhraní API v 3.0 také zavádí následující vylepšení, která můžete volitelně využít:
* `createdDateTime` a `lastUpdatedDateTime` jsou přidány, abyste mohli sledovat dobu trvání zpracování. Další podrobnosti najdete v dokumentaci. 
* `version` oznamuje verzi rozhraní API používaného k vygenerování výsledků.
* Bylo přidáno jednotlivé slovo `confidence` . Tato hodnota se kalibruje tak, že hodnota 0,95 znamená, že se rozpoznávání vyřeší na 95%. Skóre spolehlivosti lze použít k výběru textu, který se má odeslat do lidské recenze. 
    
    
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

## <a name="service-only"></a>Pouze služba

### `Recognize Text`
`Recognize Text` je operace *verze Preview* , která se *už nepoužívá ve všech verzích rozhraní API pro počítačové zpracování obrazu*. Je nutné provést migraci z nástroje `Recognize Text` na `Read` verzi (v 3.0) nebo `Batch Read File` (v 2.0, v 2.1). v 3.0 `Read` obsahuje novější, lepší modely pro rozpoznávání textu a další funkce, takže se doporučuje. Upgrade z `Recognize Text` na verzi `Read` :

|Rozpoznávání textu 2. x |Přečíst 3. x  |
|----------|-----------|
|https://{Endpoint}/Vision/**v 2.0/recognizeText [? Mode]**|https://{Endpoint}/Vision/<**řetězec verze**>/Read/Analyze [? Language]|
    
Parametr _Mode_ není v nástroji podporován `Read` . Rukopisný i vytištěný text bude automaticky podporován.
    
Nový volitelný parametr _jazyka_ je k dispozici v v 3.0. Pokud neznáte jazyk dokumentu, nebo může být vícejazyčný, nezahrnujte ho. 

### `Get Recognize Text Operation Result`

|Rozpoznávání textu 2. x |Přečíst 3. x  |
|----------|-----------|
|https://{Endpoint}/Vision/**v 2.0/textOperations/**{operationId}|https://{Endpoint}/<**řetězec verze**>/Read/analyzeresults/{operationId}|

### <a name="get-recognize-text-operation-result-status-flags"></a>`Get Recognize Text Operation Result` příznaky stavu
Pokud `Get Recognize Text Operation Result` je volání úspěšné, vrátí pole stavového řetězce v těle JSON. 
 
|Rozpoznávání textu 2. x |Přečíst 3. x  |
|----------|-----------|
|`"NotStarted"` |   `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|

### <a name="api-response-json"></a>Odpověď rozhraní API (JSON)

Všimněte si následujících změn ve formátu JSON:    
* V v2. x `Get Read Operation Result` vrátí JSON pro rozpoznávání OCR, pokud je stav `Succeeded` . V v3. x je toto pole `succeeded` .
* Chcete-li získat kořen pro pole stránky, změňte hierarchii JSON z `recognitionResult` na `analyzeResult` / `readResults` . Hierarchie JSON řádku a slov nezůstane beze změny, takže se nevyžadují žádné změny kódu.

Rozhraní API v 3.0 také zavádí následující vylepšení, která můžete volitelně využít. Další podrobnosti najdete v referenčních informacích k rozhraní API:
* `createdDateTime` a `lastUpdatedDateTime` jsou přidány, abyste mohli sledovat dobu trvání zpracování. Další podrobnosti najdete v dokumentaci. 
* `version` oznamuje verzi rozhraní API používaného k vygenerování výsledků.
* Bylo přidáno jednotlivé slovo `confidence` . Tato hodnota se kalibruje tak, že hodnota 0,95 znamená, že se rozpoznávání vyřeší na 95%. Skóre spolehlivosti lze použít k výběru textu, který se má odeslat do lidské recenze. 
* `angle` Obecná orientace textu ve směru po směru hodinových ručiček měřená ve stupních mezi (-180, 180].
* `width` a `"height"` Poskytněte vám rozměry dokumentu a `"unit"` poskytuje jednotky (v pixelech nebo palcích) v závislosti na typu dokumentu.
* `page` vícestránkové dokumenty jsou podporovány.
* `language` vstupní jazyk dokumentu (z volitelného parametru _jazyka_ )


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
    
V v3. x byl upraven:
    
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

## <a name="container-only"></a>Pouze kontejner

### `Synchronous Read`

|Přečíst 2,0 |Přečíst 3. x  |
|----------|-----------|
|https://{Endpoint}/Vision/**v 2.0/čtení/jádro/analyzovat**     |https://{Endpoint}/Vision/<**řetězec verze**>/Read/syncanalyze [? Language]|
