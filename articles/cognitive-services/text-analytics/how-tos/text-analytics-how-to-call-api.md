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
ms.date: 12/17/2020
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 9302bde13a303dda2107900dc0c10cc180669a18
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650724"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Způsob volání Analýza textu REST API

V tomto článku používáme Analýza textu [REST API a](https://www.postman.com/downloads/) potomer k předvedení klíčových konceptů. Rozhraní API poskytuje několik synchronních a asynchronních koncových bodů pro používání funkcí služby. 

## <a name="create-a-text-analytics-resource"></a>Vytvoření prostředku Analýza textu

> [!NOTE]
> * Pokud chcete použít koncové body nebo, budete potřebovat prostředek Analýza textu s použitím [cenové úrovně](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) Standard (S) `/analyze` `/health` . `/analyze`Koncový bod je zahrnutý ve vaší [cenové úrovni](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

Před použitím rozhraní API pro analýzu textu budete muset vytvořit prostředek Azure s klíčem a koncovým bodem pro vaše aplikace. 

1.  Nejprve přejdete na [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) a vytvořte nový prostředek analýza textu, pokud ho ještě nemáte. Vyberte [cenovou úroveň](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

2.  Vyberte oblast, kterou chcete použít pro koncový bod.  Všimněte si prosím, že `/analyze` `/health` koncové body a jsou dostupné jenom v těchto oblastech: Západní USA 2, Východní USA 2, Střed USA, Severní Evropa a západní Evropa.

3.  Vytvořte prostředek Analýza textu a v levé části stránky přejdete do okna klíče a koncový bod. Zkopírujte klíč, který se použije později při volání rozhraní API. Později ho přidáte jako hodnotu pro `Ocp-Apim-Subscription-Key` hlavičku.

## <a name="change-your-pricing-tier"></a>Změna cenové úrovně 

Pokud máte existující Analýza textu prostředek pomocí cenové úrovně S4 S0, měli byste ho aktualizovat tak, aby používaly [cenovou úroveň](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)Standard (S). Cenové úrovně S0 až S4 budou vyřazeny. Aktualizace cen prostředků:

1. V [Azure Portal](https://portal.azure.com/)přejděte k prostředku analýza textu.
2. V navigační nabídce vlevo vyberte **cenovou úroveň** . Bude se nacházet pod **správou prostředků**. 
3. Vyberte cenovou úroveň Standard (S). Pak klikněte na **Vybrat**.

Můžete také vytvořit nový prostředek Analýza textu s cenovou úrovní Standard (S) a migrovat aplikace tak, aby používaly přihlašovací údaje pro nový prostředek. 

## <a name="using-the-api-synchronously"></a>Synchronní používání rozhraní API

Můžete volat Analýza textu synchronně (pro scénáře s nízkou latencí). Při použití synchronního rozhraní API je třeba každé rozhraní API (funkce) zavolat samostatně. Pokud potřebujete zavolat více funkcí, přečtěte si níže část o postupu volání Analýza textu asynchronně. 

## <a name="using-the-api-asynchronously"></a>Asynchronní použití rozhraní API

Počínaje verzí v 3.1 – Preview. 3 poskytuje rozhraní API pro analýzu textu dva asynchronní koncové body: 

* `/analyze`Koncový bod pro analýza textu umožňuje analyzovat stejnou sadu textových dokumentů s více funkcemi pro analýzu textu v jednom volání rozhraní API. Předtím, abyste mohli použít více funkcí, musíte pro každou operaci udělat samostatná volání rozhraní API. Tuto možnost zvažte, pokud potřebujete analyzovat velké sady dokumentů s více než jednou funkcí Analýza textu.

* `/health`Koncový bod pro analýza textu pro stav, který může extrahovat a označovat relevantní lékařské informace z klinických dokumentů.  

V následující tabulce najdete informace o tom, které funkce se dají použít asynchronně. Všimněte si, že z koncového bodu může být volána pouze několik funkcí `/analyze` . 

| Funkce | Synchronní | Asynchronní |
|--|--|--|
| Rozpoznávání jazyka | ✔ |  |
| Analýza mínění | ✔ |  |
| Dolování názoru | ✔ |  |
| Extrakce klíčových frází | ✔ | ✔* |
| Rozpoznávání pojmenovaných entit (včetně PII a FÍ) | ✔ | ✔* |
| Analýza textu pro stav (kontejner) | ✔ |  |
| Analýza textu pro stav (API) |  | ✔  |

`*` – Volá se asynchronně prostřednictvím `/analyze` koncového bodu.


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

<a name="json-schema"></a>

## <a name="api-request-formats"></a>Formáty požadavků rozhraní API

Do rozhraní API pro analýzu textu lze odeslat synchronní i asynchronní volání.

#### <a name="synchronous"></a>[Synchronní](#tab/synchronous)

### <a name="synchronous-requests"></a>Synchronní požadavky

Formát pro požadavky rozhraní API je stejný pro všechny synchronní operace. Dokumenty jsou odesílány v objektu JSON jako nezpracovaný nestrukturovaný text. KÓD XML není podporován. Schéma JSON se skládá z prvků popsaných níže.

| Prvek | Platné hodnoty | Povinné? | Využití |
|---------|--------------|-----------|-------|
|`id` |Datovým typem je řetězec, ale v praxi se ID dokumentů považují za celá čísla. | Vyžadováno | Systém používá ID, která zadáte k strukturování výstupu. Pro každé ID v žádosti jsou vygenerovány kódy jazyka, klíčové fráze a výsledky mínění.|
|`text` | Nestrukturovaný nezpracovaný text, maximálně 5 120 znaků. | Vyžadováno | V případě detekce jazyka lze text vyjádřit v jakémkoli jazyce. Pro analýzu mínění, extrakci klíčových frází a identifikaci entit musí být text v [podporovaném jazyce](../language-support.md). |
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

#### <a name="asynchronous"></a>[Asynchronní](#tab/asynchronous)

### <a name="asynchronous-requests-to-the-analyze-endpoint"></a>Asynchronní požadavky na `/analyze` koncový bod

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
|`analysisInput` | Zahrnuje `documents` pole níže. | Vyžadováno | Obsahuje informace o dokumentech, které chcete odeslat. |
|`documents` | Obsahuje `id` pole a `text` níže. | Vyžadováno | Obsahuje informace pro každý odesílaný dokument a nezpracovaný text dokumentu. |
|`id` | Řetězec | Vyžadováno | ID, která zadáte, se použijí k uspořádání výstupu. |
|`text` | Nestrukturovaný nezpracovaný text, maximálně 125 000 znaků. | Vyžadováno | Musí být v anglickém jazyce, což je jediný aktuálně podporovaný jazyk. |
|`tasks` | Obsahuje následující funkce Analýza textu: `entityRecognitionTasks` , `keyPhraseExtractionTasks` nebo `entityRecognitionPiiTasks` . | Vyžadováno | Jedna nebo více Analýza textuch funkcí, které chcete použít. Všimněte si, že `entityRecognitionPiiTasks` má volitelný `domain` parametr, který lze nastavit na `pii` nebo `phi` . Pokud tento parametr nezadáte, použije se výchozí hodnota systému `pii` . |
|`parameters` | Obsahuje `model-version` pole a `stringIndexType` níže. | Vyžadováno | Toto pole je zahrnuté ve výše uvedených úlohách funkcí, které jste si zvolili. Obsahují informace o verzi modelu, kterou chcete použít, a typ indexu. |
|`model-version` | Řetězec | Vyžadováno | Určete, která verze modelu je volána, kterou chcete použít.  |
|`stringIndexType` | Řetězec | Vyžadováno | Určete dekodér textu, který odpovídá vašemu programovacímu prostředí.  Podporované typy jsou `textElement_v8` (výchozí), `unicodeCodePoint` , `utf16CodeUnit` . Další informace najdete v [článku posuny textu](../concepts/text-offsets.md#offsets-in-api-version-31-preview) .  |
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

### <a name="asynchronous-requests-to-the-health-endpoint"></a>Asynchronní požadavky na `/health` koncový bod

Formát požadavků rozhraní API na Analýza textu pro hostované rozhraní API pro stav je stejný jako u svého kontejneru. Dokumenty jsou odesílány v objektu JSON jako nezpracovaný nestrukturovaný text. KÓD XML není podporován. Schéma JSON se skládá z prvků popsaných níže.  Vyplňte prosím [formulář žádosti Cognitive Services](https://aka.ms/csgate) , abyste požádali o přístup k analýza textu ve verzi Public Preview. Nebudete se vám účtovat Analýza textu pro využívání stavu. 

| Prvek | Platné hodnoty | Povinné? | Využití |
|---------|--------------|-----------|-------|
|`id` |Datovým typem je řetězec, ale v praxi se ID dokumentů považují za celá čísla. | Vyžadováno | Systém používá ID, která zadáte k strukturování výstupu. |
|`text` | Nestrukturovaný nezpracovaný text, maximálně 5 120 znaků. | Vyžadováno | Všimněte si, že v současné době je podporován pouze anglický text. |
|`language` | 2 – znakový kód [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) pro [podporovaný jazyk](../language-support.md) | Vyžadováno | V tuto `en` chvíli se podporuje jenom. |

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

### <a name="endpoints-for-sending-synchronous-requests"></a>Koncové body pro odesílání synchronních žádostí

| Funkce | Typ žádosti | Koncové body prostředků |
|--|--|--|
| Rozpoznávání jazyka | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| Analýza mínění | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| Dolování názoru | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| Extrakce klíčových frází | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| Rozpoznávání pojmenovaných entit – obecné | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| Rozpoznávání pojmenovaných entit – PII | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| Rozpoznávání pojmenovaných entit – FÍ | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="asynchronous"></a>[Asynchronní](#tab/asynchronous)

### <a name="endpoints-for-sending-asynchronous-requests-to-the-analyze-endpoint"></a>Koncové body pro odesílání asynchronních požadavků na `/analyze` koncový bod

| Funkce | Typ žádosti | Koncové body prostředků |
|--|--|--|
| Odeslat úlohu analýzy | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze` |
| Získání stavu a výsledků analýzy | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>` |

### <a name="endpoints-for-sending-asynchronous-requests-to-the-health-endpoint"></a>Koncové body pro odesílání asynchronních požadavků na `/health` koncový bod

| Funkce | Typ žádosti | Koncové body prostředků |
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

      + [Rozpoznávání jazyka](text-analytics-how-to-language-detection.md)
      + [Extrakce klíčových frází](text-analytics-how-to-keyword-extraction.md)
      + [Analýza mínění](text-analytics-how-to-sentiment-analysis.md)
      + [Rozpoznávání entit](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>Odeslat žádost

Odešlete žádost o rozhraní API. Pokud jste provedli volání synchronního koncového bodu, odpověď se zobrazí okamžitě jako jeden dokument JSON s položkou pro každé ID dokumentu, které je v požadavku k dispozici.

Pokud jste provedli volání asynchronních `/analyze` nebo `/health` koncových bodů, ověřte, že jste obdrželi kód odpovědi 202. k zobrazení výsledků budete muset získat odpověď:

1. V odpovědi rozhraní API vyhledejte `Operation-Location` z hlavičky, která označuje úlohu, kterou jste odeslali do rozhraní API. 
2. Vytvořte žádost o získání koncového bodu, který jste použili. Přečtěte si v [tabulce výše](#set-up-a-request) pro formát koncového bodu a podívejte se na [referenční dokumentaci k rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus). Příklad:

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>`

3. Přidejte `Operation-Location` k žádosti.

4. Odpověď bude jeden dokument JSON s položkou pro každé ID dokumentu, které je v požadavku k dispozici.

Počítejte s tím, že pro `/analyze` asynchronní `/health` operace nebo jsou výsledky z požadavku GET v kroku 2 k dispozici po dobu 24 hodin od okamžiku vytvoření úlohy.  Tato doba je určena `expirationDateTime` hodnotou v odpovědi Get.  Po uplynutí tohoto časového období jsou výsledky vymazány a již nejsou k dispozici pro načtení.    

## <a name="example-api-responses"></a>Příklady odpovědí rozhraní API
 
# <a name="synchronous"></a>[Synchronní](#tab/synchronous)

### <a name="example-responses-for-synchronous-operation"></a>Příklady odpovědí na synchronní operaci

Odezvy synchronního koncového bodu se budou lišit v závislosti na použitém koncovém bodu. Příklady odpovědí najdete v následujících článcích.

+ [Rozpoznávání jazyka](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [Extrakce klíčových frází](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Analýza mínění](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [Rozpoznávání entit](text-analytics-how-to-entity-linking.md#view-results)

# <a name="asynchronous"></a>[Asynchronní](#tab/asynchronous)

### <a name="example-responses-for-asynchronous-operations"></a>Příklady odpovědí pro asynchronní operace

V případě úspěchu vrátí požadavek GET na `/analyze` koncový bod objekt obsahující přiřazené úkoly. Příklad: `keyPhraseExtractionTasks`. Tyto úlohy obsahují objekt odpovědi z příslušné funkce Analýza textu. Další informace najdete v následujících článcích.

+ [Extrakce klíčových frází](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Rozpoznávání entit](text-analytics-how-to-entity-linking.md#view-results)
+ [Analýza textu pro zdravotnictví](text-analytics-for-health.md#hosted-asynchronous-web-api-response)

--- 

## <a name="see-also"></a>Viz také

* [Přehled analýzy textu](../overview.md)
* [Nejčastější dotazy](../text-analytics-resource-faq.md)</br>
* [Produktová stránka pro analýzu textu](//go.microsoft.com/fwlink/?LinkID=759712)
* [Použití klientské knihovny Analýza textu](../quickstarts/client-libraries-rest-api.md)
* [Co je nového](../whats-new.md)
