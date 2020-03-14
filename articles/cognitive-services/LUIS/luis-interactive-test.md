---
title: Test aplikace portálu služby LUIS
description: Language Understanding (LUIS) umožňuje neustále pracovat na aplikaci zpřesnit jej a zvýšit jeho umožňující porozumět jazyku.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: ba7e23a72cd308dd4393bf9a581571e2bc9f5fa0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219816"
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

## <a name="additional-settings-in-test-panel"></a>Další nastavení panelu testu

### <a name="luis-endpoint"></a>Koncový bod služby LUIS

Pokud máte několik koncových bodů LUIS, použijte odkaz **Další nastavení** v podokně Publikováno v testu ke změně koncového bodu používaného pro testování. Pokud si nejste jisti, který koncový bod chcete použít, vyberte výchozí **Starter_Key**.

> [!div class="mx-imgBorder"]
> ![testovací panel se zvýrazněným odkazem na další nastavení](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>Dávkové testování
Podívejte se na [Koncepty](luis-concept-batch-test.md) testování dávek a Naučte [se](luis-how-to-batch-test.md) testovat dávku projevy.

## <a name="next-steps"></a>Další kroky

Pokud testování označuje, že aplikace LUIS nedokáže rozpoznat správný záměry a entity, můžete pracovat zvyšte přesnost aplikace LUIS označování další projevy nebo přidáním funkcí.

* [Popisek navržený projevy pomocí LUIS](luis-how-to-review-endpoint-utterances.md)
* [Využijte funkce ke zlepšení výkonu aplikace v LUIS.](luis-how-to-add-features.md)
