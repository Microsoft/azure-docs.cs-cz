---
title: Test aplikace portálu služby LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) umožňuje neustále pracovat na aplikaci zpřesnit jej a zvýšit jeho umožňující porozumět jazyku.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 628547e8254bb0055cf1f09af50e79b68311a759
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221729"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Testování aplikace LUIS na portálu LUIS

[Testování](luis-concept-test.md) aplikace je iterativní proces. Po školení aplikace LUIS, otestujte ho s ukázka projevy a zjistěte, jestli jsou správně rozpoznány záměry a entity. Pokud ne, proveďte aktualizace znovu na aplikaci LUIS, trénování a testování. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Výuka před testováním

Chcete-li provést test na nejnovější verzi aktivní aplikace, vyberte před testováním z horní nabídky možnost **výuka** . 

## <a name="test-an-utterance"></a>Testování utterance

Utterance testu by neměl být přesně stejný jako jakýkoli příklad projevy v aplikaci. Testovací utterance by měl obsahovat volbu Word, délku fráze a využití entity, které očekáváte pro uživatele. 

1. Přístup k aplikaci získáte tak, že na stránce **Moje aplikace** vyberete její název. 

1. Pro přístup k panelu **testovacího** snímku vyberte v horním panelu aplikace **test** .

    > [!div class="mx-imgBorder"]
    > Stránka aplikace ![& testování](./media/luis-how-to-interactive-test/test.png)

1. Zadejte utterance do textového pole a stiskněte Enter. V **testu**můžete zadat tolik projevy testů, ale pouze jeden utterance v jednom okamžiku.

1. Utterance, hlavní záměr a skóre jsou přidány do seznamu projevy pod textovým polem.

    ![Interaktivní testování identifikuje nesprávné záměr](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Kontrola skóre

Podrobnosti o výsledku testu si můžete prohlédnout na panelu **Kontrola** . 
 
1. V otevřeném panelu **testovacího** snímku vyberte **zkontrolovat** pro utterance, který chcete porovnat. 

    ![Kliknutím na tlačítko zkontrolovat zobrazíte další podrobnosti o výsledcích testu.](./media/luis-how-to-interactive-test/inspect.png)

1. Zobrazí se panel **Kontrola** . Na panelu zahrnuje nejvyšší hodnocení záměr, stejně jako všechny zjištěné entity. Na panelu zobrazuje výsledek vybraný utterance.

    ![Na panelu zahrnuje nejvyšší hodnocení záměr, stejně jako všechny zjištěné entity. Na panelu zobrazuje výsledek vybraný utterance.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Správné nejvyšší hodnocení záměr

1. Pokud je záměr nejvyšší bodování nesprávný, vyberte tlačítko **Upravit** .

1.  V rozevíracím seznamu vyberte správné záměr pro utterance.

    ![Vyberte správný záměr](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Zobrazení výsledků mínění

Pokud je na stránce **[publikovat](luis-how-to-publish-app.md#enable-sentiment-analysis)** nakonfigurována **Analýza mínění** , výsledky testu zahrnují mínění nalezený v utterance. 

![Obrázek testovací podokno s analýzou mínění](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Opravte odpovídající vzor záměr

Pokud používáte [vzory](luis-concept-patterns.md) , ale utterance odpovídá vzoru, ale nesprávný záměr byl předpovězen, vyberte odkaz **Upravit** podle vzoru a pak vyberte správný záměr.

## <a name="compare-with-published-version"></a>Porovnat s publikovanou verzi

Aktivní verzi aplikace můžete otestovat pomocí publikované verze [koncového bodu](luis-glossary.md#endpoint) . Na panelu **Kontrola** vyberte **Porovnat s publikováním**. Testování publikované modelu se odečte od kvóty zůstatek vašeho předplatného Azure. 

![Porovnat s publikování](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Zobrazit koncový bod JSON v panelu testu
Můžete zobrazit vrácený koncový bod JSON pro porovnání tím, že vyberete **zobrazení zobrazit JSON**.

![Publikované odpověď JSON](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Další nastavení panelu testu

### <a name="luis-endpoint"></a>Koncový bod služby LUIS

Pokud máte několik koncových bodů LUIS, použijte odkaz **Další nastavení** v podokně Publikováno v testu ke změně koncového bodu používaného pro testování. Pokud si nejste jisti, který koncový bod chcete použít, vyberte výchozí **Starter_Key**. 

> [!div class="mx-imgBorder"]
> ![testovací panel se zvýrazněným odkazem na další nastavení](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)

<!--
###  View Bing Spell Check corrections in test panel

Requirements to view the spelling corrections: 

* Published app
* Bing Spell Check [service key](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api). The service key is not stored and needs to be reset for each browser session. 

Use the following procedure to include the [Bing Spell Check v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) service  in the Test pane results. 

1. In the **Test** pane, enter an utterance. When the utterance is predicted, select **[Inspect](#inspect-score)** underneath the utterance you entered. 

1. When the **Inspect** panel opens, select **[Compare with Published](#compare-with-published-version)**. 

1. When the **Published** panel opens, select **[Additional Settings](#additional-settings-in-test-panel)**.

1. In the pop-up dialog, check **Enable Bing Spell Check** and enter the key, then select **Done**. 
    ![Enter Bing Spell Check service key](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key-text.png)

1. Enter a query with an incorrect spelling such as `book flite to seattle` and select enter. The incorrect spelling of the word `flite` is replaced in the query sent to LUIS and the resulting JSON shows both the original query, as `query`, and the corrected spelling in the query, as `alteredQuery`.

<a name="json-file-with-no-duplicates"></a>
<a name="import-a-dataset-file-for-batch-testing"></a>
<a name="export-rename-delete-or-download-dataset"></a>
<a name="run-a-batch-test-on-your-trained-app"></a>
<a name="access-batch-test-result-details-in-a-visualized-view"></a>
<a name="filter-chart-results-by-intent-or-entity"></a>
<a name="investigate-false-sections"></a>
<a name="view single-point utterance data"></a>
<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>
-->

## <a name="batch-testing"></a>Dávkové testování
Podívejte se na [Koncepty](luis-concept-batch-test.md) testování dávek a Naučte [se](luis-how-to-batch-test.md) testovat dávku projevy.

## <a name="next-steps"></a>Další kroky

Pokud testování označuje, že aplikace LUIS nedokáže rozpoznat správný záměry a entity, můžete pracovat zvyšte přesnost aplikace LUIS označování další projevy nebo přidáním funkcí. 

* [Popisek navržený projevy pomocí LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Využijte funkce ke zlepšení výkonu aplikace v LUIS.](luis-how-to-add-features.md) 
