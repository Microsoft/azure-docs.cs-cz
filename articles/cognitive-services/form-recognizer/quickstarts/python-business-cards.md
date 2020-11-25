---
title: 'Rychlý Start: extrakce dat z obchodních karet pomocí nástroje pro rozpoznávání formulářů v Pythonu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete nástroj pro rozpoznávání formulářů REST API s Pythonem k extrakci dat z obrázků vizitek v angličtině.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 67a21dd86059f6cf1f017ce3eada285d2faab1e6
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "96012420"
---
# <a name="quickstart-extract-business-card-data-using-the-form-recognizer-rest-api-with-python"></a>Rychlý Start: extrakce dat z vizitky pomocí REST API pro rozpoznávání formulářů pomocí Pythonu

V tomto rychlém startu použijete nástroj pro rozpoznávání formulářů Azure REST API s Pythonem k extrakci a identifikaci relevantních informací na obchodních kartách.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu musíte mít:
- Je nainstalovaný [Python](https://www.python.org/downloads/) (Pokud chcete spustit ukázku místně).
- Obrázek vizitky Pro tento rychlý Start můžete použít [ukázkový obrázek](../media/business-card-english.jpg) .

> [!NOTE]
> V tomto rychlém startu se používá místní soubor. Pokud chcete místo toho použít k použití image vzdáleného obchodu na vizitce, přečtěte si [referenční dokumentaci](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync).

## <a name="create-a-form-recognizer-resource"></a>Vytvoření prostředku pro rozpoznávání formulářů

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-business-card"></a>Analýza obchodní karty

Pokud chcete začít s analýzou vizitky, zavoláte rozhraní API pro **[analýzu vizitky](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)** pomocí následujícího skriptu Pythonu. Před spuštěním skriptu proveďte tyto změny:

1. Nahraďte `<endpoint>` koncovým bodem, který jste získali v rámci předplatného pro rozpoznávání formulářů.
1. Nahraďte `<path to your business card>` místní cestou k obrazu vizitky nebo PDF.
1. Nahraďte `<subscription key>` klíčem předplatného, který jste zkopírovali z předchozího kroku.
1. Nahraďte `<file type>` "image/jpeg", "image/png", "Application/PDF" nebo "image/TIFF".

    ```python
    ########### Python Form Recognizer Async Business Cards #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyze"
    source = r"<path to your business card>"
    content_type = "<file type>"
    
    headers = {
        # Request headers
        'Content-Type': content_type,
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    
    params = {
        "includeTextDetails": True  # True to output all recognized text
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. Uložte kód do souboru s příponou. py. Například *Form-Recognizer-BusinessCards.py*.
1. Otevřete okno příkazového řádku.
1. Ke spuštění ukázky na příkazovém řádku použijte příkaz `python`. Například, `python form-recognizer-businesscards.py`.

Dostanete `202 (Success)` odpověď, která obsahuje hlavičku **umístění operace** , kterou skript vytiskne do konzoly. Tato hlavička obsahuje ID výsledku, které můžete použít k dotazování na stav dlouhotrvající operace s dlouhou dobou a získání výsledků. V následujícím příkladu hodnoty řetězec následuje za `operations/` ID výsledku.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-business-card-results"></a>Získání výsledků obchodních karet

Po vyvolání rozhraní API pro **analýzu vizitky** zavoláte rozhraní API pro **[získání výsledku analýzy](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeBusinessCardResult)** pro získání stavu operace a extrahovaných dat. Do dolní části skriptu Pythonu přidejte následující kód. Použije se hodnota ID výsledku v novém volání rozhraní API. Tento skript volá rozhraní API v pravidelných intervalech, dokud nebudou k dispozici výsledky. Doporučujeme interval jednoho sekundy nebo více.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Business card results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Business card Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Uložte skript.
1. Znovu použijte `python` příkaz ke spuštění ukázky. Například, `python form-recognizer-businesscards.py`.

### <a name="examine-the-response"></a>Prozkoumání odpovědi
![Firemní karta od společnosti Contoso](../media/business-card-english.jpg)

Tato ukázka znázorňuje výstup JSON vrácený nástrojem pro rozpoznávání formulářů. Bylo zkráceno pro čtení.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-06-04T08:19:29Z",
    "lastUpdatedDateTime": "2020-06-04T08:19:35Z",
    "analyzeResult": {
        "version": "2.1.1",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel"
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.993
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

Skript vytiskne odpovědi na konzolu, dokud se nedokončí operace **analyzovat obchodní kartu** . `"readResults"`Uzel obsahuje veškerý rozpoznaný text. Text je uspořádán podle stránky, potom podle řádku, podle jednotlivých slov. `"documentResults"`Uzel obsahuje hodnoty specifické pro obchodní karty, které model zjistil. Tady najdete užitečné kontaktní údaje, jako je název společnosti, jméno, příjmení, telefonní číslo atd.


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste pomocí nástroje pro rozpoznávání formulářů REST API s Pythonem rozbalíte obsah vizitky. Dále si přečtěte referenční dokumentaci a prozkoumejte rozhraní API pro rozpoznávání formulářů ve větší hloubkě.

> [!div class="nextstepaction"]
> [Referenční dokumentace REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
