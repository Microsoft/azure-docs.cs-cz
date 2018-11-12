---
title: 'Kurz 3: Data nalezená pomocí regulárního výrazu – extrahování správně formátovaných dat'
titleSuffix: Azure Cognitive Services
description: Extrahování konzistentně formátovaných dat z promluvy s využitím entity regulárního výrazu
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 82d7e5ab57d9cf12c6917386282182faacb07725
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282380"
---
# <a name="tutorial-3-extract-well-formatted-data"></a>Kurz 3: Extrahování správně naformátovaných dat
V tomto kurzu změníte aplikaci Human Resources, aby extrahovala konzistentně formátovaná data z promluvy pomocí entity **regulárního výrazu**.

Účelem entity je extrahovat důležitá data obsažená v promluvě. Aplikace využívá entitu regulárního výrazu k získání formátovaných čísel formulářů lidských zdrojů z promluvy. Když se záměr promluvy vždy určuje pomocí strojového učení, tento konkrétní typ entity se pomocí strojového učení nezískává. 

**Mezi ukázkové promluvy patří:**

```
Where is HRF-123456?
Who authored HRF-123234?
HRF-456098 is published in French?
HRF-456098
HRF-456098 date?
HRF-456098 title?
```
 
Regulární výraz je pro tento typ dat vhodný, když:

* jsou data správně naformátovaná

**V tomto kurzu se naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Používat existující ukázkovou aplikaci
> * Přidání záměru FindForm (Vyhledat formulář)
> * Přidání entity regulárního výrazu 
> * Trénování
> * Publikování
> * Zjistit záměry a entity z koncového bodu

[!INCLUDE[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Použití existující aplikace
Pokračujte s aplikací **HumanResources**, kterou jste vytvořili v posledním kurzu. 

Pokud aplikaci HumanResources z předchozího kurzu nemáte, postupujte takto:

1. Stáhněte si [soubor JSON aplikace](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-prebuilts-HumanResources.json) a uložte si ho.

2. Naimportujte soubor JSON do nové aplikace.

3. V části **Manage** (Správa) na kartě **Versions** (Verze) naklonujte verzi a pojmenujte ji `regex`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL. 

## <a name="findform-intent"></a>Záměr FindForm (Vyhledat formulář)

1. [!INCLUDE[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Vyberte **Create new intent** (Vytvořit nový záměr). 

3. V automaticky otevíraném dialogovém okně zadejte `FindForm` a pak vyberte **Done** (Hotovo). 

    ![Snímek obrazovky s dialogovým oknem Create new intent (Vytvořit nový záměr) s textem Utilities (Nástroje) ve vyhledávacím poli](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

4. Přidejte do záměru ukázkové promluvy.

    |Ukázkové promluvy|
    |--|
    |What is the URL for hrf-123456? (Jaká je adresa URL formuláře hrf-123456?)|
    |Where is hrf-345678? (Kde je hrf-345678?)|
    |When was hrf-456098 updated? (Kdy se aktualizoval formulář hrf-456098?)|
    |Did John Smith update hrf-234639 last week? (Aktualizoval John Smith minulý týden hrf-234639?)|
    |How many version of hrf-345123 are there? (Kolik verzí hrf-345123 existuje?)|
    |Who needs to authorize form hrf-123456? (Kdo musí autorizovat hrf-123456?)|
    |How many people need to sign off on hrf-345678? (Kolik lidí musí podepsat hrf-345678?)|
    |hrf-234123 date? (datum hrf-234123?)|
    |author of hrf-546234? (autor hrf-546234?)|
    |title of hrf-456234? (název hrf-456234?)|

    [ ![Snímek obrazovky se stránkou záměru a zvýrazněnými novými promluvami](./media/luis-quickstart-intents-regex-entity/findform-intent.png) ](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    Aplikace má z předchozího kurzu přidanou předem připravenou entitu čísla, takže je každé číslo formuláře označené. Pro klientskou aplikaci to může být dostačující, ale číslo nebude označené typem čísla. Vytvoření nové entity s odpovídajícím názvem umožní klientské aplikaci odpovídajícím způsobem zpracovat entitu po vrácení ze služby LUIS.

    [!INCLUDE[Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="regular-expression-entity"></a>Entiay regulárního výrazu 
Entita regulárního výrazu, který hledá shodu s číslem formuláře, je `hrf-[0-9]{6}`. Tento regulární výraz hledá shodu se znaky literálů `hrf-`, ale ignoruje velikost a kulturní varianty. Hledá shodu s číslicemi 0–9 o délce přesně 6 číslic.

HRF je zkratka pro `human resources form`.

Služba LUIS promluvu při přidání do záměru tokenizuje. Tokenizací těchto promluv se přidají mezery před a za spojovník: `Where is HRF - 123456?`. Regulární výraz se použije na promluvu v nezpracované podobě ještě před tím, než se tokenizuje. Vzhledem k tomu, že se regulární výraz používá na _nezpracovanou_ podobu, nemusí se starat o hranice slov. 

Podle následujících kroků vytvořte entitu regulárního výrazu, která službě LUIS poskytne informace o formátu HRF-number:

1. Na levém panelu vyberte **Entities** (Entity).

2. Na stránce Entities (Entity) vyberte tlačítko **Create new entity** (Vytvořit novou entitu). 

3. V automaticky otevíraném okně zadejte název nové entity `HRF-number`, jako typ entity vyberte **RegEx** (Regulární výraz), jako hodnotu `hrf-[0-9]{6}`RegEx**zadejte** a pak vyberte **Done** (Hotovo).

    ![Snímek obrazovky s automaticky otevíraným dialogovým oknem s nastavením vlastností nové entity](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

4. V levé nabídce vyberte **Intents** (Záměry) a pak záměr **FindForm** (Vyhledat formulář), aby se v promluvách zobrazil označený regulární výraz. 

    [![Snímek obrazovky s promluvami s označenou stávající entitou a vzorem regulárního výrazu](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Vzhledem k tomu, že entita není strojově naučená, se popisek na promluvy použije a zobrazí se na webu LUIS okamžitě po vytvoření.

## <a name="train"></a>Trénování

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publikování

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Získání záměru a entit z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Na konec adresy URL zadejte `When were HRF-123456 and hrf-234567 published in the last year?`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měl by se vrátit záměr `FindForm` se dvěma čísly formuláře `HRF-123456` a `hrf-234567`.

    ```JSON
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9993477
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9993477
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0206110049
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00533067342
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.004215215
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00209096959
        },
        {
          "intent": "None",
          "score": 0.0017655947
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00109490135
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.0005704638
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.000525338168
        }
      ],
      "entities": [
        {
          "entity": "last year",
          "type": "builtin.datetimeV2.daterange",
          "startIndex": 53,
          "endIndex": 61,
          "resolution": {
            "values": [
              {
                "timex": "2017",
                "type": "daterange",
                "start": "2017-01-01",
                "end": "2018-01-01"
              }
            ]
          }
        },
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        },
        {
          "entity": "-234567",
          "type": "builtin.number",
          "startIndex": 28,
          "endIndex": 34,
          "resolution": {
            "value": "-234567"
          }
        }
      ]
    }
    ```

    Čísla v promluvách se vrátí dvakrát, jednou jako nová entita `hrf-number` a jednou jako předem připravená entita `number`. Jak ukazuje tento příklad, promluva může obsahovat více než jednu entitu a více než jednu entitu stejného typu. Pomocí entity regulárního výrazu služba LUIS extrahuje pojmenovaná data, což usnadňuje programové použití v klientské aplikaci, která přijímá odpověď JSON.


## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili nový záměr, přidali příklady promluv a pak vytvořili entitu regulárního výrazu k extrahování správně formátovaných data z promluv. Po natrénování a publikování aplikace jste dotazem adresovaným koncovému bodu zjistili záměr a vrátili extrahovaná data.

> [!div class="nextstepaction"]
> [Informace o entitě seznamu](luis-quickstart-intent-and-list-entity.md)

