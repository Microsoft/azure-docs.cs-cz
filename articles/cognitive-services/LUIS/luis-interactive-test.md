---
title: Testovací aplikace na portálu LUIS
description: Pomocí Language Understanding (LUIS) můžete průběžně pracovat na vaší aplikaci a zdokonalit tak její porozumění jazyku.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: cb4559e6d3582a337cbc32ef986bec5db5940ef9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591858"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Testování aplikace LUIS na portálu LUIS

[Testování](luis-concept-test.md) aplikace je iterativní proces. Po školení aplikace LUIS otestujte ji pomocí Sample projevy a zjistěte, jestli jsou záměry a entity správně rozpoznané. Pokud nejsou, proveďte aktualizace aplikace LUIS, školení a testování znovu.

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Výuka před testováním

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.
1. Chcete-li provést test na nejnovější verzi aktivní aplikace, vyberte před testováním z horní nabídky možnost **výuka** .

## <a name="test-an-utterance"></a>Testování utterance

Utterance testu by neměl být přesně stejný jako jakýkoli příklad projevy v aplikaci. Testovací utterance by měl obsahovat volbu Word, délku fráze a využití entity, které očekáváte pro uživatele.

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.

1. Pro přístup k panelu **testovacího** snímku vyberte v horním panelu aplikace **test** .

    > [!div class="mx-imgBorder"]
    > ![Výuka stránky testovací aplikace &](./media/luis-how-to-interactive-test/test.png)

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

## <a name="additional-settings-in-test-panel"></a>Další nastavení na testovacím panelu

### <a name="luis-endpoint"></a>Koncový bod LUIS

Pokud máte několik koncových bodů LUIS, použijte odkaz **Další nastavení** v podokně Publikováno v testu ke změně koncového bodu používaného pro testování. Pokud si nejste jisti, který koncový bod chcete použít, vyberte výchozí **Starter_Key**.

> [!div class="mx-imgBorder"]
> ![Testovací panel s zvýrazněným dalším nastavením odkazu](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>Dávkové testování
Podívejte se na [Koncepty](luis-concept-batch-test.md) testování dávek a Naučte [se](luis-how-to-batch-test.md) testovat dávku projevy.

## <a name="next-steps"></a>Další kroky

Pokud testování indikuje, že vaše aplikace LUIS nerozpozná správné záměry a entity, můžete pracovat na vylepšení přesnosti vaší aplikace LUIS tak, že se přihlásíte další projevy nebo přidáte funkce.

* [Popisek navržený projevy pomocí LUIS](luis-how-to-review-endpoint-utterances.md)
* [Využijte funkce ke zlepšení výkonu aplikace v LUIS.](luis-how-to-add-features.md)
