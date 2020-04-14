---
title: Vlastní dovednost nástroje Pro rozpoznávání formulářů (C#)
titleSuffix: Azure Cognitive Search
description: Zjistěte, jak vytvořit vlastní dovednosti nástroje pro rozpoznávání formulářů pomocí c# a sady Visual Studio.
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: article
ms.date: 01/21/2020
ms.openlocfilehash: 713b790c432f0e416392243262aed4b0fcda8892
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274572"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>Příklad: Vytvoření vlastní dovednosti nástroje pro rozpoznávání formulářů

V tomto příkladu dovedností Azure Cognitive Search se dozvíte, jak vytvořit vlastní dovednosti nástroje pro rozpoznávání formulářů pomocí jazyka C# a sady Visual Studio. Nástroj pro rozpoznávání formulářů analyzuje dokumenty a extrahuje dvojice klíč/hodnota a data tabulky. Zabalením nástroje pro rozpoznávání formulářů do [vlastního rozhraní dovedností](cognitive-search-custom-skill-interface.md)můžete tuto funkci přidat jako krok v kanálu obohacení od konce. Kanál pak můžete načíst dokumenty a provést další transformace.

## <a name="prerequisites"></a>Požadavky

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (libovolné vydání).
- Nejméně pět forem stejného typu. Můžete použít ukázková data dodaný s touto příručkou.

## <a name="create-a-form-recognizer-resource"></a>Vytvoření prostředku nástroje pro rozpoznávání formulářů

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>Trénování vašeho modelu

Před použitím této dovednosti budete muset trénovat model nástroje pro rozpoznávání formulářů se vstupními formuláři. Postupujte podle [cURL rychlý start](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/curl-train-extract) se dozvíte, jak trénovat model. Můžete použít ukázkové formuláře uvedené v tomto rychlém startu nebo můžete použít vlastní data. Jakmile je model trénovaný, zkopírujte jeho hodnotu ID do zabezpečeného umístění.

## <a name="set-up-the-custom-skill"></a>Nastavení vlastní dovednosti

Tento kurz používá projekt [AnalyzeForm](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm) v úložišti [GitHub Azure Search Power Skills.](https://github.com/Azure-Samples/azure-search-power-skills) Klonujte toto úložiště do místního počítače a přejděte na **Vision/AnalyzeForm/** pro přístup k projektu. Potom _otevřete AnalyzeForm.csproj_ v sadě Visual Studio. Tento projekt vytvoří prostředek funkce Azure, který splňuje [vlastní rozhraní dovedností](cognitive-search-custom-skill-interface.md) a lze použít pro azure cognitive search obohacení. Trvá formulář dokumenty jako vstupy a výstupy (jako text) klíč/hodnota dvojice, které zadáte.

Nejprve přidejte proměnné prostředí na úrovni projektu. Vyhledejte projekt **AnalyzeForm** v levém podokně, klepněte na něj pravým tlačítkem myši a vyberte **příkaz Vlastnosti**. V okně **Vlastnosti** klikněte na kartu **Ladění** a vyhledejte pole **Proměnné prostředí.** Chcete-li přidat následující proměnné, klepněte na **tlačítko Přidat:**
* `FORMS_RECOGNIZER_ENDPOINT_URL`s hodnotou nastavenou na adresu URL koncového bodu.
* `FORMS_RECOGNIZER_API_KEY`s hodnotou nastavenou na klíč předplatného.
* `FORMS_RECOGNIZER_MODEL_ID`s hodnotou nastavenou na ID modelu, který jste trénovali.
* `FORMS_RECOGNIZER_RETRY_DELAY`s hodnotou nastavenou na 1000. Tato hodnota je čas v milisekundách, který program bude čekat před opakováním dotazu.
* `FORMS_RECOGNIZER_MAX_ATTEMPTS`s hodnotou nastavenou na 100. Tato hodnota je počet, kolikrát program bude dotaz služby při pokusu o získání úspěšné odpovědi.

Dále _otevřete AnalyzeForm.cs_ `fieldMappings` a vyhledejte proměnnou, která odkazuje na soubor *field-mappings.json.* Tento soubor (a proměnná, která na něj odkazuje) definuje seznam klíčů, které chcete extrahovat z formulářů, a vlastní popisek pro každý klíč. Například hodnota `{ "Address:", "address" }, { "Invoice For:", "recipient" }` znamená, že skript uloží pouze hodnoty `Address:` `Invoice For:` pro zjištěné a pole `"address"` a `"recipient"`bude tyto hodnoty označovat písmenem a) a .

Nakonec si `contentType` všimněte proměnné. Tento skript spouští daný model nástroje pro rozpoznávání formulářů ve vzdálených `application/json`dokumentech, na které odkazuje adresa URL, takže typ obsahu je . Pokud chcete analyzovat místní soubory zahrnutím jejich bajtových datových proudů do `contentType` požadavků HTTP, budete muset změnit [příslušný typ MIME](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types) pro váš soubor.

## <a name="test-the-function-from-visual-studio"></a>Testování funkce z aplikace Visual Studio

Po úpravě projektu ho uložte a nastavte projekt **AnalyzeForm** jako projekt po spuštění v sadě Visual Studio (pokud ještě není nastaven). Potom stisknutím **klávesy F5** spusťte funkci v místním prostředí. Pomocí služby REST, jako [je Pošťák,](https://www.postman.com/) volejte funkci.

### <a name="http-request"></a>Požadavek HTTP

Provedete následující žádost o volání funkce.

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>Text požadavku

Začněte se šablonou těla požadavku níže.

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>"
            }
        }
    ]
}
```

Zde budete muset zadat adresu URL formuláře, který má stejný typ jako formuláře, se kterými jste trénovali. Pro účely testování můžete použít jeden z vašich tréninkových formulářů. Pokud jste postupovali podle rychlého startu cURL, vaše formuláře se budou nacházet v účtu úložiště objektů blob Azure. Otevřete Průzkumníka úložiště Azure, vyhledejte soubor formuláře, klikněte na něj pravým tlačítkem myši a vyberte **získat sdílený přístupový podpis**. Další dialogové okno bude poskytovat adresu URL a token SAS. Zadejte tyto řetězce `"formUrl"` `"formSasToken"` do polí a v těle požadavku.

> [!div class="mx-imgBorder"]
> ![Průzkumník úložiště Azure; je vybrán dokument pdf](media/cognitive-search-skill-form/form-sas.png)

Pokud chcete analyzovat vzdálený dokument, který není v úložišti objektů blob Azure, vložte jeho adresu URL do `"formUrl"` pole a ponechte `"formSasToken"` pole prázdné.

> [!NOTE]
> Když je dovednost integrována do sady dovedností, adresa URL a token budou poskytnuty kognitivním vyhledáváním.

### <a name="response"></a>Odpověď

Měli byste vidět odpověď podobnou následujícímu příkladu:

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024 "
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publikování funkce do Azure

Když jste spokojeni s chováním funkce, můžete ji publikovat.

1. V **Průzkumníku řešení** v sadě Visual Studio klikněte pravým tlačítkem myši na projekt a vyberte **publikovat**. Zvolte **Vytvořit novou** > **publikování**.
1. Pokud jste visual studio ještě nepřipojili ke svému účtu Azure, vyberte **Přidat účet....**
1. Postupujte podle výzev na obrazovce. Zadejte jedinečný název služby aplikace, předplatného Azure, skupiny prostředků, plánu hostování a účtu úložiště, který chcete použít. Můžete vytvořit novou skupinu prostředků, nový plán hostování a nový účet úložiště, pokud je ještě nemáte. Až budete hotovi, vyberte **Vytvořit**.
1. Po dokončení nasazení si všimněte adresy URL webu. Tato adresa URL je adresa vaší funkční aplikace v Azure. Uložte jej do dočasného umístění.
1. Na [webu Azure Portal](https://portal.azure.com)přejděte do skupiny `AnalyzeForm` prostředků a vyhledejte funkci, kterou jste publikovali. V části **Spravovat** byste měli vidět klíče hostitelů. Zkopírujte *výchozí* klíč hostitele a uložte jej do dočasného umístění.

## <a name="connect-to-your-pipeline"></a>Připojení k potrubí

Chcete-li tuto dovednost použít v kanálu kognitivního vyhledávání, budete muset do sady dovedností přidat definici dovedností. Následující blok JSON je ukázková definice dovedností (měli byste aktualizovat vstupy a výstupy tak, aby odrážely vaše konkrétní scénář a prostředí skillset). Nahraďte `AzureFunctionEndpointUrl` adresu URL `AzureFunctionDefaultHostKey` funkce a nahraďte ji hostitelským klíčem.

```json
{ 
  "description":"Skillset that invokes the Form Recognizer custom skill",
  "skills":[ 
    "[... your existing skills go here]",
    { 
      "@odata.type":"#Microsoft.Skills.Custom.WebApiSkill",
      "name":"formrecognizer",
      "description":"Extracts fields from a form using a pre-trained form recognition model",
      "uri":"[AzureFunctionEndpointUrl]/api/analyze-form?code=[AzureFunctionDefaultHostKey]",
      "httpMethod":"POST",
      "timeout":"PT30S",
      "context":"/document",
      "batchSize":1,
      "inputs":[ 
        { 
          "name":"formUrl",
          "source":"/document/metadata_storage_path"
        },
        { 
          "name":"formSasToken",
          "source":"/document/metadata_storage_sas_token"
        }
      ],
      "outputs":[ 
        { 
          "name":"address",
          "targetName":"address"
        },
        { 
          "name":"recipient",
          "targetName":"recipient"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Další kroky

V této příručce jste vytvořili vlastní dovednosti ze služby Azure Form Recognizer. Další informace o vlastních dovednostech najdete v následujících zdrojích. 

* [Azure Search Power Skills: úložiště vlastních dovedností](https://github.com/*zure-Samples/azure-search-power-skills)
* [Přidání vlastní chudinské dovednosti do kanálu obohacení umělou ai.](cognitive-search-custom-skill-interface.md)
* [Definování dovedností](cognitive-search-defining-skillset.md)
* [Vytvoření sady dovedností (REST)](https://docs.microsoft.com/rest/api/*earchservice/create-skillset)
* [Mapovaná pole](cognitive-search-output-field-mapping.md)
