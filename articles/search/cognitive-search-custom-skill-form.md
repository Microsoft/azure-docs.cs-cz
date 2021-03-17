---
title: Vlastní dovednost pro rozpoznávání formulářů (C#)
titleSuffix: Azure Cognitive Search
description: Naučte se, jak vytvořit vlastní dovednosti pro rozpoznávání formulářů pomocí C# a Visual studia.
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: a3b073cdb90e0c427bfbca15c1440b9122672610
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880131"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>Příklad: Vytvoření vlastní dovednosti pro rozpoznávání formulářů

V tomto příkladu Azure Kognitivní hledání dovednosti se dozvíte, jak vytvořit vlastní dovednosti pro rozpoznávání formulářů pomocí C# a Visual studia. Nástroj pro rozpoznávání formulářů analyzuje dokumenty a extrahuje páry klíč/hodnota a tabulková data. Když rozbalíte Nástroj pro rozpoznávání formulářů do [vlastního rozhraní dovedností](cognitive-search-custom-skill-interface.md), můžete tuto funkci přidat jako krok v rámci kompletního kanálu pro rozšíření. Kanál pak může načíst dokumenty a dělat další transformace.

## <a name="prerequisites"></a>Požadavky

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (libovolná edice).
- Nejméně pět forem stejného typu. Můžete použít ukázková data uvedená v této příručce.

## <a name="create-a-form-recognizer-resource"></a>Vytvoření prostředku pro rozpoznávání formulářů

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>Trénování modelu

Předtím, než použijete tuto dovednost, budete muset vytvořit model rozpoznávání formulářů se vstupními formuláři. Postupujte podle pokynů pro [rychlý Start](../cognitive-services/form-recognizer/quickstarts/client-library.md?pivots=programming-language-rest-api) a Naučte se naučit model. Můžete použít ukázkové formuláře poskytované v rámci tohoto rychlého startu nebo můžete použít vlastní data. Po vyškolení modelu zkopírujte jeho hodnotu ID do zabezpečeného umístění.

## <a name="set-up-the-custom-skill"></a>Nastavení vlastní dovednosti

V tomto kurzu se používá projekt [AnalyzeForm](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm) v úložišti GitHub [Azure Searchch dovedností pro napájení](https://github.com/Azure-Samples/azure-search-power-skills) . Naklonujte toto úložiště do místního počítače a přejděte ke službě **Vision/AnalyzeForm/** , abyste měli přístup k projektu. Pak otevřete _AnalyzeForm. csproj_ v aplikaci Visual Studio. Tento projekt vytvoří prostředek funkce Azure, který splňuje [vlastní dovednostní rozhraní](cognitive-search-custom-skill-interface.md) a dá se použít pro rozšíření Azure kognitivní hledání. Přebírá dokumenty jako vstupy a výstupy IT (jako text) páry klíč/hodnota, které zadáte.

Nejprve přidejte proměnné prostředí na úrovni projektu. V levém podokně vyhledejte projekt **AnalyzeForm** , klikněte na něj pravým tlačítkem myši a vyberte možnost **vlastnosti**. V okně **vlastnosti** klikněte na kartu **ladění** a poté vyhledejte pole **proměnné prostředí** . Kliknutím na **Přidat** přidejte následující proměnné:
* `FORMS_RECOGNIZER_ENDPOINT_URL` s hodnotou nastavenou na adresu URL koncového bodu.
* `FORMS_RECOGNIZER_API_KEY` s hodnotou nastavenou na klíč předplatného.
* `FORMS_RECOGNIZER_MODEL_ID` s hodnotou nastavenou na ID modelu, který jste vyškole.
* `FORMS_RECOGNIZER_RETRY_DELAY` s hodnotou nastavenou na 1000. Tato hodnota je čas v milisekundách, po který bude program čekat před opakováním dotazu.
* `FORMS_RECOGNIZER_MAX_ATTEMPTS` s hodnotou nastavenou na 100. Tato hodnota představuje počet pokusů, kolikrát program dotazuje službu při pokusu o získání úspěšné odpovědi.

Dále otevřete _AnalyzeForm.cs_ a najděte `fieldMappings` proměnnou, která odkazuje na *field-mappings.jsv* souboru. Tento soubor (a proměnná, která na něj odkazuje) definuje seznam klíčů, které chcete extrahovat z vašich formulářů, a vlastní popisek pro každý klíč. Například hodnota `{ "Address:", "address" }, { "Invoice For:", "recipient" }` znamená, že skript uloží pouze hodnoty pro zjištěné `Address:` a `Invoice For:` pole a označí tyto hodnoty pomocí `"address"` a v `"recipient"` uvedeném pořadí.

Nakonec si poznamenejte `contentType` proměnnou. Tento skript spustí daný model rozpoznávání formulářů na vzdálených dokumentech, na které odkazuje adresa URL, takže typ obsahu je `application/json` . Pokud chcete analyzovat místní soubory zahrnutím jejich datových proudů do požadavků HTTP, budete muset změnit na `contentType` příslušný [typ MIME](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types) pro váš soubor.

## <a name="test-the-function-from-visual-studio"></a>Testování funkce ze sady Visual Studio

Po úpravě projektu ho uložte a nastavte projekt **AnalyzeForm** jako spouštěný projekt v sadě Visual Studio (Pokud už není nastavený). Potom stisknutím klávesy **F5** spusťte funkci v místním prostředí. Použijte službu REST, jako je například [post](https://www.postman.com/) , pro volání funkce.

### <a name="http-request"></a>Požadavek HTTP

Pro volání funkce provedete následující požadavek.

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>Text požadavku

Začněte s níže uvedenou šablonou textu žádosti.

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

Tady budete muset zadat adresu URL formuláře, který má stejný typ jako formuláře, které jste si vystavili. Pro účely testování můžete použít jeden z vašich školicích formulářů. Pokud jste postupovali s rychlým startem, vaše formuláře se budou nacházet v účtu služby Azure Blob Storage. Otevřete Průzkumník služby Azure Storage, vyhledejte soubor formuláře, klikněte na něj pravým tlačítkem myši a vyberte **získat sdílený přístupový podpis**. V dalším dialogovém okně bude k dispozici adresa URL a token SAS. Zadejte tyto řetězce do `"formUrl"` polí a v `"formSasToken"` textu žádosti, v uvedeném pořadí.

> [!div class="mx-imgBorder"]
> ![Průzkumník služby Azure Storage; je vybraný dokument PDF.](media/cognitive-search-skill-form/form-sas.png)

Pokud chcete analyzovat vzdálený dokument, který není v úložišti objektů BLOB v Azure, vložte jeho adresu URL do `"formUrl"` pole a nechejte `"formSasToken"` pole prázdné.

> [!NOTE]
> Když je dovednost integrovaná v dovednosti, adresa URL a token se poskytne Kognitivní hledání.

### <a name="response"></a>Odpověď

Měla by se zobrazit odpověď podobná následujícímu příkladu:

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

Až budete s chováním funkce spokojeni, můžete ho publikovat.

1. V **Průzkumník řešení** v aplikaci Visual Studio klikněte pravým tlačítkem myši na projekt a vyberte **publikovat**. Vyberte **vytvořit nové**  >  **publikování**.
1. Pokud jste ještě nepřipojili Visual Studio k účtu Azure, vyberte **Přidat účet....**
1. Postupujte podle výzev na obrazovce. Zadejte jedinečný název pro službu App Service, předplatné Azure, skupinu prostředků, plán hostování a účet úložiště, který chcete použít. Můžete vytvořit novou skupinu prostředků, nový plán hostování a nový účet úložiště, pokud je ještě nemáte. Až budete hotovi, vyberte **vytvořit**.
1. Po dokončení nasazení si všimněte adresy URL webu. Tato adresa URL je adresa vaší aplikace Function App v Azure. Uložte ho do dočasného umístění.
1. V [Azure Portal](https://portal.azure.com)přejděte do skupiny prostředků a vyhledejte `AnalyzeForm` funkci, kterou jste publikovali. V části **Spravovat** byste měli vidět klíče hostitele. Zkopírujte *výchozí* klíč hostitele a uložte ho do dočasného umístění.

## <a name="connect-to-your-pipeline"></a>Připojení k vašemu kanálu

Pokud chcete tuto dovednost použít v kanálu Kognitivní hledání, budete muset do dovednosti přidat definici dovedností. Následující blok JSON je ukázková definice dovedností (vaše vstupy a výstupy byste měli aktualizovat tak, aby odrážely konkrétní scénář a dovednosti prostředí). Nahraďte `AzureFunctionEndpointUrl` adresou URL funkce a nahraďte `AzureFunctionDefaultHostKey` klíčem hostitele.

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

V této příručce jste vytvořili vlastní dovednost ze služby pro rozpoznávání formulářů Azure. Další informace o vlastních dovednostech najdete v následujících zdrojích. 

* [Azure Search schopnosti napájení: úložiště vlastních dovedností](https://github.com/Azure-Samples/azure-search-power-skills)
* [Přidání vlastní dovednosti do kanálu pro obohacení AI](cognitive-search-custom-skill-interface.md)
* [Definování dovedností](cognitive-search-defining-skillset.md)
* [Vytvoření dovednosti (REST)](/rest/api/searchservice/create-skillset)
* [Obohacená pole mapy](cognitive-search-output-field-mapping.md)