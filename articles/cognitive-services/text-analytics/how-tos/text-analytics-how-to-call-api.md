---
title: Volání rozhraní Text Analytics API
titleSuffix: Azure Cognitive Services
description: Tento článek vysvětluje, jak můžete volat službu Azure Cognitive Services Analýza textu REST API a post.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: aahi
ms.openlocfilehash: 90a4da2aadbbdf07d851e4407d2d417fc76d32af
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512320"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Způsob volání Analýza textu REST API

V tomto článku používáme Analýza textu [REST API a](https://www.postman.com/downloads/) potomer k předvedení klíčových konceptů. Rozhraní API poskytuje několik synchronních a asynchronních koncových bodů pro používání funkcí služby. 

## <a name="using-the-api-asynchronously"></a>Asynchronní použití rozhraní API

Počínaje verzí v 3.1 – Preview. 3 poskytuje rozhraní API pro analýzu textu dva asynchronní koncové body: 

* `/analyze`Koncový bod pro analýza textu umožňuje analyzovat stejnou sadu textových dokumentů s více funkcemi pro analýzu textu v jednom volání rozhraní API. Předtím, abyste mohli použít více funkcí, musíte pro každou operaci udělat samostatná volání rozhraní API. Tuto možnost zvažte, pokud potřebujete analyzovat velké sady dokumentů s více než jednou funkcí Analýza textu.

* `/health`Koncový bod pro analýza textu pro stav, který může extrahovat a označovat relevantní lékařské informace z klinických dokumentů.  

V následující tabulce najdete informace o tom, které funkce se dají použít asynchronně. Všimněte si, že z koncového bodu může být volána pouze několik funkcí `/analyze` . 

| Příznak | Synchronní | Asynchronní |
|--|--|--|
| rozpoznávání jazyka, | ✔ |  |
| Analýza mínění | ✔ |  |
| Dolování názoru | ✔ |  |
| extrakce klíčových frází, | ✔ | ✔* |
| Rozpoznávání pojmenovaných entit (včetně PII a FÍ) | ✔ | ✔* |
| Analýza textu pro stav (kontejner) | ✔ |  |
| Analýza textu pro stav (API) |  | ✔  |

`*` – Volá se asynchronně prostřednictvím `/analyze` koncového bodu.


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

## <a name="prerequisites"></a>Požadavky


> [!NOTE]
> Pokud chcete použít koncové body nebo, budete potřebovat prostředek Analýza textu s použitím [cenové úrovně](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) Standard (S) `/analyze` `/health` .

1.  Nejprve přejdete na [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) a vytvořte nový prostředek analýza textu, pokud ho ještě nemáte. Pokud chcete použít koncové body nebo, vyberte **cenovou úroveň Standard (y)** `/analyze` `/health` .

2.  Vyberte oblast, kterou chcete použít pro koncový bod.  Všimněte si prosím, že `/analyze` `/health` koncové body a jsou dostupné jenom v těchto oblastech: Západní USA 2, Východní USA 2, Střed USA, Severní Evropa a západní Evropa.

3.  Vytvořte prostředek Analýza textu a v levé části stránky přejdete do okna klíče a koncový bod. Zkopírujte klíč, který se použije později při volání rozhraní API. Později ho přidáte jako hodnotu pro `Ocp-Apim-Subscription-Key` hlavičku.


<a name="json-schema"></a>

## <a name="api-request-format"></a>Formát požadavku rozhraní API

#### <a name="synchronous"></a>[Synchronní](#tab/synchronous)

Formát pro požadavky rozhraní API je stejný pro všechny synchronní operace. Dokumenty jsou odesílány v objektu JSON jako nezpracovaný nestrukturovaný text. KÓD XML není podporován. Schéma JSON se skládá z prvků popsaných níže.

| Prvek | Platné hodnoty | Povinné? | Využití |
|---------|--------------|-----------|-------|
|`id` |Datovým typem je řetězec, ale v praxi se ID dokumentů považují za celá čísla. | Povinné | Systém používá ID, která zadáte k strukturování výstupu. Pro každé ID v žádosti jsou vygenerovány kódy jazyka, klíčové fráze a výsledky mínění.|
|`text` | Nestrukturovaný nezpracovaný text, maximálně 5 120 znaků. | Povinné | V případě detekce jazyka lze text vyjádřit v jakémkoli jazyce. Pro analýzu mínění, extrakci klíčových frází a identifikaci entit musí být text v [podporovaném jazyce](../language-support.md). |
|`language` | 2 – znakový kód [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) pro [podporovaný jazyk](../language-support.md) | Různé | Vyžaduje se pro analýzu míněníí, extrakci klíčových frází a propojení entit. volitelné pro detekci jazyka. Pokud vyloučíte, nedošlo k žádné chybě, ale analýza je bez něj oslabena. Kód jazyka by měl odpovídat vašemu `text` zadání. |

Následuje příklad požadavku rozhraní API pro synchronní Analýza textu koncové body. 

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    }
  ]
}
```

#### <a name="analyze"></a>[Analýza](#tab/analyze)

> [!NOTE]
> Nejnovější předběžná verze klientské knihovny Analýza textu umožňuje volat asynchronní operace analýzy pomocí objektu klienta. Příklady najdete na GitHubu:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

`/analyze`Koncový bod vám umožní vybrat, které z podporovaných funkcí analýza textu chcete použít v jednom volání rozhraní API. Tento koncový bod aktuálně podporuje:

* extrakce klíčových frází 
* Rozpoznávání pojmenovaných entit (včetně PII a FÍ)

| Prvek | Platné hodnoty | Povinné? | Využití |
|---------|--------------|-----------|-------|
|`displayName` | Řetězec | Volitelné | Slouží jako zobrazovaný název pro jedinečný identifikátor úlohy.|
|`analysisInput` | Zahrnuje `documents` pole níže. | Povinné | Obsahuje informace o dokumentech, které chcete odeslat. |
|`documents` | Obsahuje `id` pole a `text` níže. | Povinné | Obsahuje informace pro každý odesílaný dokument a nezpracovaný text dokumentu. |
|`id` | Řetězec | Povinné | ID, která zadáte, se použijí k uspořádání výstupu. |
|`text` | Nestrukturovaný nezpracovaný text, maximálně 125 000 znaků. | Povinné | Musí být v anglickém jazyce, což je jediný aktuálně podporovaný jazyk. |
|`tasks` | Obsahuje následující funkce Analýza textu: `entityRecognitionTasks` , `keyPhraseExtractionTasks` nebo `entityRecognitionPiiTasks` . | Povinné | Jedna nebo více Analýza textuch funkcí, které chcete použít. Všimněte si, že `entityRecognitionPiiTasks` má volitelný `domain` parametr, který lze nastavit na `pii` nebo `phi` . Pokud tento parametr nezadáte, použije se výchozí hodnota systému `pii` . |
|`parameters` | Obsahuje `model-version` pole a `stringIndexType` níže. | Povinné | Toto pole je zahrnuté ve výše uvedených úlohách funkcí, které jste si zvolili. Obsahují informace o verzi modelu, kterou chcete použít, a typ indexu. |
|`model-version` | Řetězec | Povinné | Určete, která verze modelu je volána, kterou chcete použít.  |
|`stringIndexType` | Řetězec | Povinné | Určete dekodér textu, který odpovídá vašemu programovacímu prostředí.  Podporované typy jsou `textElement_v8` (výchozí), `unicodeCodePoint` , `utf16CodeUnit` . Další informace najdete v [článku posuny textu](../concepts/text-offsets.md#offsets-in-api-version-31-preview) .  |
|`domain` | Řetězec | Volitelné | Platí pouze jako parametr `entityRecognitionPiiTasks` úlohy a lze ji nastavit na `pii` nebo `phi` . Nastaví se na výchozí hodnotu, je- `pii` li tento parametr zadán.  |

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}

```

#### <a name="text-analytics-for-health"></a>[Analýza textu pro zdravotnictví](#tab/health)

Formát požadavků rozhraní API na Analýza textu pro hostované rozhraní API pro stav je stejný jako u svého kontejneru. Dokumenty jsou odesílány v objektu JSON jako nezpracovaný nestrukturovaný text. KÓD XML není podporován. Schéma JSON se skládá z prvků popsaných níže.  Vyplňte prosím [formulář žádosti Cognitive Services](https://aka.ms/csgate) , abyste požádali o přístup k analýza textu ve verzi Public Preview. Nebudete se vám účtovat Analýza textu pro využívání stavu. 

| Prvek | Platné hodnoty | Povinné? | Využití |
|---------|--------------|-----------|-------|
|`id` |Datovým typem je řetězec, ale v praxi se ID dokumentů považují za celá čísla. | Povinné | Systém používá ID, která zadáte k strukturování výstupu. |
|`text` | Nestrukturovaný nezpracovaný text, maximálně 5 120 znaků. | Povinné | Všimněte si, že v současné době je podporován pouze anglický text. |
|`language` | 2 – znakový kód [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) pro [podporovaný jazyk](../language-support.md) | Povinné | V tuto `en` chvíli se podporuje jenom. |

Následuje příklad požadavku rozhraní API pro Analýza textu pro koncové body stavu. 

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

---

>[!TIP]
> Informace o limitech a omezeních velikosti pro posílání dat do rozhraní API pro analýzu textu najdete v článku omezení přenosů [dat a](../concepts/data-limits.md) sazeb.


## <a name="set-up-a-request"></a>Nastavení žádosti 

V části post (nebo jiný nástroj pro testování webového rozhraní API) přidejte koncový bod pro funkci, kterou chcete použít. Pomocí následující tabulky Najděte příslušný formát koncového bodu a nahraďte `<your-text-analytics-resource>` ho vaším koncovým bodem prostředku. Příklad:

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0/languages`

#### <a name="synchronous"></a>[Synchronní](#tab/synchronous)

| Příznak | Typ žádosti | Koncové body prostředků |
|--|--|--|
| rozpoznávání jazyka, | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| Analýza mínění | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| Dolování názoru | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| extrakce klíčových frází, | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| Rozpoznávání pojmenovaných entit – obecné | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| Rozpoznávání pojmenovaných entit – PII | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| Rozpoznávání pojmenovaných entit – FÍ | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="analyze"></a>[Analýza](#tab/analyze)

| Příznak | Typ žádosti | Koncové body prostředků |
|--|--|--|
| Odeslat úlohu analýzy | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze` |
| Získání stavu a výsledků analýzy | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>` |

#### <a name="text-analytics-for-health"></a>[Analýza textu pro zdravotnictví](#tab/health)

| Příznak | Typ žádosti | Koncové body prostředků |
|--|--|--|
| Odeslat Analýza textu pro úlohu stavu  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs` |
| Získání stavu a výsledků úlohy | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |
| Zrušit úlohu | DELETE | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |

--- 

Až budete mít koncový bod, v post (nebo jiném testovacím nástroji webového rozhraní API):

1. Vyberte typ žádosti pro funkci, kterou chcete použít.
2. Vložte do koncového bodu správné operace z výše uvedené tabulky.
3. Nastavte tři hlavičky žádosti:

   + `Ocp-Apim-Subscription-Key`: přístupový klíč získaný z Azure Portal
   + `Content-Type`: Application/JSON
   + `Accept`: Application/JSON

    Pokud používáte metodu post, měla by vaše žádost vypadat podobně jako na následujícím snímku obrazovky za předpokladu, že je `/keyPhrases` koncový bod.
    
    ![Snímek obrazovky žádosti s koncovým bodem a záhlavími](../media/postman-request-keyphrase-1.png)
    
4. Pro formát **těla** vyberte **raw** .
    
    ![Snímek obrazovky žádosti s nastavením textu](../media/postman-request-body-raw.png)

5. Vložte je do některých dokumentů JSON v platném formátu. Použijte příklady v části **Formát žádosti API** výše a další informace a příklady najdete v následujících tématech:

      + [rozpoznávání jazyka,](text-analytics-how-to-language-detection.md)
      + [extrakce klíčových frází,](text-analytics-how-to-keyword-extraction.md)
      + [Analýza mínění](text-analytics-how-to-sentiment-analysis.md)
      + [rozpoznávání entit,](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>Odeslat žádost

Odešlete žádost o rozhraní API. Pokud jste provedli volání synchronního koncového bodu, odpověď se zobrazí okamžitě jako jeden dokument JSON s položkou pro každé ID dokumentu, které je v požadavku k dispozici.

Pokud jste provedli volání asynchronních `/analyze` nebo `/health` koncových bodů, ověřte, že jste obdrželi kód odpovědi 202. k zobrazení výsledků budete muset získat odpověď:

1. V odpovědi rozhraní API vyhledejte `Operation-Location` z hlavičky, která označuje úlohu, kterou jste odeslali do rozhraní API. 
2. Vytvořte žádost o získání koncového bodu, který jste použili. Přečtěte si v [tabulce výše](#set-up-a-request) pro formát koncového bodu a podívejte se na [referenční dokumentaci k rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus). Příklad:

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>`

3. Přidejte `Operation-Location` k žádosti.

4. Odpověď bude jeden dokument JSON s položkou pro každé ID dokumentu, které je v požadavku k dispozici.

## <a name="example-api-responses"></a>Příklady odpovědí rozhraní API
 
# <a name="synchronous"></a>[Synchronní](#tab/synchronous)

Odezvy synchronního koncového bodu se budou lišit v závislosti na použitém koncovém bodu. Příklady odpovědí najdete v následujících článcích.

+ [rozpoznávání jazyka,](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [extrakce klíčových frází,](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Analýza mínění](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [rozpoznávání entit,](text-analytics-how-to-entity-linking.md#view-results)

# <a name="analyze"></a>[Analýza](#tab/analyze)

V případě úspěchu vrátí požadavek GET na `/analyze` koncový bod objekt obsahující přiřazené úkoly. Příklad: `keyPhraseExtractionTasks`. Tyto úlohy obsahují objekt odpovědi z příslušné funkce Analýza textu. Další informace najdete v následujících článcích.

+ [extrakce klíčových frází,](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [rozpoznávání entit,](text-analytics-how-to-entity-linking.md#view-results)


```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```

# <a name="text-analytics-for-health"></a>[Analýza textu pro zdravotnictví](#tab/health)

Další informace o Analýza textu pro odpověď na asynchronní rozhraní API pro stav najdete v následujícím článku:

+ [Analýza textu pro zdravotnictví](text-analytics-for-health.md#hosted-asynchronous-web-api-response)


--- 

## <a name="see-also"></a>Viz také:

* [Přehled analýzy textu](../overview.md)
* [Nejčastější dotazy](../text-analytics-resource-faq.md)</br>
* [Produktová stránka pro analýzu textu](//go.microsoft.com/fwlink/?LinkID=759712)
* [Použití klientské knihovny Analýza textu](../quickstarts/text-analytics-sdk.md)
* [Co je nového](../whats-new.md)
