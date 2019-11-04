---
title: Testovací aplikace na portálu LUIS
titleSuffix: Azure Cognitive Services
description: Pomocí Language Understanding (LUIS) můžete průběžně pracovat na vaší aplikaci a zdokonalit tak její porozumění jazyku.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: ebc86d1cf91cf79ab83b0f49d9898a91d8be8a75
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500280"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Testování aplikace LUIS na portálu LUIS

[Testování](luis-concept-test.md) aplikace je iterativní proces. Po školení aplikace LUIS otestujte ji pomocí Sample projevy a zjistěte, jestli jsou záměry a entity správně rozpoznané. Pokud nejsou, proveďte aktualizace aplikace LUIS, školení a testování znovu. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="test-an-utterance"></a>Testování utterance

1. Přístup k aplikaci získáte tak, že na stránce **Moje aplikace** vyberete její název. 

1. Pro přístup k panelu **testovacího** snímku vyberte v horním panelu aplikace **test** .

    ![Výuka stránky testovací aplikace &](./media/luis-how-to-interactive-test/test.png)

1. Do textového pole zadejte utterance a vyberte Enter. V **testu**můžete zadat tolik projevy testů, ale pouze jeden utterance v jednom okamžiku.

1. Do seznamu projevy pod textovým polem se přidají utterance, jeho nejvyšší záměr a skóre.

    ![Interaktivní testování identifikuje nesprávný záměr.](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Zkontrolovat skóre

Podrobnosti o výsledku testu si můžete prohlédnout na panelu **Kontrola** . 
 
1. V otevřeném panelu **testovacího** snímku vyberte **zkontrolovat** pro utterance, který chcete porovnat. 

    ![Kliknutím na tlačítko zkontrolovat zobrazíte další podrobnosti o výsledcích testu.](./media/luis-how-to-interactive-test/inspect.png)

1. Zobrazí se panel **Kontrola** . Panel zahrnuje nejvyšší záměr hodnocení i všechny identifikované entity. Na panelu se zobrazí výsledek vybrané utterance.

    ![Panel zahrnuje nejvyšší záměr hodnocení i všechny identifikované entity. Na panelu se zobrazí výsledek vybrané utterance.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Opravit nejvyšší záměr hodnocení

1. Pokud je záměr nejvyšší bodování nesprávný, vyberte tlačítko **Upravit** .

1.  V rozevíracím seznamu vyberte správný záměr pro utterance.

    ![Vybrat správný záměr](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Zobrazit výsledky mínění

Pokud je na stránce **[publikovat](luis-how-to-publish-app.md#enable-sentiment-analysis)** nakonfigurována **Analýza mínění** , výsledky testu zahrnují mínění nalezený v utterance. 

![Obrázek testovacího podokna s analýzou mínění](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Opravit záměr vyhovujícího vzoru

Pokud používáte [vzory](luis-concept-patterns.md) , ale utterance odpovídá vzoru, ale nesprávný záměr byl předpovězen, vyberte odkaz **Upravit** podle vzoru a pak vyberte správný záměr.

## <a name="compare-with-published-version"></a>Porovnat s publikovanou verzí

Aktivní verzi aplikace můžete otestovat pomocí publikované verze [koncového bodu](luis-glossary.md#endpoint) . Na panelu **Kontrola** vyberte **Porovnat s publikováním**. Jakékoli testování u publikovaného modelu se odečte od vyvážení kvóty předplatného Azure. 

![Porovnat s publikovaným](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Zobrazit JSON koncového bodu na testovacím panelu
Můžete zobrazit vrácený koncový bod JSON pro porovnání tím, že vyberete **zobrazení zobrazit JSON**.

![Publikovaná odpověď JSON](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Další nastavení na testovacím panelu

### <a name="luis-endpoint"></a>Koncový bod LUIS

Pokud máte několik koncových bodů LUIS, použijte odkaz **Další nastavení** v podokně Publikováno v testu ke změně koncového bodu používaného pro testování. Pokud si nejste jisti, který koncový bod chcete použít, vyberte výchozí **Starter_Key**. 

![Testovací panel s zvýrazněným dalším nastavením odkazu](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)


### <a name="view-bing-spell-check-corrections-in-test-panel"></a>Zobrazit Kontrola pravopisu Bingu opravy na testovacím panelu

Požadavky pro zobrazení oprav pravopisu: 

* Publikovaná aplikace
* [Klíč služby](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)kontrola pravopisu Bingu Klíč služby není uložený a je potřeba ho resetovat pro každou relaci prohlížeče. 

Použijte následující postup, chcete-li zahrnout službu [Kontrola pravopisu Bingu v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) do výsledků podokna testu. 

1. V podokně **test** zadejte utterance. Po předvídání **[utterance vyberte pod](#inspect-score)** utterance, které jste zadali. 

1. Po otevření panelu **Kontrola** vyberte **[Porovnat s publikováním](#compare-with-published-version)** . 

1. Po otevření **publikovaného** panelu vyberte **[Další nastavení](#additional-settings-in-test-panel)** .

1. V místním dialogovém okně zaškrtněte **povolit kontrola pravopisu Bingu** a zadejte klíč a potom vyberte **Hotovo**. 
    ![zadat klíč služby Kontrola pravopisu Bingu](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key-text.png)

1. Zadejte dotaz, který má nesprávný pravopis, například `book flite to seattle`, a vyberte Enter. Nesprávný pravopis slova `flite` je nahrazen v dotazu odeslaném do LUIS a výsledný JSON zobrazuje jak původní dotaz, tak `query`a opravený pravopis v dotazu, jako `alteredQuery`.

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
Podívejte se na [Koncepty](luis-concept-batch-test.md) testování dávek a Naučte [se](luis-how-to-batch-test.md) testovat dávku projevy.

## <a name="next-steps"></a>Další kroky

Pokud testování indikuje, že vaše aplikace LUIS nerozpozná správné záměry a entity, můžete pracovat na vylepšení přesnosti vaší aplikace LUIS tak, že se přihlásíte další projevy nebo přidáte funkce. 

* [Popisek navržený projevy pomocí LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Využijte funkce ke zlepšení výkonu aplikace v LUIS.](luis-how-to-add-features.md) 
