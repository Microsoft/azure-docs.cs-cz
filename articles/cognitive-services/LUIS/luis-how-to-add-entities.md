---
title: Přidat entity – LUIS
description: Vytvořte entity pro extrakci klíčových dat z uživatelských projevy v aplikacích Language Understanding (LUIS). Extrahovaná data entity používá klientská aplikace pro moje požadavky zákazníků.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: diberry
ms.openlocfilehash: 8be5dac2d1d5e8a5a3ceafff6b51b2d89e03993f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593273"
---
# <a name="add-entities-to-extract-data"></a>Přidat entity pro extrakci dat

Vytvořte entity pro extrakci klíčových dat z uživatelských projevy v aplikacích Language Understanding (LUIS). Extrahovaná data entity používá vaše klientská aplikace pro moje požadavky zákazníků.

Entita představuje slovo nebo frázi uvnitř utterance, který chcete extrahovat. Entity popisují informace týkající se záměru a někdy jsou nezbytné, aby vaše aplikace mohla provádět její úkoly. Můžete vytvořit entity, když přidáte příklad utterance k záměru nebo od (před nebo po) přidání příkladu utterance k záměru.

## <a name="plan-entities-then-create-and-label"></a>Plánování entit a pak vytvoření a označení

Entity, které se naučily počítačem, se dají vytvořit z příkladu projevy nebo vytvořit ze stránky **entity** .

Obecně platí, že je potřeba věnovat čas plánování entit předtím, než na portálu vytvoříte entitu získanou počítačem. Pak vytvořte entitu získanou počítačem z příkladu utterance s toliki podrobnostmi v podentitách a funkcích, které znáte v čase. Tento [kurz](tutorial-machine-learned-entity.md) ukazuje, jak použít tuto metodu.

V rámci plánování entit můžete znát, že budete potřebovat entity vyhovující textu (například předem připravené entity, entity regulárních výrazů nebo seznam entit). Můžete je vytvořit ze stránky **entity** předtím, než jsou označeny jako projevy.

Při označování můžete jednotlivé entity označit popiskem a pak sestavit až na nadřazenou entitu počítačového učení. Nebo můžete začít s entitou získanou nadřazeným počítačem a rozložit ji na podřízené entity.

> [!TIP]
>Označte všechna slova, která mohou označovat entitu, a to i v případě, že se slova při extrakci v klientské aplikaci nepoužívají.

## <a name="creating-an-entity-before-or-with-labeling"></a>Vytvoření entity před nebo s popisky

Následující tabulka vám pomůže pochopit, které entity chcete do aplikace vytvořit nebo přidat jednotlivé entity.

|Typ entity|Kde vytvořit entitu na portálu LUIS|
|--|--|
|Strojově naučená entita|Podrobnosti o entitách nebo záměrech|
|Entita seznamu|Podrobnosti o entitách nebo záměrech|
|Entiay regulárního výrazu|Entity|
|Entita Pattern.any|Entity|
|Předem vytvořená entita|Entity|
|Předem sestavená doménová entita|Entity|

Můžete vytvořit všechny entity ze stránky **entity** , nebo můžete vytvořit několik entit jako součást popisu entity v příkladu utterance na stránce s **podrobnostmi o záměru** . Entitu můžete _Označit_ jenom na příkladu utterance ze stránky s **podrobnostmi záměru** .

## <a name="create-a-machine-learned-entity"></a>Vytvořit entitu získanou počítačem

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>Vytvoření entity, která odpovídá textu

Použití entit pro porovnání textu nabízí několik způsobů, jak extrahovat data:

|Entity, které se shodují s textem|Účel|
|--|--|
|[Entita seznamu](reference-entity-list.md)|seznam kanonických názvů spolu s synonymy jako s použitím alternativních forem|
|Entiay regulárního výrazu|porovnat text s použitím entity regulárního výrazu|
|[Předem vytvořená entita](luis-reference-prebuilt-entities.md)|odpovídají běžným datovým typům, jako je číslo, e-mail, datum|
|Předem sestavená doménová entita|shoda s použitím vybraných domén předmětu|
|[Vzor. any](reference-entity-pattern-any.md)| aby odpovídaly entitám, které mohou být snadno zaměňovány s okolním textem|

Předem připravené entity fungují bez poskytování vlastních školicích dat. Ostatní entity potřebují zadat data školení zákazníka (například položky seznamu entit) nebo výraz (například regulární výraz nebo vzor. any).

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>Jak vytvořit novou vlastní entitu

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.
1. Vyberte stránku **entity** .
1. Vyberte **+ vytvořit**a pak vyberte typ entity.
1. Pokračujte v konfiguraci entity a potom po dokončení vyberte **vytvořit** .

### <a name="add-list-entities-for-exact-matches"></a>Přidat seznam entit pro přesné shody

Seznam entit představuje pevně uzavřenou sadu příbuzných slov. I když jako autor může seznam změnit, LUIS nebude zvětšovat nebo zmenšovat seznam. Můžete také importovat do existující entity seznamu pomocí [formátu list. JSON](reference-entity-list.md#example-json-to-import-into-list-entity).

Následující seznam znázorňuje kanonický název a synonyma.

|Název položky seznamu barev|Barva – synonyma|
|--|--|
|Červený|Crimson, krev, Apple, hasicí stroj|
|Blue|nebe, Azure, kobalt|
|Green|Kelly, vápno|

Pomocí postupu vytvořte entitu seznamu. Po vytvoření entity seznamu nemusíte v záměru popsat vzorový projevy. Položky seznamu a synonyma se shodují pomocí přesného textu.
1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.
1. V části **sestavení** vyberte **entity** na levém panelu a potom vyberte **+ vytvořit**.

1. V dialogovém okně **vytvořit typ entity** zadejte název entity, například `Colors` a vyberte **seznam**.
1. V dialogovém okně **vytvořit entitu** vyberte v **podseznamu přidat nový**, zadejte název položky seznamu, například `Green` a pak přidejte synonyma.

    > [!div class="mx-imgBorder"]
    > ![Na stránce podrobností entity vytvořte seznam barev jako entitu seznamu.](media/how-to-add-entities/create-list-entity-of-colors.png)

1. Až skončíte s přidáváním položek seznamu a synonym, vyberte **vytvořit**.

    Až budete hotovi se skupinou změn v aplikaci, nezapomeňte aplikaci **naučit** . Neprovádějte výuku aplikace po jedné změně.

    > [!NOTE]
    > Tento postup ukazuje vytvoření a označení entity seznamu z příkladu utterance na stránce s **podrobnostmi o záměru** . Stejnou entitu můžete také vytvořit ze stránky **entity** .

## <a name="add-a-role-for-an-entity"></a>Přidání role pro entitu

Role je pojmenovaný podtyp entity na základě kontextu.

### <a name="add-a-role-to-distinguish-different-contexts"></a>Přidání role pro odlišení různých kontextů

V následujících utterance jsou dvě umístění, přičemž každý z nich je sémanticky určenými slovy, například `to` a `from` :

`Pick up the package from Seattle and deliver to New York City.`

V tomto postupu přidáte `origin` `destination` k předem sestavené entitě geographyV2 a role.
1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.
1. V části **Build (sestavení** ) vyberte **entity** na levém panelu.

1. Vyberte **+ Přidat předem vytvořenou entitu**. Vyberte **geographyV2** a potom vyberte **Hotovo**. Tím se do aplikace přidá předem sestavená entita.

    Pokud zjistíte, že váš vzor zahrnující entitu Pattern.any neextrahuje entity správně, můžete tento problém opravit pomocí [explicitního seznamu](reference-pattern-syntax.md#explicit-lists).

1. Vyberte nově přidanou předem vytvořenou entitu geographyV2 ze seznamu entit na stránce **entity** .
1. Chcete-li přidat novou roli, vyberte možnost **+** Další **bez přidaných rolí**.
1. Do textového pole **typ role...** zadejte název role `Origin` a potom zadejte. Přidejte název druhé role a `Destination` potom zadejte.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s přidáním role původu do entity umístění](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    Role se přidá k předem připravené entitě, ale nepřidá se do žádné projevy pomocí této entity.

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Popisek textu s rolí v příkladu utterance
1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.
1. Přejít na stránku s podrobnostmi záměru, která obsahuje příklad projevy, který používá roli.
1. Pokud chcete s rolí označit popisek, vyberte v příkladu utterance popisek entity (plná čára pod textem) a pak v rozevíracím seznamu vyberte **Zobrazit v paletě entit** .

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s výběrem zobrazení v paletě entit](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)

    Na pravé straně se otevře paleta entit.

1. Vyberte entitu, potom klikněte na dolní část palety a vyberte roli.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s výběrem zobrazení v paletě entit](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>Přidejte vzor. libovolnou entitu

[Vzor. všechny](luis-concept-entity-types.md) entity jsou platné jenom v [vzorcích](luis-how-to-model-intent-pattern.md), ne záměrech, jako je například projevy. Tento typ entity pomáhá LUIS najít konec entit s různou délkou a volbou Wordu. Vzhledem k tomu, že tato entita se používá ve vzoru, LUIS ví, kde je konec entity v šabloně utterance.

### <a name="steps-to-create-a-patternany-entity"></a>Postup vytvoření vzoru. kterákoli entita
1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.
1. V části **sestavení** vyberte **entity** na levém panelu a potom vyberte **+ vytvořit**.

1. V dialogovém okně **Zvolte typ entity** zadejte název entity do pole **název** a jako **typ** vyberte **vzor. any** a pak vyberte **vytvořit**.

    Jakmile [vytvoříte vzor utterance](luis-how-to-model-intent-pattern.md) pomocí této entity, bude se tato entita extrahovat pomocí kombinovaného algoritmu pro porovnávání počítačů a textu.

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>Vytvořte šablonu vzoru utterance pro použití vzoru. libovolnou entitu

Chcete-li použít vzor. jakékoli entity, přidejte vzor na stránce **vzory** v části **zlepšení výkonu aplikace** se správnou syntaxí složených závorek, například `Where is **{HumanResourcesFormTitle}** on the server?` .

Pokud zjistíte, že váš vzor zahrnující entitu Pattern.any neextrahuje entity správně, můžete tento problém opravit pomocí [explicitního seznamu](reference-pattern-syntax.md#explicit-lists).

## <a name="do-not-change-entity-type"></a>Neměnit typ entity

LUIS neumožňuje změnit typ entity, protože nevíte, co přidat nebo odebrat k vytvoření této entity. Aby bylo možné typ změnit, je vhodnější vytvořit novou entitu správného typu s trochu odlišným názvem. Po vytvoření entity odstraňte v každém utterance starý název entity a přidejte nový název entity. Po přeznačení všech projevy se odstraní stará entita.

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Použití předem připravených modelů](howto-add-prebuilt-models.md)

Další informace:
* Postup [výuky](luis-how-to-train.md)
* Jak [testovat](luis-interactive-test.md)
* Jak [publikovat](luis-how-to-publish-app.md)
* Charakteristiky
    * [Koncepty](luis-concept-patterns.md)
    * [Syntaxe](reference-pattern-syntax.md)
* [Úložiště GitHub předem připravené entity](https://github.com/Microsoft/Recognizers-Text)
* [Koncepce extrakce dat](luis-concept-data-extraction.md)



