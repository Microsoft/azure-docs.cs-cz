---
title: Vlastní AML dovednosti v dovednosti
titleSuffix: Azure Cognitive Search
description: Naširší možnosti Azure Kognitivní hledání dovednosti s modelem Azure Machine Learning.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 6cefe543ea8ba992b028448070bf041a77bfec64
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630271"
---
# <a name="aml-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>AML dovednosti v kanálu pro rozšíření Azure Kognitivní hledání

> [!IMPORTANT] 
> Tato dovednost je aktuálně ve verzi Public Preview. Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). V tuto chvíli není podporovaná žádná podpora sady .NET SDK.

**AML** dovednosti umožňuje rozšířit obohacení AI pomocí vlastního modelu [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) (AML). Jakmile je model AML [vyškolený a nasazený](../machine-learning/concept-azure-machine-learning-architecture.md#workspace), **AML** dovednost ho integruje do obohacení AI.

Podobně jako v případě integrovaných dovedností mají **AML** dovednosti vstupy a výstupy. Vstupy jsou odesílány do nasazené služby AML jako objekt JSON, který vypíše datovou část JSON jako odpověď spolu s kódem stavu úspěch. Očekává se, že odpověď bude mít výstupy určené vaší **AML** dovedností. Jakákoli jiná odpověď se považuje za chybu a neprovádí se žádné obohacení.

> [!NOTE]
> Indexer se dvakrát opakuje pro určité standardní stavové kódy HTTP vrácené službou AML. Tyto stavové kódy HTTP:
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="prerequisites"></a>Požadavky

* [Pracovní prostor AML](../machine-learning/concept-workspace.md)
* [Azure Kubernetes Service AML Target COMPUTE](../machine-learning/concept-compute-target.md) v tomto pracovním prostoru s [nasazeným modelem](../machine-learning/how-to-deploy-azure-kubernetes-service.md)
  * [Cíl výpočtů by měl mít povolený protokol SSL](../machine-learning/how-to-secure-web-service.md#deploy-on-azure-kubernetes-service). Azure Kognitivní hledání povoluje přístup jenom k koncovým bodům **https** .
  * Certifikáty podepsané svým držitelem se nedají použít.

## <a name="odatatype"></a>@odata.type  
Microsoft. dovednosti. Custom. AmlSkill

## <a name="skill-parameters"></a>Parametry dovednosti

U parametrů se rozlišují malá a velká písmena. Parametry, které se rozhodnete použít, závisí na tom [, jaké ověřování vaše služba AML vyžaduje](#WhatSkillParametersToUse) .

| Název parametru | Description |
|--------------------|-------------|
| `uri` | (Vyžaduje se pro [ověřování a ověření klíče](#WhatSkillParametersToUse)) [Identifikátor URI pro vyhodnocování služby AML](../machine-learning/how-to-consume-web-service.md) , na kterou se pošle datová část _JSON_ Je povoleno pouze schéma identifikátoru URI **protokolu HTTPS** . |
| `key` | (Vyžadováno pro [ověření klíče](#WhatSkillParametersToUse)) [Klíč pro službu AML](../machine-learning/how-to-consume-web-service.md#authentication-with-keys) |
| `resourceId` | (Vyžaduje se pro [ověřování tokenu](#WhatSkillParametersToUse)). ID prostředku Azure Resource Manager služby AML Měl by být ve formátu Subscriptions/{GUID}/resourceGroups/{Resource-Group-Name}/Microsoft. MachineLearningServices/Workspaces/{Workspace-Name}/Services/{service_name}. |
| `region` | (Volitelné pro [ověřování tokenu](#WhatSkillParametersToUse)). [Oblast](https://azure.microsoft.com/global-infrastructure/regions/) , ve které je nasazená služba AML |
| `timeout` | Volitelné Když se tato parametr zadá, označuje časový limit pro klienta http, který provádí volání rozhraní API. Musí být formátován jako hodnota XSD "dayTimeDuration" (omezená podmnožina hodnoty [Duration ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Například `PT60S` po dobu 60 sekund. Pokud není nastavená, vybere se výchozí hodnota 30 sekund. Časový limit lze nastavit na maximálně 230 sekund a minimálně 1 sekundu. |
| `degreeOfParallelism` | Volitelné Když se tato hodnota zadá, označuje počet volání, která indexer provede paralelně se zadaným koncovým bodem. Tuto hodnotu můžete snížit, pokud se Váš koncový bod nedaří s příliš vysokým zatížením žádosti nebo ho zvýšit, pokud je váš koncový bod schopný přijmout více požadavků a vy chcete zvýšit výkon indexeru.  Pokud není nastavená, použije se výchozí hodnota 5. DegreeOfParallelism může být nastavená na maximálně 10 a minimálně 1.

<a name="WhatSkillParametersToUse"></a>

## <a name="what-skill-parameters-to-use"></a>Jaké parametry dovednosti použít

Které parametry dovedností AML jsou povinné, závisí na tom, jaké ověřování služba AML používá, pokud existuje. Služby AML Services poskytují tři možnosti ověřování:

* [Ověřování založené na klíčích](../machine-learning/how-to-authenticate-web-service.md#key-based-authentication). Pro ověření žádostí o vyhodnocování z dovedností AML je k dispozici statický klíč.
  * Použití parametrů _URI_ a _Key_
* [Ověřování založené na tokenech](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication). Služba AML je [nasazena pomocí ověřování založeného na tokenech](../machine-learning/how-to-authenticate-web-service.md#token-based-authentication). [Spravované identitě](../active-directory/managed-identities-azure-resources/overview.md) služby Azure kognitivní hledání je udělená [role čtecího](../machine-learning/how-to-assign-roles.md) modulu v pracovním prostoru služby AML. AML dovednost potom používá spravovanou identitu služby Azure Kognitivní hledání k ověření ve službě AML, bez nutnosti vyžadování statických klíčů.
  * Použijte parametr _ResourceID_ .
  * Pokud je služba Azure Kognitivní hledání v jiném regionu než pracovní prostor AML, použijte parametr _region_ a nastavte oblast, ve které byla služba AML nasazená.
* Bez ověřování. K používání služby AML není nutné žádné ověřování.
  * Použít parametr _URI_

## <a name="skill-inputs"></a>Vstupy dovedností

Pro tuto dovednost neexistují žádné předdefinované vstupy. Můžete vybrat jedno nebo více polí, která budou již k dispozici v době provádění této dovednosti, jako vstupy a datová část _JSON_ odesílaná do služby AML bude mít různá pole.

## <a name="skill-outputs"></a>Výstupy dovedností

Pro tuto dovednost nejsou k dispozici žádné předdefinované výstupy. V závislosti na reakci, kterou služba AML vrátí, přidejte výstupní pole tak, aby bylo možné je vybrat z odpovědi _JSON_ .

## <a name="sample-definition"></a>Definice vzorku

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="sample-input-json-structure"></a>Ukázka vstupní struktury JSON

Tato struktura _JSON_ představuje datovou část, která se pošle do vaší služby AML. Pole nejvyšší úrovně struktury budou odpovídat názvům uvedeným v `inputs` části definice dovednosti. Hodnota těchto polí bude z `source` těchto polí (což může být z pole v dokumentu nebo potenciálně z jiné dovednosti).

```json
{
  "text": "Este es un contrato en Inglés"
}
```

## <a name="sample-output-json-structure"></a>Ukázka výstupní struktury JSON

Výstup odpovídá odpovědi vrácené z vaší služby AML. Služba AML by měla vracet pouze datovou část _JSON_ (ověřena tak, že prohlíží `Content-Type` hlavičku odpovědi) a měla by být objekt, kde jsou obohacena rozšíření, která odpovídají názvům v `output` a jejichž hodnota je považována za obohacení.

```json
{
    "detected_language_code": "es"
}
```

## <a name="inline-shaping-sample-definition"></a>Definice ukázky vloženého tvaru

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.AmlSkill",
    "description": "A sample model that detects the language of sentence",
    "uri": "https://contoso.count-things.com/score",
    "context": "/document",
    "inputs": [
      {
        "name": "shapedText",
        "sourceContext": "/document",
        "inputs": [
            {
              "name": "content",
              "source": "/document/content"
            }
        ]
      }
    ],
    "outputs": [
      {
        "name": "detected_language_code"
      }
    ]
  }
```

## <a name="inline-shaping-input-json-structure"></a>Struktura vstupu JSON pro vložené tvarování

```json
{
  "shapedText": { "content": "Este es un contrato en Inglés" }
}
```

## <a name="inline-shaping-sample-output-json-structure"></a>Ukázka vloženého tvarování výstupní struktury JSON

```json
{
    "detected_language_code": "es"
}
```

## <a name="error-cases"></a>Chybové případy
Kromě AML nedostupných nebo odesílaných stavových kódů, které nejsou úspěšné, se považují za chybné případy následující:

* Pokud služba AML vrátí stavový kód úspěchu, ale odezva indikuje, že není `application/json` , odpověď se považuje za neplatnou a neprovede se žádná rozšíření.
* Pokud služba AML vrátí neplatný kód JSON

V případech, kdy není služba AML k dispozici, nebo vrátí chybu protokolu HTTP, bude do historie spouštění indexeru přidána popisná chyba s případnými podrobnostmi o chybě HTTP.

## <a name="see-also"></a>Viz také

+ [Jak definovat dovednosti](cognitive-search-defining-skillset.md)
+ [Řešení potíží se službou AML](../machine-learning/how-to-troubleshoot-deployment.md)