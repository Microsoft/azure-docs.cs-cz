---
title: Test aplikace portálu služby LUIS
titleSuffix: Language Understanding - Azure Cognitive Services
description: Language Understanding (LUIS) umožňuje neustále pracovat na aplikaci zpřesnit jej a zvýšit jeho umožňující porozumět jazyku.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 76db86f8ddaee43654689e78437d3cdbd1c211f6
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54883020"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Otestujte aplikaci LUIS na portálu služby LUIS
<a name="train-your-app"></a>
[Testování](luis-concept-test.md) aplikace je iterativní proces. Po školení aplikace LUIS, otestujte ho s ukázka projevy a zjistěte, jestli jsou správně rozpoznány záměry a entity. Pokud ne, proveďte aktualizace znovu na aplikaci LUIS, trénování a testování. 

<!-- anchors for H2 name changes -->
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="test-an-utterance"></a>Testování utterance

1. Přístup k aplikaci tak, že vyberete jeho název na **Moje aplikace** stránky. 

2. Pro přístup k **testovací** snímku na více instancí panelu, vyberte **Test** v horním panelu vaší aplikace.

    ![Aplikace Test pro trénování a stránky](./media/luis-how-to-interactive-test/test.png)

3. Zadejte utterance do textového pole a stiskněte Enter. Můžete zadat tolik testovacích projevy, jak chcete, aby **testování**, ale pouze jeden utterance najednou.

4. Utterance, hlavní záměr a skóre jsou přidány do seznamu projevy pod textovým polem.

    ![Interaktivní testování identifikuje nesprávné záměr](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Kontrola skóre

Prozkoumejte podrobnosti o výsledku testu v **zkontrolujte, jestli se** panelu. 
 
1. S **testovací** snímku na více instancí panel otevřený, vyberte **zkontrolujte, jestli se** pro utterance chcete porovnat. 

    ![Vyberte tlačítko zkontrolujte, jestli chcete zobrazit další podrobnosti o výsledcích testů](./media/luis-how-to-interactive-test/inspect.png)

2. **Kontroly** otevře se panel. Na panelu zahrnuje nejvyšší hodnocení záměr, stejně jako všechny zjištěné entity. Na panelu zobrazuje výsledek vybraný utterance.

    ![Na panelu zahrnuje nejvyšší hodnocení záměr, stejně jako všechny zjištěné entity. Na panelu zobrazuje výsledek vybraný utterance.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Správné nejvyšší hodnocení záměr

1. Pokud je nesprávná horní vyhodnocování záměr, vyberte **upravit** tlačítko.

2.  V rozevíracím seznamu vyberte správné záměr pro utterance.

    ![Vyberte správný záměr](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Zobrazení výsledků mínění

Pokud **analýzu subjektivního hodnocení** je nakonfigurovaná na **[publikovat](luis-how-to-publish-app.md#enable-sentiment-analysis)** stránky, výsledky testů zahrnují mínění v utterance. 

![Obrázek testovací podokno s analýzou mínění](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Opravte odpovídající vzor záměr

Pokud používáte [vzory](luis-concept-patterns.md) a utterance odpovídá vzoru, ale byl nesprávný záměr předpovědět, vyberte **upravit** propojit vzorec a potom vyberte správné záměr.

## <a name="compare-with-published-version"></a>Porovnat s publikovanou verzi

Aktivní verzi aplikace s využitím publikovanému můžete otestovat [koncový bod](luis-glossary.md#endpoint) verze. V **zkontrolujte, jestli se** panelu, vyberte **porovnat s publikované**. Testování publikované modelu se odečte od kvóty zůstatek vašeho předplatného Azure. 

![Porovnat s publikování](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Zobrazit koncový bod JSON v panelu testu
Můžete zobrazit koncový bod vrátil JSON pro porovnání tak, že vyberete **zobrazení zobrazit JSON**.

![Publikované odpověď JSON](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Další nastavení panelu testu

### <a name="luis-endpoint"></a>Koncový bod služby LUIS

Pokud máte několik koncových bodů služby LUIS, použijte **další nastavení** publikování odkazu na testovací podokno, chcete-li změnit koncový bod použitý pro testování. Pokud si nejste jisti, který koncový bod používat, vyberte výchozí **Starter_Key**. 

![Test panelu se zvýrazněným odkazem další nastavení](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)


### <a name="view-bing-spell-check-corrections-in-test-panel"></a>Zobrazení pro kontrolu pravopisu Bingu oprav v panelu testu

Požadavky na zobrazení opravy pravopisu: 

* Publikované aplikace
* Pro kontrolu pravopisu Bingu [klíč služby](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api). Klíč služby není uložený a je potřeba resetovat pro každou relaci prohlížeče. 

Pomocí následujícího postupu Pokud chcete zahrnout [kontrolu pravopisu Bingu v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) v podokně Výsledky testu. 

1. V **Test** podokně, zadejte utterance. Když očekává se, utterance, vyberte **[zkontrolujte, jestli se](#inspect-score)** pod utterance jste zadali. 

2. Když **zkontrolujte, jestli se** panelu otevře, vyberte  **[porovnat s publikovanou](#compare-with-published-version)**. 

3. Když **publikováno** panelu otevře, vyberte  **[další nastavení](#additional-settings-in-test-panel)**.

4. V místním dialogovém okně zadejte vaše **kontrolu pravopisu Bingu** klíč služby. 
    ![Zadejte klíč služby pro kontrolu pravopisu Bingu](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)

5. Zadejte dotaz s nesprávné pravopisu, jako `book flite to seattle` a vyberte možnost enter. Nesprávný pravopis slova `flite` nahrazuje ve dotaz odeslaný do služby LUIS a výsledné JSON ukazuje obě původní dotaz, jako `query`a opravený pravopis v dotazu jako `alteredQuery`.

    ![Oprava kontroly pravopisu JSON](./media/luis-how-to-interactive-test/interactive-with-spell-check-results.png)

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

## <a name="batch-testing"></a>Dávkové testování
V části Testování služby batch [koncepty](luis-concept-batch-test.md) a zjistěte, [jak](luis-how-to-batch-test.md) testování dávku projevy.

## <a name="next-steps"></a>Další postup

Pokud testování označuje, že aplikace LUIS nedokáže rozpoznat správný záměry a entity, můžete pracovat zvyšte přesnost aplikace LUIS označování další projevy nebo přidáním funkcí. 

* [Popisek navrhované projevy služba luis](luis-how-to-review-endoint-utt.md) 
* [Použití funkcí pro zvýšení výkonu aplikace LUIS](luis-how-to-add-features.md) 
