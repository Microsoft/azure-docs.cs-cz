---
title: Přidat entity – LUIS
titleSuffix: Azure Cognitive Services
description: Vytvořte entity pro extrakci klíčových dat z uživatelských projevy v aplikacích Language Understanding (LUIS). Extrahovaná data entity používá klientská aplikace pro moje požadavky zákazníků.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1f2b293acdc77e25e6b932c47d466cc28a04a2b6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393848"
---
# <a name="add-entities-to-extract-data"></a>Přidat entity pro extrakci dat 

Vytvořte entity pro extrakci klíčových dat z uživatelských projevy v aplikacích Language Understanding (LUIS). Extrahovaná data entity používá vaše klientská aplikace pro moje požadavky zákazníků.

Entita představuje slovo nebo frázi v utterance, který chcete, aby byl extrahován. Entity popisují informace související s cílem a někdy jsou nezbytné pro vaši aplikaci k provedení svých úkolů. Můžete vytvořit entity, když přidáte příklad utterance k záměru nebo od (před nebo po) přidání příkladu utterance k záměru.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>Plánování entit a pak vytvoření a označení

Entity, které se naučily počítačem, se dají vytvořit z příkladu projevy nebo vytvořit ze stránky **entity** . 

Obecně platí, že je potřeba věnovat čas plánování entit předtím, než na portálu vytvoříte entitu získanou počítačem. Pak vytvořte entitu získanou počítačem z příkladu utterance s toliki podrobnostmi dílčích komponent a popisovačů a omezení v době, kdy víte v čase. Tento [kurz](tutorial-machine-learned-entity.md) ukazuje, jak použít tuto metodu. 

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
|Předem připravených entit|Entity|
|Předem sestavená doménová entita|Entity|

Můžete vytvořit všechny entity ze stránky **entity** , nebo můžete vytvořit několik entit jako součást popisu entity v příkladu utterance na stránce s **podrobnostmi o záměru** . Entitu můžete _Označit_ jenom na příkladu utterance ze stránky s **podrobnostmi záměru** . 

## <a name="create-a-machine-learned-entity"></a>Vytvořit entitu získanou počítačem

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>Vytvoření entity, která odpovídá textu

Použití entit pro porovnání textu nabízí několik způsobů, jak extrahovat data:

|Entity, které se shodují s textem|Účel|
|--|--|
|[Seznam entit](#add-list-entities-for-exact-matches)|seznam kanonických názvů spolu s synonymy jako s použitím alternativních forem|
|Entiay regulárního výrazu|porovnat text s použitím entity regulárního výrazu|
|[Předem vytvořená entita](tutorial-machine-learned-entity.md#add-prebuilt-number-to-help-extract-data)|odpovídají běžným datovým typům, jako je číslo, e-mail, datum|
|Předem sestavená doménová entita|shoda s použitím vybraných domén předmětu|
|[Vzor. any](#add-a-patternany-entity)| aby odpovídaly entitám, které mohou být snadno zaměňovány s okolním textem|  

Předem připravené entity fungují bez poskytování vlastních školicích dat. Ostatní entity potřebují zadat data školení zákazníka (například položky seznamu entit) nebo výraz (například regulární výraz nebo vzor. any).

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>Jak vytvořit novou vlastní entitu

1. Na portálu LUIS přejděte do části **Správa** a pak na stránku **entity** . 
1. Vyberte **+ vytvořit**a pak vyberte typ entity. 
1. Pokračujte v konfiguraci entity a potom po dokončení vyberte **vytvořit** . 

### <a name="add-list-entities-for-exact-matches"></a>Přidat seznam entit pro přesné shody

Seznam entit představují pevné, uzavřené sadu souvisejících slov. I když jako autor může seznam změnit, LUIS nebude zvětšovat nebo zmenšovat seznam. Můžete také importovat do existující entity seznamu pomocí formátu [list entity. JSON (Reference-entity-list. MD # example-JSON-to-list-into-import-entita). 

Následující seznam znázorňuje kanonický název a synonyma. 

|Název položky seznamu barev|Barva – synonyma|
|--|--|
|Červená|Crimson, krev, Apple, hasicí stroj|
|Modrá|nebe, Azure, kobalt|
|Šetrn|Kelly, vápno|

Pomocí postupu vytvořte entitu seznamu. Po vytvoření entity seznamu nemusíte v záměru popsat vzorový projevy. Položky seznamu a synonyma se shodují pomocí přesného textu. 

1. V části **sestavení** vyberte **entity** na levém panelu a potom vyberte **+ vytvořit**.

1. V dialogovém okně **vytvořit typ entity** zadejte název entity, například `Colors` a **seznam**pro výběr.
1. V dialogovém okně **vytvořit entitu** vyberte v **podseznamu přidat nový**, zadejte název položky seznamu, například `Green`, a přidejte synonyma.

    > [!div class="mx-imgBorder"]
    > na stránce podrobností entity ![vytvořit seznam barev jako entitu seznamu.](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. Až skončíte s přidáváním položek seznamu a synonym, vyberte **vytvořit**.

    Až budete hotovi se skupinou změn v aplikaci, nezapomeňte aplikaci **naučit** . Neprovádějte výuku aplikace po jedné změně. 

    > [!NOTE]
    > Tento postup ukazuje vytvoření a označení entity seznamu z příkladu utterance na stránce s **podrobnostmi o záměru** . Stejnou entitu můžete také vytvořit ze stránky **entity** .

## <a name="add-a-role-for-an-entity"></a>Přidání role pro entitu

Role je pojmenovaný podtyp entity na základě kontextu. 

### <a name="add-a-role-to-distinguish-different-contexts"></a>Přidání role pro odlišení různých kontextů

V následujících utterance jsou dvě umístění a každý z nich je sémanticky určenými slovy, například `to` a `from`: 

`Pick up the package from Seattle and deliver to New York City.`

V tomto postupu přidáte `origin` a `destination` role k předem sestavené entitě geographyV2.

1. V části **Build (sestavení** ) vyberte **entity** na levém panelu.

1. Vyberte **+ Přidat předem vytvořenou entitu**. Vyberte **geographyV2** a potom vyberte **Hotovo**. Tím se do aplikace přidá předem sestavená entita.
    
    Pokud zjistíte, že váš vzor zahrnující entitu Pattern.any neextrahuje entity správně, můžete tento problém opravit pomocí [explicitního seznamu](reference-pattern-syntax.md#explicit-lists). 

1. Vyberte nově přidanou předem vytvořenou entitu geographyV2 ze seznamu entit na stránce **entity** . 
1. Pokud chcete přidat novou roli, vyberte **+** vedle **žádné přidané role**.
1. Do textového pole **typ role...** zadejte název role `Origin` pak zadejte. Přidejte název druhé role `Destination` pak zadejte. 

    > [!div class="mx-imgBorder"]
    > ![snímku obrazovky přidání role původu do entity umístění](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    Role se přidá k předem připravené entitě, ale nepřidá se do žádné projevy pomocí této entity. 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Popisek textu s rolí v příkladu utterance

1. Přejít na stránku s podrobnostmi záměru, která obsahuje příklad projevy, který používá roli. 
1. Pokud chcete s rolí označit popisek, vyberte v příkladu utterance popisek entity (plná čára pod textem) a pak v rozevíracím seznamu vyberte **Zobrazit v paletě entit** . 

    > [!div class="mx-imgBorder"]
    > ![snímek obrazovky s výběrem zobrazení v paletě entit](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    Na pravé straně se otevře paleta entit. 

1. Vyberte entitu, potom klikněte na dolní část palety a vyberte roli. 

    > [!div class="mx-imgBorder"]
    > ![snímek obrazovky s výběrem zobrazení v paletě entit](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>Přidejte vzor. libovolnou entitu

[Vzor. všechny](luis-concept-entity-types.md) entity jsou platné jenom v [vzorcích](luis-how-to-model-intent-pattern.md), ne záměrech, jako je například projevy. Tento typ entity pomáhá najít koncové entity různé délky a výběr slov LUIS. Protože tato entita se používá ve vzorku, LUIS ví, kde koncové entity je v šabloně utterance.

### <a name="steps-to-create-a-patternany-entity"></a>Postup vytvoření vzoru. kterákoli entita

1. V části **sestavení** vyberte **entity** na levém panelu a potom vyberte **+ vytvořit**.

1. V dialogovém okně **Zvolte typ entity** zadejte název entity do pole **název** a jako **typ** vyberte **vzor. any** a pak vyberte **vytvořit**.

    Jakmile [vytvoříte vzor utterance](luis-how-to-model-intent-pattern.md) pomocí této entity, bude se tato entita extrahovat pomocí kombinovaného algoritmu pro porovnávání počítačů a textu. 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>Vytvořte šablonu vzoru utterance pro použití vzoru. libovolnou entitu

Chcete-li použít vzor. jakékoli entity, přidejte vzor na stránce **vzory** v části **zlepšení výkonu aplikace** se správnou syntaxí složené závorky, například `Where is **{HumanResourcesFormTitle}** on the server?`.

Pokud zjistíte, že váš vzor zahrnující entitu Pattern.any neextrahuje entity správně, můžete tento problém opravit pomocí [explicitního seznamu](reference-pattern-syntax.md#explicit-lists). 

## <a name="do-not-change-entity-type"></a>Neměnit typ entity

Služba LUIS neumožňuje změnit typ entity, protože nebude vědět, co se má přidat nebo odebrat k vytvoření této entity. Chcete-li změnit typ, je lepší vytvořit novou entitu správného typu s mírně odlišný název. Po vytvoření entity v každé utterance, odeberte starý název s popiskem entity a přidejte nový název entity. Jakmile se všechny projevy mít relabeled, odstraňte staré entitu. 

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
    * [Syntaktick](reference-pattern-syntax.md)
* [Úložiště GitHub předem připravené entity](https://github.com/Microsoft/Recognizers-Text)
* [Koncepce extrakce dat](luis-concept-data-extraction.md)


 
