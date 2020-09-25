---
title: Testovací aplikace na portálu LUIS
description: Pomocí Language Understanding (LUIS) můžete průběžně pracovat na vaší aplikaci a zdokonalit tak její porozumění jazyku.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: b6fcc294c2b8c131d9a4a058c653ae1f64652ccf
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324854"
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

    > [!div class="mx-imgBorder"]
    > ![Interaktivní testování identifikuje nesprávný záměr.](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-the-prediction"></a>Zkontrolovat předpověď

Podrobnosti o výsledku testu si můžete prohlédnout na panelu **Kontrola** .

1. V otevřeném panelu **testovacího** snímku vyberte **zkontrolovat** pro utterance, který chcete porovnat.

    > [!div class="mx-imgBorder"]
    > ![Kliknutím na tlačítko zkontrolovat zobrazíte další podrobnosti o výsledcích testu.](./media/luis-how-to-interactive-test/inspect.png)

1. Zobrazí se panel **Kontrola** . Panel zahrnuje nejvyšší záměr hodnocení i všechny identifikované entity. Na panelu se zobrazuje předpověď vybrané utterance.

    > [!div class="mx-imgBorder"]
    > ![Částečný snímek obrazovky s panelem pro kontrolu testu](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="add-to-example-utterances"></a>Přidat do ukázkového projevy

Z panelu Kontrola můžete přidat utterance testu do záměru tak, že vyberete **Přidat do ukázkového projevy**.

## <a name="disable-required-features"></a>Zakázat požadované funkce

Tento přepínač vám pomůže určit, jestli má školená aplikace správně předpovídat vaše entity na základě požadovaných funkcí. Výchozím nastavením je použití funkce, jak je požadováno během předpovědi. Výběrem tohoto přepínače zobrazíte, co by předpověď znamenalo v případě, že se funkce subentity nevyžadovala.

### <a name="when-to-disable-required-features"></a>Kdy zakázat požadované funkce

Vyškolená aplikace může vypovídat nepředvídatelné entitu zjištěné počítačem na základě jedné z následujících možností:
* Nesprávné označení příkladu projevy.
* Požadovaná funkce se neshoduje s textem.

Příkladem je entita získaná počítačem s podentitou názvu osoby.

:::image type="content" source="media/luis-how-to-interactive-test/disable-required-feature.png" alt-text="Snímek obrazovky počítačového portálu LUIS se naučilo schéma entit s požadovanou funkcí":::

Příklad utterance pro tuto entitu, která se naučila pro tento počítač, je: `Assign Bob Jones to work on the new security feature` .

Extrakce by měla být `security feature` jako popis lístku a `Bob Jones` jako inženýr, dvě subentity `Assign ticket` entity.

Aby bylo možné podentitě úspěšně odhadnout, přidejte do subentity předem vytvořenou entitu [Person](luis-reference-prebuilt-person.md) AA a `engineer` . Pokud funkci požadujete, znamená to, že podentita bude extrahována pouze v případě, že je pro text předpovězena předdefinovaná entita Person. To znamená, že libovolný název v textu, který není předpovídat s názvem Person subentity, nebude vrácen jako subentita s popiskem, `engineer` .

Když použijete interaktivní podokno test a v případě, že se zobrazí podentita s požadovanou funkcí, není předpověď, přepněte toto nastavení, abyste viděli, jestli by se subentita mohla předpovědět bez požadované funkce. Podentita může být schopná správně předpovězena bez požadované funkce z důvodu správného označení příkladu projevy.

## <a name="view-sentiment-results"></a>Zobrazit výsledky mínění

Pokud je na stránce **[publikovat](luis-how-to-publish-app.md#enable-sentiment-analysis)** nakonfigurována **Analýza mínění** , výsledky testu zahrnují mínění nalezený v utterance.

## <a name="correct-matched-patterns-intent"></a>Opravit záměr vyhovujícího vzoru

Pokud používáte [vzory](luis-concept-patterns.md) , ale utterance odpovídá vzoru, ale nesprávný záměr byl předpovězen, vyberte odkaz **Upravit** podle vzoru a pak vyberte správný záměr.

## <a name="compare-with-published-version"></a>Porovnat s publikovanou verzí

Aktivní verzi aplikace můžete otestovat pomocí publikované verze [koncového bodu](luis-glossary.md#endpoint) . Na panelu **Kontrola** vyberte **Porovnat s publikováním**. Jakékoli testování u publikovaného modelu se odečte od vyvážení kvóty předplatného Azure.

> [!div class="mx-imgBorder"]
> ![Porovnat s publikovaným](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Zobrazit JSON koncového bodu na testovacím panelu
Můžete zobrazit vrácený koncový bod JSON pro porovnání tím, že vyberete **zobrazení zobrazit JSON**.

> [!div class="mx-imgBorder"]
> ![Publikovaná odpověď JSON](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

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
