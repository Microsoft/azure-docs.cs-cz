---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 3c6059e131eadf1144fd189c47691b2352176745
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446419"
---
## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Analýza formulářů pro dvojice a tabulky klíč-hodnota

Dále budete používat nově trénovaný model k analýze dokumentu a extrahování párů klíč-hodnota a tabulek z něj. Volání **[rozhraní Analyzovat rozhraní API formuláře](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)** spuštěním následujícího kódu v novém skriptu Pythonu. Před spuštěním skriptu proveďte tyto změny:

1. Nahraďte `<file path>` cestou k souboru formuláře (například C:\temp\file.pdf). Může se také jedná o adresu URL vzdáleného souboru. Pro tento rychlý start můžete použít soubory ve složce **Test** [ukázkové datové sady](https://go.microsoft.com/fwlink/?linkid=2090451).
1. Nahraďte `<model_id>` id modelu, které jste obdrželi v předchozí části.
1. Nahraďte `<endpoint>` koncovýbod, který jste získali pomocí klíče předplatného nástroje pro rozpoznávání formulářů. Najdete ji na kartě **Přehled** prostředků nástroje pro rozpoznávání formulářů.
1. Nahraďte `<file type>` typ souboru. Podporované `application/pdf`typy: `image/jpeg` `image/png`, `image/tiff`, , .
1. Místo `<subscription key>` použijte váš klíč předplatného.

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.0-preview/custom/models/%s/analyze" % model_id
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

1. Uložte kód do souboru s příponou Py. Například *form-recognizer-analyze.py*.
1. Otevřete okno příkazového řádku.
1. Ke spuštění ukázky na příkazovém řádku použijte příkaz `python`. Například, `python form-recognizer-analyze.py`.

Když zavoláte rozhraní ANALYZOVAT rozhraní API `201 (Success)` **formuláře,** obdržíte odpověď s hlavičkou **Operation-Location.** Hodnota tohoto záhlaví je ID, které použijete ke sledování výsledků operace Analyzovat. Výše uvedený skript vytiskne hodnotu této hlavičky do konzoly.

## <a name="get-the-analyze-results"></a>Získejte výsledky analýzy

Přidejte následující kód do dolní části skriptu Pythonu. To používá hodnotu ID z předchozího volání v nové volání rozhraní API k načtení výsledků analýzy. Operace **Analyzovat formulář** je asynchronní, takže tento skript volá rozhraní API v pravidelných intervalech, dokud nejsou k dispozici výsledky. Doporučujeme interval jedné sekundy nebo více.

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
