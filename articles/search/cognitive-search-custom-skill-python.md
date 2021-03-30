---
title: Příklad vlastní dovednosti (Python)
titleSuffix: Azure Cognitive Search
description: Pro vývojáře v Pythonu se naučíte používat nástroje a techniky pro vytváření vlastních dovedností pomocí Azure Functions a sady Visual Studio. Vlastní dovednosti obsahují uživatelsky definované modely nebo logiku, které můžete přidat do kanálu pro indexování s obohaceným AI do Azure Kognitivní hledání.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 40e20ad4bab0275b44cd868521c7dc70dec52567
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88936125"
---
# <a name="example-create-a-custom-skill-using-python"></a>Příklad: Vytvoření vlastní dovednosti pomocí Pythonu

V tomto příkladu Azure Kognitivní hledání dovednosti se dozvíte, jak vytvořit vlastní dovednost webového rozhraní API pomocí Pythonu a Visual Studio Code. V příkladu se používá [funkce Azure](https://azure.microsoft.com/services/functions/) , která implementuje [vlastní rozhraní dovedností](cognitive-search-custom-skill-interface.md).

Vlastní dovednosti jsou jednoduché v návrhu (zřetězují dva řetězce), takže se můžete soustředit na nástroje a technologie používané pro vlastní vývoj dovedností v Pythonu. Po úspěšném použití jednoduché dovednosti můžete rozvětvit složitější scénáře.

## <a name="prerequisites"></a>Požadavky

+ Projděte si [vlastní dovednostní rozhraní](cognitive-search-custom-skill-interface.md) , kde se seznámíte se vstupním a výstupním rozhraním, které by měla vlastní dovednost implementovat.

+ Nastavte své prostředí. Provedli jsme [Tento kurz kompletním](/azure/python/tutorial-vs-code-serverless-python-01) nastavením funkce Azure bez serveru pomocí rozšíření Visual Studio Code a Pythonu. Kurz vás provede instalací následujících nástrojů a součástí: 

  + [Python 3,75](https://www.python.org/downloads/release/python-375/)
  + [Visual Studio Code](https://code.visualstudio.com/)
  + [Rozšíření Pythonu pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  + [Azure Functions Core Tools](../azure-functions/functions-run-local.md#v2)
  + [Rozšíření Azure Functions pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## <a name="create-an-azure-function"></a>Vytvoření funkce Azure Function

Tento příklad používá funkci Azure k předvedení konceptu hostování webového rozhraní API, ale je možné použít i jiné přístupy. Pokud splňujete [požadavky na rozhraní pro vnímání znalostí](cognitive-search-custom-skill-interface.md), je přístup, který převezmete, nemateriálný. Azure Functions však usnadňuje vytváření vlastních dovedností.

### <a name="create-a-function-app"></a>Vytvoření aplikace funkcí

Šablona projektu Azure Functions ve Visual Studio Code vytvoří projekt, který jde publikovat do aplikace funkcí v Azure. Aplikace funkcí umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků.

1. V Visual Studio Code stisknutím klávesy F1 otevřete paletu příkazů. V paletě příkazů vyhledejte a vyberte `Azure Functions: Create new project...` .

1. Zvolte umístění adresáře pro váš pracovní prostor projektu a zvolte **Vybrat**.

    > [!NOTE]
    > Tyto kroky jsou navržené tak, aby se dokončily mimo pracovní prostor. Z tohoto důvodu nevybírejte složku projektu, která je součástí pracovního prostoru.

1. Vyberte jazyk pro projekt Function App. Pro tento kurz vyberte **Python**.
1. Vyberte verzi Pythonu (Azure Functions podporuje verze 3.7.5).
1. Vyberte šablonu pro funkci prvního projektu. Výběrem **triggeru http** vytvořte v nové aplikaci Function App funkci AKTIVOVANou protokolem HTTP.
1. Zadejte název funkce. V tomto případě použijeme **zřetězení** 
1. Jako úroveň autorizace vyberte **funkce** . To znamená, že poskytneme [klíč funkce](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) , který bude volat koncový bod HTTP funkce. 
1. Vyberte způsob, jakým chcete projekt otevřít. Pro tento krok vyberte **Přidat do pracovního prostoru** a vytvořte aplikaci Function App v aktuálním pracovním prostoru.

Visual Studio Code vytvoří projekt aplikace funkcí v novém pracovním prostoru. Tento projekt obsahuje konfigurační soubory [host.json](../azure-functions/functions-host-json.md) a [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file) a navíc všechny soubory projektu pro konkrétní jazyk. 

Ve složce **zřetězení** projektu Function App se vytvoří také nová funkce AKTIVovaná protokolem HTTP. V takovém případě bude k dispozici soubor s názvem " \_ \_ init__. py" s tímto obsahem:

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

Teď tento kód upravíte tak, aby sledoval [vlastní dovednostní rozhraní](cognitive-search-custom-skill-interface.md). Upravte kód s následujícím obsahem:

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

Metoda **transform_value** provádí operaci na jednom záznamu. Metodu lze upravit tak, aby splňovala vaše specifické potřeby. Nezapomeňte provést veškerá nutná ověření vstupu a vrátit všechny chyby a upozornění, která byla vytvořena, pokud operaci pro záznam nebylo možné dokončit.

### <a name="debug-your-code-locally"></a>Místní ladění kódu

Visual Studio Code usnadňuje ladění kódu. Stiskněte klávesu F5 nebo přejděte do nabídky **ladění** a vyberte **Spustit ladění**.

Můžete nastavit všechny zarážky v kódu tím, že na řádku zájmu zapnete "F9".

Po spuštění ladění se vaše funkce spustí místně. K vystavení žádosti hostiteli localhost můžete použít nástroj, jako je například post nebo Fiddler. Poznamenejte si umístění místního koncového bodu v okně terminálu. 

## <a name="publish-your-function"></a>Publikování funkce

Až budete s chováním funkce spokojeni, můžete ho publikovat.

1. V Visual Studio Code stisknutím klávesy F1 otevřete paletu příkazů. V paletě příkazů vyhledejte a vyberte **nasadit do Function App...**. 

1. Vyberte předplatné Azure, na které chcete aplikaci nasadit.

1. Vybrat **+ vytvořit novou Function App v Azure**

1. Zadejte globálně jedinečný název vaší aplikace Function App.

1. Vyberte verzi Pythonu (pro tuto funkci funguje Python 3.7. x).

1. Vyberte umístění pro nový prostředek (například Západní USA 2).

V tomto okamžiku se v předplatném Azure vytvoří potřebné prostředky pro hostování nové funkce Azure v Azure. Počkejte, až se nasazení dokončí. V okně výstup se zobrazí stav procesu nasazení.

1. V [Azure Portal](https://portal.azure.com)přejděte na **všechny prostředky** a vyhledejte funkci, kterou jste publikovali pomocí jejího názvu. Pokud jste pojmenovali **zřetězený**, vyberte prostředek.

1. Klikněte na tlačítko **</> získat adresu URL funkce** . To vám umožní zkopírovat adresu URL pro volání funkce.

## <a name="test-the-function-in-azure"></a>Testování funkce v Azure

Teď, když máte výchozí klíč hostitele, otestujte funkci následujícím způsobem:

```http
POST [Function URL you copied above]
```

### <a name="request-body"></a>Text požadavku
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

Tento příklad by měl mít stejný výsledek, který jste viděli dříve při spuštění funkce v místním prostředí.

## <a name="connect-to-your-pipeline"></a>Připojení k vašemu kanálu

Teď, když máte novou vlastní dovednost, ji můžete přidat do svého dovednostiu. Následující příklad ukazuje, jak volat dovednost pro zřetězení názvu a autora dokumentu do jednoho pole, které voláme merged_title_author. Nahraďte `[your-function-url-here]` adresou URL vaší nové funkce Azure Functions.

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
Gratulujeme! Vytvořili jste svou první vlastní dovednost. Teď můžete postupovat podle stejného vzoru, abyste mohli přidat vlastní funkce. Další informace získáte kliknutím na následující odkazy.

+ [Dovednosti v Power: úložiště vlastních dovedností](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Přidání vlastní dovednosti do kanálu pro obohacení AI](cognitive-search-custom-skill-interface.md)
+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Vytvořit dovednosti (REST)](/rest/api/searchservice/create-skillset)
+ [Jak mapovat obohacená pole](cognitive-search-output-field-mapping.md)