---
title: Kurz kontroly promluv koncového bodu ve službě Language Understanding (LUIS) – Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak kontrolovat promluvy koncového bodu v doméně lidských zdrojů (HR) ve službě LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 07/03/2018
ms.author: diberry
ms.openlocfilehash: 1f1e3310e0d02983aaecc3f87ba9c116d65b751b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237209"
---
# <a name="tutorial-review-endpoint-utterances"></a>Kurz: Kontrola promluv koncového bodu
V tomto kurzu zlepšíte predikce aplikace ověřením nebo opravou promluv získaných prostřednictvím koncového bodu HTTP služby LUIS. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Vysvětlení kontroly promluv koncového bodu 
> * Použití aplikace LUIS pro doménu lidských zdrojů 
> * Kontrola promluv koncového bodu
> * Trénování a publikování aplikace
> * Odeslání dotazu na koncový bod aplikace a zobrazení odpovědi JSON ze služby LUIS

Pro účely tohoto článku potřebujete bezplatný účet [LUIS](luis-reference-regions.md#luis-website), abyste mohli vytvořit svou aplikaci LUIS.

## <a name="before-you-begin"></a>Než začnete
Pokud nemáte aplikaci pro lidské zdroje z kurzu k [mínění](luis-quickstart-intent-and-sentiment-analysis.md), naimportujte ji z úložiště [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-sentiment-HumanResources.json) na Githubu. Pokud tento kurz používáte jako novou naimportovanou aplikaci, musíte ji také natrénovat, publikovat a potom přidat promluvy do koncového bodu pomocí [skriptu](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/demo-upload-endpoint-utterances/endpoint.js) nebo z koncového bodu v prohlížeči. Přidávané promluvy jsou tyto:

   [!code-nodejs[Node.js code showing endpoint utterances to add](~/samples-luis/examples/demo-upload-endpoint-utterances/endpoint.js?range=15-26)]

Pokud chcete zachovat původní aplikaci pro lidské zdroje, naklonujte verzi na stránce [Settings](luis-how-to-manage-versions.md#clone-a-version) (Nastavení) a pojmenujte ji `review`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. 

Pokud máte díky sérii kurzů všechny verze této aplikace, může vás u některých verzí překvapit, že se seznam **Kontrola promluv koncového bodu** nezmění. Je jenom jeden fond promluv ke kontrole bez ohledu na to, jakou verzi promluvy právě editujete nebo jaká verze aplikace byla publikovaná v koncovém bodě. 

## <a name="purpose-of-reviewing-endpoint-utterances"></a>Účel kontroly promluv koncového bodu
Tento proces kontroly představuje další způsob, jakým se LUIS seznamuje s vaší aplikační doménou. Služba LUIS vybrala projevy v seznamu revizí. Tento seznam:

* Je specifický pro danou aplikaci.
* Má za cíl zlepšit přesnost předpovědí aplikace. 
* Měl by se pravidelně kontrolovat. 

Kontrolou projevů koncového bodu ověřujete nebo opravujete předpokládaný záměr promluvy. Označíte také vlastní entity, které nebyly předpokládané. 

## <a name="review-endpoint-utterances"></a>Kontrola promluv koncového bodu

1. Ujistěte se, že je vaše aplikace pro lidské zdroje uvedená v části **Build** (Sestavení) služby LUIS. Do této části můžete přejít výběrem možnosti **Build** (Sestavit) v pravém horním řádku nabídek. 

    [ ![Snímek obrazovky aplikace LUIS se zvýrazněnou možností Build (Sestavit) na pravém horním navigačním panelu](./media/luis-tutorial-review-endpoint-utterances/first-image.png)](./media/luis-tutorial-review-endpoint-utterances/first-image.png#lightbox)

1. V levém navigačním panelu vyberte **Review endpoint utterances** (Kontrola promluv koncového bodu). Seznam je filtrovaný pro záměr **ApplyForJob**. 

    [ ![Snímek obrazovky s tlačítkem pro kontrolu promluv koncového bodu na levém navigačním panelu](./media/luis-tutorial-review-endpoint-utterances/entities-view-endpoint-utterances.png)](./media/luis-tutorial-review-endpoint-utterances/entities-view-endpoint-utterances.png#lightbox)

2. Přepněte zobrazení entit (**Entities view**), aby se zobrazily označené entity. 
    
    [ ![Snímek obrazovky kontroly promluv koncového bodu se zvýrazněným přepínačem zobrazení entit](./media/luis-tutorial-review-endpoint-utterances/select-entities-view.png)](./media/luis-tutorial-review-endpoint-utterances/select-entities-view.png#lightbox)

    |Promluva|Správný záměr|Chybějící entity|
    |:--|:--|:--|
    |I'm looking for a job with Natural Language Processing|GetJobInfo|Job – "Natural Language Process"|

    Tato promluva není ve správném záměru a má skóre menší než 50 %. Záměr **ApplyForJob** má 21 promluv, zatímco záměr **GetJobInformation** má sedm promluv. Současně se správným zařazením promluv koncového bodu by se do záměru **GetJobInformation** měly přidat další promluvy. To necháme na vás jako cvičení, které si můžete provést sami. Každý záměr s výjimkou záměru **None** by měl mít přibližně stejný počet ukázkových promluv. Záměr **None** by měl mít 10 % z celkového počtu promluv v aplikaci. 

    V zobrazení tokenů (**Tokens View**) můžete najetím myši na libovolný modrý text zobrazit predikovaný název entity. 

3. Pro promluvu `I'm looking for a job with Natual Language Processing` vyberte správný záměr **GetJobInformation** ve sloupci **Aligned intent** (Nastavený záměr). 

    [ ![Snímek obrazovky kontroly promluv koncového bodu se zařazováním promluv k záměrům](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png)](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png#lightbox)

4. Entitou pro `Natural Language Processing` ve stejné promluvě je keyPhrase. Místo toho by to měla být entita **Job**. Vyberte `Natural Language Processing` a potom ze seznamu vyberte entitu **Job**.

    [ ![Snímek obrazovky kontroly promluv koncového bodu s označováním entit v promluvě](./media/luis-tutorial-review-endpoint-utterances/label-entity.png)](./media/luis-tutorial-review-endpoint-utterances/label-entity.png#lightbox)

5. Na stejném řádku vyberte značku zaškrtnutí v kroužku ve sloupci **Add to aligned intent** (Přidat do nastaveného záměru). 

    [ ![Snímek finalizace zařazování promluv v záměru](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png)](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png#lightbox)

    Tato akce přesune promluvu ze seznamu **Review endpoint utterances** (Kontrola promluv koncového bodu) do záměru **GetJobInformation**. Promluva koncového bodu je teď příkladem promluvy pro tento záměr. 

6. Zkontrolujte zbývající promluvy v tomto záměru, označte je a opravte nastavený záměr (**Aligned intent**), pokud je nesprávný.

7. Jakmile jsou všechny promluvy správné, vyberte zaškrtávací políčko na každém řádku a potom výběrem **Add selected** (Přidat vybrané) nastavte jejich správné zařazení. 

    [ ![Snímek obrazovky finalizace zbývajících promluv v nastaveném záměru](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png)](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png#lightbox)

8. Seznam už by toto promluvy neměl obsahovat. Pokud se zobrazí další promluvy, pokračujte v práci s tímto seznamem a opravujte záměry a označujte všechny chybějící entity, dokud tento seznam není prázdný. Vyberte další záměr v seznamu filtru a potom pokračujte v opravách promluv a označování entit. Nezapomeňte, že posledním krokem každého záměru je buď vybrat **Add to aligned intent** (Přidat do nastaveného záměru) na řádku promluvy, nebo zaškrtnout políčko vedle každého záměru a vybrat **Add selected** (Přidat vybrané) nad tabulkou. 

    Tato aplikace je velmi malá. Celý proces kontroly trvá jenom několik minut.

## <a name="add-new-job-name-to-phrase-list"></a>Přidání nového názvu pracovní pozice do seznamu frází
Aktualizujte seznam frází každým nově zjištěným názvem pracovní pozice. 

1. V levém navigačním panelu vyberte **Phrase lists** (Seznam frází).

2. V seznamu frází vyberte **Jobs** (Pracovní pozice).

3. Jako hodnotu přidejte `Natural Language Processing` a potom vyberte **Uložit**. 

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS
Služba LUIS nemá o těchto změnách informace, dokud se nenatrénuje. 

1. V pravé horní části webu LUIS vyberte tlačítko **Train** (Trénovat).

2. Trénování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikování aplikace a získání adresy URL koncového bodu
Abyste mohli využít aktualizovaný model aplikace LUIS v chatbotu nebo jiné aplikaci, musíte aplikaci publikovat. 

1. V pravé horní části webu LUIS vyberte tlačítko **Publish** (Publikovat). 

2. Pokud jste tuto aplikaci naimportovali, musíte vybrat **Sentiment analysis** (Analýza mínění). 

3. Vyberte slot Production (Produkční) a tlačítko **Publish** (Publikovat).

4. Publikování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

## <a name="query-the-endpoint-with-an-utterance"></a>Odeslání dotazu na koncový bod s promluvou
Vyzkoušejte promluvu blízkou opravené promluvě. 

1. V dolní části stránky **Publish** (Publikovat) vyberte odkaz na **koncový bod**. Tato akce otevře další okno prohlížeče s adresou URL koncového bodu v adresním řádku. 

2. Na konec adresy URL zadejte `Are there any natural language processing jobs in my department right now?`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. 

```JSON
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
Některé z promluv koncového bodu budou mít v seznamu kontrol vysoký počet procent. Tyto promluvy musíte přesto zkontrolovat a ověřit. Jsou v tomto seznamu proto, že další záměr má skóre příliš blízko skóre nejlepšího záměru. 

## <a name="what-has-this-tutorial-accomplished"></a>Co bylo účelem tohoto kurzu?
Kontrolou projevů z koncového bodu se zvýšila přesnost predikcí této aplikace. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už aplikaci LUIS nepotřebujete, odstraňte ji. V nabídce vlevo nahoře vyberte **Moje aplikace**. Vyberte tři tečky **...** vpravo od názvu aplikace v seznamu aplikací a potom vyberte **Delete** (Odstranit). V automaticky otevíraném dialogovém okně **Delete app?** (Odstranit aplikaci?) vyberte **Ok**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Jak používat vzory](luis-tutorial-pattern.md)
