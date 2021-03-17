---
title: Extrakce dat – LUIS
description: Extrahovat data z utterance textu s záměry a entitami. Zjistěte, jaký druh dat je možné extrahovat z Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: e6f01354bb5aa2b78d3c9962bac49be39dd2c81f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025989"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Extrakce dat z utterance textu s využitím záměrů a entit
LUIS poskytuje možnost získávat informace z přirozeného jazyka projevy uživatele. Informace jsou extrahovány způsobem, který může být použit programem, aplikací nebo robotem chatu k provedení akce. V následujících částech se dozvíte, jaká data se vracejí z záměrů a entit s příklady JSON.

Nejzávažnější data pro extrakci jsou data strojového učení, protože se neshoduje s přesným textem. Extrakce dat [entitami](luis-concept-entity-types.md) strojového učení musí být součástí [cyklu vytváření](luis-concept-app-iteration.md) , dokud nebudete mít jistotu, že obdržíte očekávaná data.

## <a name="data-location-and-key-usage"></a>Umístění dat a použití klíče
LUIS extrahuje data z utterance uživatele v publikovaném [koncovém bodu](luis-glossary.md#endpoint). **Požadavek https** (post nebo Get) obsahuje utterance a také některé volitelné konfigurace, například pracovní nebo produkční prostředí.

**Hodnota koncového bodu předpovědi v2**

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

**Požadavek na koncový bod verze V3**

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

`appID`Je k dispozici na stránce **Nastavení** aplikace Luis a také v rámci adresy URL (po `/apps/` ), když upravujete tuto aplikaci Luis. `subscription-key`Je klíč koncového bodu, který slouží k dotazování aplikace. I když se naučíte LUIS, můžete použít bezplatný a počáteční klíč, ale je důležité změnit klíč koncového bodu na klíč, který podporuje [očekávané využití Luis](luis-limits.md#key-limits). `timezoneOffset`Jednotka je v řádu minut.

**Odpověď https** obsahuje všechny informace o záměru a entitě, které Luis může určit na základě aktuálního publikovaného modelu pracovního nebo produkčního koncového bodu. Adresa URL koncového bodu se nachází na webu [Luis](luis-reference-regions.md) v části **Správa** na stránce **klíče a koncové body** .

## <a name="data-from-intents"></a>Data z záměrů
Primární data jsou nejvyšším **názvem záměru** hodnocení. Odpověď koncového bodu:

#### <a name="v2-prediction-endpoint-response"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

* * *

|Datový objekt|Typ dat|Umístění dat|Hodnota|
|--|--|--|--|
|Záměr|Řetězec|topScoringIntent. záměr|"GetStoreInfo"|

Pokud vaše aplikace chatovací robot nebo LUIS volá rozhodnutí na základě více než jednoho skóre záměru, vrátí všechny skóre záměrů.


#### <a name="v2-prediction-endpoint-response"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

Nastavte parametr QueryString, `verbose=true` . Odpověď koncového bodu:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

Nastavte parametr QueryString, `show-all-intents=true` . Odpověď koncového bodu:

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

* * *

Záměry jsou seřazené od nejvyšších po nejnižší skóre.

|Datový objekt|Typ dat|Umístění dat|Hodnota|Skóre|
|--|--|--|--|:--|
|Záměr|Řetězec|záměry [0]. záměr|"GetStoreInfo"|0,984749258|
|Záměr|Řetězec|záměry [1]. záměr|NTato|0,0168218873|

Pokud přidáte předem připravené domény, název záměru označuje doménu, například nebo, a `Utilties` `Communication` také záměr:

#### <a name="v2-prediction-endpoint-response"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

* * *

|Doména|Datový objekt|Typ dat|Umístění dat|Hodnota|
|--|--|--|--|--|
|Nástroje|Záměr|Řetězec|záměry [0]. záměr|"<b>Nástroje</b>. ShowNext"|
|Komunikace|Záměr|Řetězec|záměry [1]. záměr|<b>Komunikace</b>. StartOver"|
||Záměr|Řetězec|záměry [2]. záměr|NTato|


## <a name="data-from-entities"></a>Data z entit
Většina roboty chatu a aplikace potřebuje víc, než je název záměru. Tato další volitelná data pocházejí z entit zjištěných v utterance. Každý typ entity vrátí různé informace o shodě.

Jedno slovo nebo fráze v utterance se může shodovat s více než jednou entitou. V takovém případě se každá odpovídající entita vrátí se svým skóre.

Všechny entity se vrátí v poli **entity** odpovědi z koncového bodu.

## <a name="tokenized-entity-returned"></a>Byla vrácena entita s tokenem.

Přečtěte si [podporu tokenů](luis-language-support.md#tokenization) v Luis.


## <a name="prebuilt-entity-data"></a>Předem vytvořená data entity
[Předem připravené](luis-concept-entity-types.md) entity jsou zjištěny na základě regulárního výrazu, který se shoduje s použitím open source [rozpoznávacích výrazů – textový](https://github.com/Microsoft/Recognizers-Text) projekt. Předem připravené entity jsou vráceny v poli entity a používají název typu s předponou `builtin::` .

## <a name="list-entity-data"></a>Vypsat data entity

[Seznam entit](reference-entity-list.md) představuje pevně uzavřenou sadu příbuzných slov spolu s jejich synonymy. LUIS nezjistí další hodnoty pro entity seznamu. Pomocí funkce **doporučit** můžete zobrazit návrhy nových slov na základě aktuálního seznamu. Pokud existuje více než jedna entita seznamu se stejnou hodnotou, Každá entita se vrátí v dotazu koncového bodu.

## <a name="regular-expression-entity-data"></a>Data entity regulárního výrazu

[Entita regulárního výrazu](reference-entity-regular-expression.md) extrahuje entitu na základě regulárního výrazu, který zadáte.

## <a name="extracting-names"></a>Extrahování názvů
Získání názvů z utterance je obtížné, protože název může být skoro libovolná kombinace písmen a slov. V závislosti na tom, jaký typ názvu se chystáte extrahovat, máte několik možností. Následující návrhy nejsou pravidla, ale další pokyny.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Přidání předdefinovaných entit Person a GeographyV2

Entity [Person](luis-reference-prebuilt-person.md) a [GeographyV2](luis-reference-prebuilt-geographyV2.md) jsou k dispozici v některých [jazykových kulturách](luis-reference-prebuilt-entities.md).

### <a name="names-of-people"></a>Jména lidí

Jméno osoby může mít v závislosti na jazyku a jazykové verzi trochu mírné formátování. Použijte buď předem sestavenou entitu **[Person](luis-reference-prebuilt-person.md)** , nebo **[jednoduchou entitu](luis-concept-entity-types.md)** s rolemi jména a příjmení.

Pokud používáte jednoduchou entitu, nezapomeňte uvést příklady, které používají jméno a příjmení v různých částech utterance, v projevy různých délek a projevy napříč všemi záměry, včetně záměru None. Pravidelně [kontrolujte](./luis-how-to-review-endpoint-utterances.md) koncový bod projevy, abyste mohli popsat všechny názvy, které nebyly předpovídat správně.

### <a name="names-of-places"></a>Názvy míst

Názvy umístění se nastavují a označují jako města, okresy, stavy, provincie a země nebo oblasti. K extrakci informací o poloze použijte předem sestavenou entitu **[geographyV2](luis-reference-prebuilt-geographyv2.md)** .

### <a name="new-and-emerging-names"></a>Nové a vycházející názvy

Některé aplikace musí být schopné najít nové a nově vznikající názvy, jako jsou produkty nebo společnosti. Tyto typy názvů jsou nejobtížnějším typem extrakce dat. Začněte **[jednoduchou entitou](luis-concept-entity-types.md#simple-entity)** a přidejte [seznam frází](luis-concept-feature.md). Pravidelně [kontrolujte](./luis-how-to-review-endpoint-utterances.md) koncový bod projevy, abyste mohli popsat všechny názvy, které nebyly předpovídat správně.

## <a name="patternany-entity-data"></a>Vzor. libovolná data entity

[Pattern. any](reference-entity-pattern-any.md) je zástupný symbol s proměnlivou délkou, který se používá jenom v šabloně vzoru utterance k označení, kde začíná a končí entita. Aby bylo možné použít vzor, musí být entita použitá ve vzoru nalezena.

## <a name="sentiment-analysis"></a>Analýza mínění
Pokud je při [publikování](luis-how-to-publish-app.md#sentiment-analysis)nakonfigurovaná analýza mínění, odpověď Luis JSON zahrnuje analýzu mínění. Další informace o analýze mínění najdete v dokumentaci k [Analýza textu](../text-analytics/index.yml) .

## <a name="key-phrase-extraction-entity-data"></a>Data entity extrakce klíčových frází
[Entita pro extrakci klíčových frází](luis-reference-prebuilt-keyphrase.md) vrátí klíčové fráze v utterance, které poskytuje [Analýza textu](../text-analytics/index.yml).

## <a name="data-matching-multiple-entities"></a>Data, která odpovídají více entitám

LUIS vrátí všechny entity zjištěné ve utterance. V důsledku toho může být potřeba, aby váš Chat Bot mohl na základě výsledků udělat rozhodnutí.

## <a name="data-matching-multiple-list-entities"></a>Data, která odpovídají více entitám seznamu

Pokud slovo nebo fráze odpovídá více entitám seznamu, dotaz koncového bodu vrátí každou entitu seznamu.

Pro dotaz `when is the best time to go to red rock?` a aplikace má slovo `red` ve více než jednom seznamu, Luis rozpoznává všechny entity a vrátí pole entit jako součást odpovědi koncového bodu JSON.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak přidat entity do aplikace LUIS, najdete v tématu věnovaném [Přidání entit](luis-how-to-add-entities.md) .
