---
title: Přidat entity – LUIS
description: Vytvořte entity pro extrakci klíčových dat z uživatelských projevy v aplikacích Language Understanding (LUIS). Extrahovaná data entity používá klientská aplikace pro moje požadavky zákazníků.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: c5c6836c2d68036bf2b9c5abe191943537349b8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "91540961"
---
# <a name="add-entities-to-extract-data"></a>Přidat entity pro extrakci dat

Vytvořte entity pro extrakci klíčových dat z uživatelských projevy v aplikacích Language Understanding (LUIS). Extrahovaná data entity používá vaše klientská aplikace pro moje požadavky zákazníků.

Entita představuje slovo nebo frázi uvnitř utterance, který chcete extrahovat. Entity popisují informace týkající se záměru a někdy jsou nezbytné, aby vaše aplikace mohla provádět její úkoly. Můžete vytvořit entity, když přidáte příklad utterance k záměru nebo od (před nebo po) přidání příkladu utterance k záměru.

## <a name="plan-entities-then-create-and-label"></a>Plánování entit a pak vytvoření a označení

entity strojového učení lze vytvořit z příkladu projevy nebo vytvořit ze stránky **entity** .

Obecně platí, že je potřeba věnovat čas plánování entit před vytvořením entity strojového učení na portálu. Pak vytvořte entitu Machine-Learning z příkladu utterance s větším množstvím podrobností v podentitách a funkcích, které v tuto chvíli znáte. Tento [kurz](tutorial-machine-learned-entity.md) ukazuje, jak použít tuto metodu.

V rámci plánování entit můžete znát, že budete potřebovat entity vyhovující textu (například předem připravené entity, entity regulárních výrazů nebo seznam entit). Můžete je vytvořit ze stránky **entity** předtím, než jsou označeny jako projevy.

Při označování můžete jednotlivé entity označit popiskem a pak si vytvořit nadřazenou entitu strojového učení. Nebo můžete začít s nadřazenou entitou strojového učení a rozložit ji na podřízené entity.

> [!TIP]
>Označte všechna slova, která mohou označovat entitu, a to i v případě, že se slova při extrakci v klientské aplikaci nepoužívají.

## <a name="when-to-create-an-entity"></a>Kdy vytvořit entitu

Po naplánování entit byste měli vytvořit své entity a subentity pro strojové učení. To může vyžadovat přidání předem připravených entit nebo entit s vyhovujícími textem pro poskytování funkcí pro vaše entity strojového učení. Všechny by měly být provedeny před popiskem.

Až začnete s označením příkladu projevy, můžete vytvářet entity, které se naučily pro počítače, nebo rozšiřuje entity seznamu.

Následující tabulka vám pomůže pochopit, kde můžete do aplikace vytvořit nebo přidat jednotlivé typy entit.

|Typ entity|Kde vytvořit entitu na portálu LUIS|
|--|--|
|entita strojového učení|Podrobnosti o entitách nebo záměrech|
|Entita seznamu|Podrobnosti o entitách nebo záměrech|
|Entiay regulárního výrazu|Entity|
|Entita Pattern.any|Entity|
|Předem vytvořená entita|Entity|
|Předem sestavená doménová entita|Entity|

Můžete vytvořit všechny entity ze stránky **entity** , nebo můžete vytvořit několik entit jako součást popisu entity v příkladu utterance na stránce s **podrobnostmi o záměru** . Entitu můžete _Označit_ jenom na příkladu utterance ze stránky s **podrobnostmi záměru** .



## <a name="how-to-create-a-new-custom-entity"></a>Jak vytvořit novou vlastní entitu

Tento proces funguje pro entity, které jsou v počítači zjištěny, seznam entit a regulární výraz entit.

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.
1. Vyberte stránku **entity** .
1. Vyberte **+ vytvořit** a pak vyberte typ entity.
1. Pokračujte v konfiguraci entity a potom po dokončení vyberte **vytvořit** .

## <a name="create-a-machine-learned-entity"></a>Vytvoření entity, která se naučila pro počítač

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.
1. V části **sestavení** vyberte **entity** na levém panelu a potom vyberte **+ vytvořit**.
1. V dialogovém okně **vytvořit typ entity** zadejte název entity a vyberte možnost **zjištěný počítač**, vyberte. Chcete-li přidat subentity, vyberte možnost **přidat strukturu**. Vyberte **Vytvořit**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s vytvořením entity získané počítačem](media/add-entities/machine-learned-entity-with-structure.png)

1. V části **Přidat** subentity přidejte podentitu výběrem možnosti **+** na řádku nadřazené entity.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s přidáním podentit](media/add-entities/machine-learned-entity-with-subentities.png)

1. Vyberte **vytvořit** a dokončete proces vytváření.

## <a name="add-a-feature-to-a-machine-learned-entity"></a>Přidání funkce k entitě rozpoznaného počítače

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.
1. V části **sestavení** vyberte **entity** na levém panelu a pak vyberte entitu počítač, kterou jste získali.
1. Přidejte funkci tak, že vyberete **+ Přidat funkci** na řádku entity nebo subentity.
1. Vyberte ze stávajících entit a seznamů frází.
1. Pokud má být entita extrahována pouze v případě, že je funkce nalezena, vyberte `*` pro tuto funkci hvězdičku.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s přidáním funkce do entity](media/add-entities/machine-learned-entity-schema-with-features.png)

## <a name="create-a-regular-expression-entity"></a>Vytvořit entitu regulárního výrazu

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.
1. V části **sestavení** vyberte **entity** na levém panelu a potom vyberte **+ vytvořit**.

1. V dialogovém okně **vytvořit typ entity** zadejte název entity a vyberte **regulární** výraz, do pole **Regex** zadejte regulární výraz a vyberte **vytvořit**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s vytvořením entity regulárního výrazu](media/add-entities/add-regular-expression-entity.png)


<a name="add-list-entities"></a>

## <a name="create-a-list-entity"></a>Vytvořit entitu seznamu

Seznam entit představuje pevně uzavřenou sadu příbuzných slov. I když jako autor může seznam změnit, LUIS nebude zvětšovat nebo zmenšovat seznam. Můžete také importovat do existující entity seznamu pomocí [formátu list. JSON](reference-entity-list.md#example-json-to-import-into-list-entity).

Následující seznam znázorňuje kanonický název a synonyma.

|Název položky seznamu barev|Barva – synonyma|
|--|--|
|Red|Crimson, krev, Apple, hasicí stroj|
|Blue|Nebeská, kobalt|
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

> [!TIP]
> Role je možné nahradit pomocí popisků s podentitami entit strojového učení.

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.
1. Přejít na stránku s podrobnostmi záměru, která obsahuje příklad projevy, který používá roli.
1. Pokud chcete s rolí označit popisek, vyberte v příkladu utterance popisek entity (plná čára pod textem) a pak v rozevíracím seznamu vyberte **Zobrazit v podokně entity** .

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky znázorňující vybranou položku nabídky pro zobrazení v podokně entit.](media/add-entities/view-in-entity-pane.png)

    Na pravé straně se otevře paleta entit.

1. Vyberte entitu, potom klikněte na dolní část palety a vyberte roli.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky ukazuje, kde vybrat roli.](media/add-entities/select-role-in-entity-palette.png)

<a name="add-pattern-any-entities"></a>
<a name="add-a-patternany-entity"></a>
<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-patternany-entity"></a>Vytvoření vzoru. kterákoli entita

**Vzor. Každá** entita je k dispozici pouze se [vzory](luis-how-to-model-intent-pattern.md).


## <a name="do-not-change-entity-type"></a>Neměnit typ entity

LUIS neumožňuje změnit typ entity, protože nevíte, co přidat nebo odebrat k vytvoření této entity. Aby bylo možné typ změnit, je vhodnější vytvořit novou entitu správného typu s trochu odlišným názvem. Po vytvoření entity odstraňte v každém utterance starý název entity a přidejte nový název entity. Po přeznačení všech projevy se odstraní stará entita.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Použití předem připravených modelů](howto-add-prebuilt-models.md)

Přečtěte si další informace:
* Postup [výuky](luis-how-to-train.md)
* Jak [testovat](luis-interactive-test.md)
* Jak [publikovat](luis-how-to-publish-app.md)
* Charakteristiky
    * [Koncepty](luis-concept-patterns.md)
    * [Syntax](reference-pattern-syntax.md)
* [Úložiště GitHub předem připravené entity](https://github.com/Microsoft/Recognizers-Text)
* [Koncepce extrakce dat](luis-concept-data-extraction.md)



