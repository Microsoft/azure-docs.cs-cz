---
title: 'Rychlý Start: extrakce dat z obchodních karet pomocí nástroje pro rozpoznávání formulářů v Pythonu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete nástroj pro rozpoznávání formulářů REST API s Pythonem k extrakci dat z obrázků vizitek v angličtině.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 45e091fe1ed77a4efc90d426b1d9a2842ae00175
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2020
ms.locfileid: "88725444"
---
# <a name="quickstart-extract-business-card-data-using-the-form-recognizer-rest-api-with-python"></a>Rychlý Start: extrakce dat z vizitky pomocí REST API pro rozpoznávání formulářů pomocí Pythonu

V tomto rychlém startu použijete nástroj pro rozpoznávání formulářů Azure REST API s Pythonem k extrakci a identifikaci relevantních informací na obchodních kartách.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto rychlého startu musíte mít:
- Je nainstalovaný [Python](https://www.python.org/downloads/) (Pokud chcete spustit ukázku místně).
- Adresa URL obrázku vizitky Pro tento rychlý Start můžete použít [ukázkový obrázek](../media/business-card-english.jpg) .

> [!NOTE]
> V tomto rychlém startu se používá k získání obrázku na vzdálené vizitce z adresy URL. Chcete-li místo toho použít místní soubory, přečtěte si [referenční dokumentaci](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync).

## <a name="create-a-form-recognizer-resource"></a>Vytvoření prostředku pro rozpoznávání formulářů

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-business-card"></a>Analýza obchodní karty

Pokud chcete začít s analýzou vizitky, zavoláte rozhraní API pro **[analýzu vizitky](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)** pomocí následujícího skriptu Pythonu. Před spuštěním skriptu proveďte tyto změny:

1. Nahraďte `<Endpoint>` koncovým bodem, který jste získali v rámci předplatného pro rozpoznávání formulářů.
1. Nahraďte `<your business card URL>` adresou URL obrázku vizitky.
1. Nahraďte `<subscription key>` klíčem předplatného, který jste zkopírovali z předchozího kroku.

    ```python
    ########### Python Form Recognizer Async Business cards #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyzeresults"
    source = r"<path to your business card>"
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    
    params = {
        "includeTextDetails": True
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

Dostanete `202 (Success)` odpověď, která obsahuje hlavičku **umístění operace** , kterou skript vytiskne do konzoly. Tato hlavička obsahuje ID operace, pomocí které můžete zadat dotaz na stav asynchronní operace a získat výsledky. V následujícím příkladu hodnoty řetězec následuje `operations/` ID operace.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyzeresults{operationID}
```

## <a name="get-the-business-card-results"></a>Získání výsledků obchodních karet

Po vyvolání rozhraní API pro **analýzu vizitky** zavoláte rozhraní API pro **[získání výsledku analýzy](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeReceiptResult)** pro získání stavu operace a extrahovaných dat. Do dolní části skriptu Pythonu přidejte následující kód. Použije se hodnota ID operace v novém volání rozhraní API. Tento skript volá rozhraní API v pravidelných intervalech, dokud nebudou k dispozici výsledky. Doporučujeme interval jednoho sekundy nebo více.

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

Skript vytiskne odpovědi na konzolu, dokud se nedokončí operace **analyzovat obchodní kartu** . Pak budou ve formátu JSON vytištěna extrahovaná textová data. `"recognitionResults"`Pole obsahuje všechny řádky textu, které byly extrahovány z vizitky, a `"understandingResults"` pole obsahuje informace o klíč/hodnotě pro nejrelevantnější části vizitky.

![Firemní karta od společnosti Contoso](../media/business-card-english.jpg)

`"recognitionResults"`Uzel obsahuje veškerý rozpoznaný text. Text je uspořádán podle stránky, potom podle řádku, podle jednotlivých slov. `"understandingResults"`Uzel obsahuje hodnoty specifické pro obchodní karty, které model zjistil. Tady najdete užitečné páry klíč/hodnota, jako je daň, celková, obchodní adresa atd.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste pomocí nástroje pro rozpoznávání formulářů REST API s Pythonem rozbalíte obsah vizitky. Dále si přečtěte referenční dokumentaci a prozkoumejte rozhraní API pro rozpoznávání formulářů ve větší hloubkě.

> [!div class="nextstepaction"]
> [Referenční dokumentace REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)
