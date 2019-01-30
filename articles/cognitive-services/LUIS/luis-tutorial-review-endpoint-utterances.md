---
title: Kontrola projevy koncového bodu
titleSuffix: Azure Cognitive Services
description: Vylepšete predikce aplikace ověřením nebo opravou promluv získaných prostřednictvím koncového bodu HTTP služby LUIS, které nemusí být správně naučené. U některých promluv může být potřeba zkontrolovat záměr, zatímco u jiných entitu.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 30927f4a8213f3ca8b94a2c799e6204be003e2a7
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55213198"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Kurz: Oprava jisti predikcí kontrolou projevy koncového bodu
V tomto kurzu vylepšíte predikce aplikace ověřením nebo opravou promluv získaných prostřednictvím koncového bodu HTTPS služby LUIS, které nemusí mít tato služba správně naučené. U některých promluv může být potřeba zkontrolovat záměr, zatímco u jiných entitu. Promluvy koncového bodu byste měli pravidelně kontrolovat v rámci plánované údržby služby LUIS. 

Tento proces kontroly představuje další způsob, jakým se LUIS seznamuje s vaší aplikační doménou. Služba LUIS vybrala promluvy zobrazené v seznamu revizí. Tento seznam:

* Je specifický pro danou aplikaci.
* Má za cíl zlepšit přesnost předpovědí aplikace. 
* Měl by se pravidelně kontrolovat. 

Kontrolou projevů koncového bodu ověřujete nebo opravujete předpokládaný záměr promluvy. Označíte také vlastní entity, které nebyly předpovězené, nebo jejichž předpověď byla nesprávná. 

**V tomto kurzu se naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importovat ukázková aplikace
> * Kontrola promluv koncového bodu
> * Aktualizace seznamu frází
> * Vytrénovat aplikaci
> * Publikování aplikace
> * Odeslání dotazu na koncový bod aplikace a zobrazení odpovědi JSON ze služby LUIS

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importovat ukázková aplikace

Pokračujte s aplikací **HumanResources**, kterou jste vytvořili v posledním kurzu. 

Použijte k tomu následující postup:

1.  Stáhněte si [soubor JSON aplikace](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json) a uložte si ho.

2. Naimportujte soubor JSON do nové aplikace.

3. V části **Manage** (Správa) na kartě **Versions** (Verze) naklonujte verzi a pojmenujte ji `review`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL.

    Pokud tento kurz používáte jako novou naimportovanou aplikaci, musíte ji také natrénovat, publikovat a potom přidat promluvy do koncového bodu pomocí [skriptu](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/demo-upload-endpoint-utterances/endpoint.js) nebo z koncového bodu v prohlížeči. Přidávané promluvy jsou tyto:

   [!code-nodejs[Node.js code showing endpoint utterances to add](~/samples-luis/examples/demo-upload-endpoint-utterances/endpoint.js?range=15-26)]

    Pokud máte díky sérii kurzů všechny verze této aplikace, může vás u některých verzí překvapit, že se seznam **Kontrola promluv koncového bodu** nezmění. Je jen jeden fond promluv ke kontrole bez ohledu na to, jakou verzi právě editujete nebo jaká verze aplikace byla publikovaná v koncovém bodě. 

## <a name="review-endpoint-utterances"></a>Kontrola promluv koncového bodu

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. V levém navigačním panelu vyberte **Review endpoint utterances** (Kontrola promluv koncového bodu). Seznam je filtrovaný pro záměr **ApplyForJob**. 

    [ ![Snímek obrazovky s tlačítkem pro kontrolu promluv koncového bodu na levém navigačním panelu](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png#lightbox)

3. Přepněte zobrazení entit (**Entities view**), aby se zobrazily označené entity. 
    
    [ ![Snímek obrazovky kontroly promluv koncového bodu se zvýrazněným přepínačem zobrazení entit](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png#lightbox)

    |Promluva|Správný záměr|Chybějící entity|
    |:--|:--|:--|
    |I'm looking for a job with Natural Language Processing|GetJobInfo|Job – "Natural Language Process"|

    Tato promluva není ve správném záměru a má skóre menší než 50 %. Záměr **ApplyForJob** má 21 promluv, zatímco záměr **GetJobInformation** má sedm promluv. Současně se správným zařazením promluv koncového bodu by se do záměru **GetJobInformation** měly přidat další promluvy. To necháme na vás jako cvičení, které si můžete provést sami. Každý záměr s výjimkou záměru **None** by měl mít přibližně stejný počet ukázkových promluv. Záměr **None** by měl mít 10 % z celkového počtu promluv v aplikaci. 

4. Pro promluvu `I'm looking for a job with Natual Language Processing` vyberte správný záměr **GetJobInformation** ve sloupci **Aligned intent** (Nastavený záměr). 

    [ ![Snímek obrazovky kontroly promluv koncového bodu se zařazováním promluv k záměrům](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png)](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png#lightbox)

5. Entitou pro `Natural Language Processing` ve stejné promluvě je keyPhrase. Místo toho by to měla být entita **Job**. Vyberte `Natural Language Processing` a potom ze seznamu vyberte entitu **Job**.

    [ ![Snímek obrazovky kontroly promluv koncového bodu s označováním entit v promluvě](./media/luis-tutorial-review-endpoint-utterances/label-entity.png)](./media/luis-tutorial-review-endpoint-utterances/label-entity.png#lightbox)

6. Na stejném řádku vyberte značku zaškrtnutí v kroužku ve sloupci **Add to aligned intent** (Přidat do nastaveného záměru). 

    [ ![Snímek finalizace zařazování promluv v záměru](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png)](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png#lightbox)

    Tato akce přesune promluvu ze seznamu **Review endpoint utterances** (Kontrola promluv koncového bodu) do záměru **GetJobInformation**. Promluva koncového bodu je teď příkladem promluvy pro tento záměr. 

7. Zkontrolujte zbývající promluvy v tomto záměru, označte je a opravte nastavený záměr (**Aligned intent**), pokud je nesprávný.

8. Jakmile jsou všechny promluvy správné, vyberte zaškrtávací políčko na každém řádku a potom výběrem **Add selected** (Přidat vybrané) nastavte jejich správné zařazení. 

    [ ![Snímek obrazovky finalizace zbývajících promluv v nastaveném záměru](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png)](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png#lightbox)

9. Seznam už by toto promluvy neměl obsahovat. Pokud se zobrazí další promluvy, pokračujte v práci s tímto seznamem a opravujte záměry a označujte všechny chybějící entity, dokud tento seznam nebude prázdný. 

10. Vyberte další záměr v seznamu filtru a potom pokračujte v opravách promluv a označování entit. Nezapomeňte, že posledním krokem každého záměru je buď vybrat **Add to aligned intent** (Přidat do nastaveného záměru) na řádku promluvy, nebo zaškrtnout políčko vedle každého záměru a vybrat **Add selected** (Přidat vybrané) nad tabulkou.

    Pokračujte tak dlouho, dokud v seznamu filtru nebudou žádné záměry a entity. Tato aplikace je velmi malá. Celý proces kontroly trvá jenom několik minut. 

## <a name="update-phrase-list"></a>Aktualizace seznamu frází
Aktualizujte seznam frází každým nově zjištěným názvem pracovní pozice. 

1. V levém navigačním panelu vyberte **Phrase lists** (Seznam frází).

2. V seznamu frází vyberte **Jobs** (Pracovní pozice).

3. Jako hodnotu přidejte `Natural Language Processing` a potom vyberte **Uložit**. 

## <a name="train"></a>Trénování

Služba LUIS nemá o těchto změnách informace, dokud se nenatrénuje. 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publikování

Pokud jste tuto aplikaci naimportovali, musíte vybrat **Sentiment analysis** (Analýza mínění).

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Získání záměru a entit z koncového bodu

Vyzkoušejte promluvu blízkou opravené promluvě. 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Na konec adresy URL zadejte `Are there any natural language processing jobs in my department right now?`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. 

  ```json
  {
    "query": "are there any natural language processing jobs in my department right now?",
    "topScoringIntent": {
      "intent": "GetJobInformation",
      "score": 0.9247605
    },
    "intents": [
      {
        "intent": "GetJobInformation",
        "score": 0.9247605
      },
      {
        "intent": "ApplyForJob",
        "score": 0.129989788
      },
      {
        "intent": "FindForm",
        "score": 0.006438211
      },
      {
        "intent": "EmployeeFeedback",
        "score": 0.00408575451
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00194211153
      },
      {
        "intent": "None",
        "score": 0.00166400627
      },
      {
        "intent": "Utilities.Help",
        "score": 0.00118593348
      },
      {
        "intent": "MoveEmployee",
        "score": 0.0007885918
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.0006373631
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0005980781
      },
      {
        "intent": "Utilities.Confirm",
        "score": 3.719905E-05
      }
    ],
    "entities": [
      {
        "entity": "right now",
        "type": "builtin.datetimeV2.datetime",
        "startIndex": 64,
        "endIndex": 72,
        "resolution": {
          "values": [
            {
              "timex": "PRESENT_REF",
              "type": "datetime",
              "value": "2018-07-05 15:23:18"
            }
          ]
        }
      },
      {
        "entity": "natural language processing",
        "type": "Job",
        "startIndex": 14,
        "endIndex": 40,
        "score": 0.9869922
      },
      {
        "entity": "natural language processing jobs",
        "type": "builtin.keyPhrase",
        "startIndex": 14,
        "endIndex": 45
      },
      {
        "entity": "department",
        "type": "builtin.keyPhrase",
        "startIndex": 53,
        "endIndex": 62
      }
    ],
    "sentimentAnalysis": {
      "label": "positive",
      "score": 0.8251864
    }
  }
  }
  ```

  Správný záměr je predikovaný s vysokým skóre a entita **Job** je detekovaná jako `natural language processing`. 

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>Dá se kontrola nahradit přidáním dalších promluv? 
Asi vás napadá, proč nepřidat další ukázkové promluvy. Jaký je účel kontrol promluv koncového bodu? V reálných aplikacích LUIS jsou promluvy koncového bodu od uživatelů a mají volbu slov a uspořádání, které jste ještě nepoužili. Pokud jste použili stejnou volbu slov a uspořádání, původní predikce by měla vyšší procentní hodnocení. 

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Proč je hlavní záměr uvedený v seznamu promluv? 
Některé z promluv koncového bodu budou mít v seznamu kontrol vysoké skóre předpovědi. Tyto promluvy musíte přesto zkontrolovat a ověřit. Jsou v tomto seznamu proto, že další záměr má skóre příliš blízko skóre nejlepšího záměru. Mezi jednotlivými hlavními záměry je dobré mít asi 15% rozdíl.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další postup
V tomto kurzu jste zkontrolovali promluvy získané z koncového bodu, které nemusela mít služba LUIS správně naučené. Jakmile se takové promluvy ověří a přesunou jako ukázkové promluvy do správných záměrů, zlepší se ve službě LUIS přesnost předpovědí.

> [!div class="nextstepaction"]
> [Jak používat vzory](luis-tutorial-pattern.md)
