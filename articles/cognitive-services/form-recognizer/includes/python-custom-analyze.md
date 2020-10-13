---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 262774233871a46cf971d95a6cf5d810adf9746a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961781"
---
## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Analýza formulářů pro páry klíč-hodnota a tabulky

V dalším kroku použijete svůj nově vyškolený model k analýze dokumentu a extrakci párů klíč-hodnota a tabulek z něj. Volejte rozhraní API pro **[analýzu formuláře](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** spuštěním následujícího kódu v novém skriptu Pythonu. Před spuštěním skriptu proveďte tyto změny:

1. Nahraďte `<file path>` cestou k souboru vašeho formuláře (například C:\temp\file.pdf). Může to být také adresa URL vzdáleného souboru. Pro účely tohoto rychlého startu můžete použít soubory ve složce **test** [ukázkové sady dat](https://go.microsoft.com/fwlink/?linkid=2090451) (stažení a extrakce *sample_data.zip*).
1. Nahraďte `<model_id>` ID modelu, které jste obdrželi v předchozí části.
1. Nahraďte `<endpoint>` koncovým bodem, který jste získali pomocí klíče předplatného pro rozpoznávání formulářů. Můžete ji najít na kartě **Přehled** prostředků nástroje pro rozpoznávání formulářů.
1. Nahraďte `<file type>` typem souboru. Podporované typy: `application/pdf` , `image/jpeg` , `image/png` , `image/tiff` .
1. Místo `<subscription key>` použijte váš klíč předplatného.

    # <a name="v20"></a>[v2.0](#tab/v2-0)
    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"<file path>"
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```    
    # <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)
    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.1/custom/models/%s/analyze" % model_id
    source = r"<file path>"
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```    


    ---



1. Uložte kód do souboru s příponou. py. Například *Form-Recognizer-Analyze.py*.
1. Otevřete okno příkazového řádku.
1. Ke spuštění ukázky na příkazovém řádku použijte příkaz `python`. Například, `python form-recognizer-analyze.py`.

Při volání rozhraní API pro **analýzu formuláře** obdržíte `201 (Success)` odpověď s hlavičkou **umístění operace** . Hodnota tohoto záhlaví je ID, které použijete ke sledování výsledků operace analyzovat. Skript výše vytiskne hodnotu této hlavičky do konzoly.

## <a name="get-the-analyze-results"></a>Získat výsledky analýzy

Do dolní části skriptu Pythonu přidejte následující kód. Tato hodnota používá hodnotu ID z předchozího volání v novém volání rozhraní API k načtení výsledků analýzy. Operace **formuláře analýzy** je asynchronní, takže tento skript volá rozhraní API v pravidelných intervalech, dokud nebudou k dispozici výsledky. Doporučujeme interval jednoho sekundy nebo více.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET analyze results failed:\n%s" % json.dumps(resp_json))
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Analysis succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % json.dumps(resp_json))
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
print("Analyze operation did not complete within the allocated time.")
```
