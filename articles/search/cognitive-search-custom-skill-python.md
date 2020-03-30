---
title: Příklad vlastní dovednosti (Python)
titleSuffix: Azure Cognitive Search
description: Pro vývojáře Pythonu se naučte nástroje a techniky pro vytváření vlastních dovedností pomocí Azure Functions a Visual Studia. Vlastní dovednosti obsahují uživatelem definované modely nebo logiku, kterou můžete přidat do kanálu indexování obohaceného umělou ai v Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: fc69761a05ea381d39d58d5ebf0046e0d9874961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77210461"
---
# <a name="example-create-a-custom-skill-using-python"></a>Příklad: Vytvoření vlastní dovednosti pomocí Pythonu

V tomto příkladu dovedností Azure Cognitive Search se dozvíte, jak vytvořit vlastní dovednost webového rozhraní API pomocí pythonu a kódu Visual Studia. Příklad používá [funkci Azure,](https://azure.microsoft.com/services/functions/) která implementuje [vlastní rozhraní dovedností](cognitive-search-custom-skill-interface.md).

Vlastní dovednost je jednoduchá podle návrhu (zřetězí dva řetězce), takže se můžete soustředit na nástroje a technologie používané pro vlastní rozvoj dovedností v Pythonu. Jakmile uspějete s jednoduchou dovedností, můžete se rozdělit pomocí složitějších scénářů.

## <a name="prerequisites"></a>Požadavky

+ Zkontrolujte [vlastní rozhraní dovedností](cognitive-search-custom-skill-interface.md) pro úvod do vstupní ho výstupního rozhraní, které by měly implementace vlastní dovednosti.

+ Nastavte prostředí. Sledovali jsme [tento kurz end-to-end](https://docs.microsoft.com/azure/python/tutorial-vs-code-serverless-python-01) nastavit funkci Azure bez serveru pomocí Visual Studio Code a Python rozšíření. Tento kurz vás provede instalací následujících nástrojů a komponent: 

  + [Python 3,75](https://www.python.org/downloads/release/python-375/)
  + [Kód visual studia](https://code.visualstudio.com/)
  + [Rozšíření Pythonu pro kód Visual Studia](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  + [Základní nástroje Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-run-local#v2)
  + [Rozšíření Azure Functions pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## <a name="create-an-azure-function"></a>Vytvoření funkce Azure

Tento příklad používá funkci Azure k předvedení konceptu hostování webového rozhraní API, ale jsou možné i jiné přístupy. Tak dlouho, jak budete splňovat [požadavky na rozhraní pro kognitivní dovednosti](cognitive-search-custom-skill-interface.md), přístup, který budete mít, je nepodstatné. Azure Functions však usnadňují vytvoření vlastní chod.

### <a name="create-a-function-app"></a>Vytvoření Function App

Šablona projektu Azure Functions ve Visual Studio Code vytvoří projekt, který jde publikovat do aplikace funkcí v Azure. Aplikace funkcí umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků.

1. V kódu sady Visual Studio otevřete paletu příkazů stisknutím klávesy F1. V paletě příkazů vyhledejte `Azure Functions: Create new project...`a vyberte .

1. Zvolte umístění adresáře pro pracovní prostor projektu a zvolte **Vybrat**.

    > [!NOTE]
    > Tyto kroky byly navrženy tak, aby byly dokončeny mimo pracovní prostor. Z tohoto důvodu nevybírejte složku projektu, která je součástí pracovního prostoru.

1. Vyberte jazyk pro projekt aplikace funkce. V tomto kurzu vyberte **Python**.
1. Vyberte verzi Pythonu (verze 3.7.5 je podporovaná funkcemi Azure)
1. Vyberte šablonu pro první funkci projektu. Vyberte **aktivační událost HTTP,** chcete-li v nové aplikaci funkce vytvořit funkci spuštěnou protokolem HTTP.
1. Zadejte název funkce. V tomto případě použijeme **Concatenator** 
1. Jako úroveň Autorizace vyberte **Funkce.** To znamená, že poskytneme [funkční klávesu](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) pro volání koncového bodu HTTP funkce. 
1. Vyberte, jak chcete projekt otevřít. V tomto kroku vyberte **Přidat do pracovního prostoru** a vytvořte aplikaci funkcí v aktuálním pracovním prostoru.

Visual Studio Code vytvoří projekt aplikace funkcí v novém pracovním prostoru. Tento projekt obsahuje konfigurační soubory [host.json](../azure-functions/functions-host-json.md) a [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file) a navíc všechny soubory projektu pro konkrétní jazyk. 

Nová funkce spuštěná http je také vytvořena ve složce **Concatenator** projektu aplikace funkce. Uvnitř bude soubor s názvem\_\_"init__.py", s tímto obsahem:

```py
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )

```

Nyní upravíme tento kód tak, aby se řídil [vlastním rozhraním dovedností](cognitive-search-custom-skill-interface.md)). Upravte kód s následujícím obsahem:

```py
import logging
import azure.functions as func
import json

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    try:
        body = json.dumps(req.get_json())
    except ValueError:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )
    
    if body:
        result = compose_response(body)
        return func.HttpResponse(result, mimetype="application/json")
    else:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )


def compose_response(json_data):
    values = json.loads(json_data)['values']
    
    # Prepare the Output before the loop
    results = {}
    results["values"] = []
    
    for value in values:
        output_record = transform_value(value)
        if output_record != None:
            results["values"].append(output_record)
    return json.dumps(results, ensure_ascii=False)

## Perform an operation on a record
def transform_value(value):
    try:
        recordId = value['recordId']
    except AssertionError  as error:
        return None

    # Validate the inputs
    try:         
        assert ('data' in value), "'data' field is required."
        data = value['data']        
        assert ('text1' in data), "'text1' field is required in 'data' object."
        assert ('text2' in data), "'text2' field is required in 'data' object."
    except AssertionError  as error:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Error:" + error.args[0] }   ]       
            })

    try:                
        concatenated_string = value['data']['text1'] + " " + value['data']['text2']  
        # Here you could do something more interesting with the inputs

    except:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Could not complete operation for record." }   ]       
            })

    return ({
            "recordId": recordId,
            "data": {
                "text": concatenated_string
                    }
            })
```

Metoda **transform_value** provádí operaci s jedním záznamem. Můžete upravit metodu tak, aby vyhovovala vašim specifickým potřebám. Nezapomeňte provést všechny potřebné vstupní ověření a vrátit všechny chyby a upozornění vyrobené, pokud operaci nelze dokončit pro záznam.

### <a name="debug-your-code-locally"></a>Ladění kódu místně

Visual Studio Kód usnadňuje ladění kódu. Stiskněte 'F5' nebo přejděte do nabídky **Ladění** a vyberte **Spustit ladění**.

Můžete nastavit všechny zarážky na kód stisknutím 'F9' na řádku zájmu.

Jakmile začnete ladění, vaše funkce se spustí místně. Můžete použít nástroj, jako je Pošťák nebo Šumař k vydání požadavku localhost. Poznamenejte si umístění místního koncového bodu v okně terminálu. 

## <a name="publish-your-function"></a>Publikování funkce

Když jste spokojeni s chováním funkce, můžete ji publikovat.

1. V kódu sady Visual Studio otevřete paletu příkazů stisknutím klávesy F1. V paletě příkazů vyhledejte a vyberte **Nasadit do funkční aplikace...**. 

1. Vyberte předplatné Azure, kde chcete nasadit vaši aplikaci.

1. Vybrat **+ Vytvořit novou aplikaci funkcí v Azure**

1. Zadejte globálně jedinečný název aplikace pro funkci.

1. Vyberte verzi Pythonu (Python 3.7.x funguje pro tuto funkci).

1. Vyberte umístění nového zdroje (například Západní USA 2).

V tomto okamžiku se v rámci předplatného Azure vytvoří potřebné prostředky pro hostování nové funkce Azure v Azure. Počkejte, než se nasazení dokončí. Ve výstupním okně se zobrazí stav procesu nasazení.

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na **všechny prostředky** a vyhledejte funkci, kterou jste publikovali podle jeho názvu. Pokud jste jej **pojmenovali Concatenator**, vyberte zdroj.

1. Klikněte na tlačítko **URL funkce</>.** To vám umožní zkopírovat adresu URL pro volání funkce.

## <a name="test-the-function-in-azure"></a>Testování funkce v Azure

Nyní, když máte výchozí klíč hostitele, otestujte svou funkci následujícím způsobem:

```http
POST [Function URL you copied above]
```

### <a name="request-body"></a>Text žádosti
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "text1":  "Hello",
                "text2":  "World"
            }
        },
        {
            "recordId": "e2",
            "data": "This is an invalid input"
        }
    ]
}
```

Tento příklad by měl způsobit stejný výsledek, který jste viděli dříve při spuštění funkce v místním prostředí.

## <a name="connect-to-your-pipeline"></a>Připojení k potrubí

Nyní, když máte novou vlastní dovednost, můžete ji přidat do své dovednosti. Následující příklad ukazuje, jak nazvat dovednost zřetězit název a autora dokumentu do jednoho pole, které nazýváme merged_title_author. Nahraďte `[your-function-url-here]` ji adresou URL nové funkce Azure.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new search custom skill",
        "uri": "https://[your-function-url-here]",        
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "text1",
              "source": "/document/metadata_title"
            },
            {
              "name": "text2",
              "source": "/document/metadata_author"
            },
          ],
          "outputs": [
            {
              "name": "text",
              "targetName": "merged_title_author"
            }
          ]
      }
  ]
}
```

## <a name="next-steps"></a>Další kroky
Blahopřejeme! Vytvořili jste si svou první vlastní dovednost. Nyní můžete postupovat podle stejného vzoru a přidat vlastní funkce. Další informace najdete na následujících odkazech.

+ [Power Skills: úložiště vlastních dovedností](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Přidání vlastní chudinské dovednosti do kanálu obohacení umělou ai.](cognitive-search-custom-skill-interface.md)
+ [Jak definovat sadu dovedností](cognitive-search-defining-skillset.md)
+ [Vytvořit sadu dovedností (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Jak mapovat obohacená pole](cognitive-search-output-field-mapping.md)
