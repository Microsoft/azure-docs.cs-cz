---
title: Testovací aplikace na portálu LUIS
description: Pomocí funkce Language Understanding (LUIS) můžete nepřetržitě pracovat na aplikaci a vylepšovat ji a zlepšovat její jazykové porozumění.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: ba7e23a72cd308dd4393bf9a581571e2bc9f5fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219816"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Testování aplikace LUIS na portálu LUIS

[Testování](luis-concept-test.md) aplikace je iterativní proces. Po trénování aplikace LUIS, otestujte ji s ukázkové projevy, abyste zjistili, zda záměry a entity jsou rozpoznány správně. Pokud nejsou, proveďte aktualizace aplikace LUIS, trénování a testování znovu.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Vlak před testováním

Chcete-li otestovat nejnovější verzi aktivní aplikace, vyberte **vlak** z horní nabídky před testováním.

## <a name="test-an-utterance"></a>Testování utterance

Test utterance by neměla být přesně stejná jako všechny příklad projevy v aplikaci. Promluva testu by měla zahrnovat volbu slova, délku fráze a použití entity, které očekáváte pro uživatele.

1. Přístup k aplikaci vyberete její název na stránce **Moje aplikace.**

1. Chcete-li získat přístup k panelu **Testovat** vysunutí, vyberte **testovat** v horním panelu aplikace.

    > [!div class="mx-imgBorder"]
    > ![Stránka aplikace test & vlaku](./media/luis-how-to-interactive-test/test.png)

1. Do textového pole zadejte promluvu a vyberte Enter. Můžete zadat libovolný počet testovacích projevů v **testu**, ale současně pouze jeden projev.

1. Utterance, jeho hlavní záměr a skóre jsou přidány do seznamu projevy pod textové pole.

    ![Interaktivní testování identifikuje nesprávný záměr](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Zkontrolovat skóre

Podrobnosti o výsledku testu zkontrolujte v panelu **Kontrola.**

1. Když je otevřený panel **Testovat** vysunutí, vyberte **Zkontrolovat** promluvu, kterou chcete porovnat.

    ![Výběrem tlačítka Zkontrolovat zobrazíte další podrobnosti o výsledcích testu.](./media/luis-how-to-interactive-test/inspect.png)

1. Zobrazí se **kontrolní** panel. Panel obsahuje hlavní záměr hodnocení, stejně jako všechny identifikované entity. Panel zobrazuje výsledek vybrané utterance.

    ![Panel obsahuje hlavní záměr hodnocení, stejně jako všechny identifikované entity. Panel zobrazuje výsledek vybrané utterance.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Opravit hlavní záměr hodnocení

1. Pokud je záměr nejvyššího bodování nesprávný, vyberte tlačítko **Upravit.**

1.  V rozevíracím seznamu vyberte správný záměr pro utterance.

    ![Vybrat správný záměr](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Zobrazit výsledky mínění

Pokud **je analýza mínění** nakonfigurována na stránce **[Publikovat,](luis-how-to-publish-app.md#enable-sentiment-analysis)** výsledky testů zahrnují mínění nalezené v utterance.

![Obrázek podokna Test s analýzou mínění](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Správný záměr odpovídajícího vzoru

Pokud používáte [Patterns](luis-concept-patterns.md) a utterance uzavřeno vzor, ale byl předpovězen nesprávný záměr, vyberte **upravit** odkaz podle vzoru, pak vyberte správný záměr.

## <a name="compare-with-published-version"></a>Porovnat s publikovanou verzí

Aktivní verzi aplikace můžete otestovat pomocí publikované verze [koncového bodu.](luis-glossary.md#endpoint) V panelu **Zkontrolovat** vyberte **Porovnat s publikovanými**. Jakékoli testování proti publikovanémodelu se odečte z vašeho zůstatku kvóty předplatného Azure.

![Porovnat s publikovanými](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Zobrazení koncového bodu JSON v testovacím panelu
Koncový bod, který json vrátil pro porovnání, můžete zobrazit výběrem **zobrazení Zobrazit JSON**.

![Publikovaná odpověď JSON](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>Další nastavení v testovacím panelu

### <a name="luis-endpoint"></a>Koncový bod služby LUIS

Pokud máte několik koncových bodů LUIS, použijte odkaz **Další nastavení** v podokně Publikovaný test změnit koncový bod použitý pro testování. Pokud si nejste jisti, který koncový bod použít, vyberte výchozí **Starter_Key**.

> [!div class="mx-imgBorder"]
> ![Testovací panel se zvýrazněnou vazbou Další nastavení](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>Dávkové testování
Viz [koncepty dávkového](luis-concept-batch-test.md) testování a zjistěte, [jak](luis-how-to-batch-test.md) otestovat dávku promluv.

## <a name="next-steps"></a>Další kroky

Pokud testování znamená, že vaše aplikace LUIS nerozpozná správné záměry a entity, můžete pracovat na zlepšení přesnosti aplikace LUIS označením další projevy nebo přidáním funkcí.

* [Popisek navrhované projevy s LUIS](luis-how-to-review-endpoint-utterances.md)
* [Vylepšování výkonu aplikace LUIS pomocí funkcí](luis-how-to-add-features.md)
