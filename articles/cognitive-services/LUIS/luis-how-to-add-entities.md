---
title: Přidání entit – LUIS
titleSuffix: Azure Cognitive Services
description: Vytvořte entity pro extrahování klíčových dat z projevy uživatelů v aplikacích language understanding (LUIS). Extrahovaná data entity používá klientská aplikace k požadavkům zákazníků fullfil.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220905"
---
# <a name="add-entities-to-extract-data"></a>Přidání entit pro extrahování dat 

Vytvořte entity pro extrahování klíčových dat z projevy uživatelů v aplikacích language understanding (LUIS). Extrahovaná data entit jsou používána klientskou aplikací k požadavkům zákazníků fullfil.

Entita představuje slovo nebo frázi uvnitř utterance, které chcete extrahovat. Entity popisují informace relevantní pro záměr a někdy jsou nezbytné pro to, aby vaše aplikace prováděla svůj úkol. Můžete vytvořit entity při přidání příklad utterance k záměru nebo na rozdíl od (před nebo po) přidání příklad utterance k záměru.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>Plánování entit a vytvoření a označení popisku

Entity získané počítačem lze vytvořit z ukázkových projevů nebo vytvořit ze stránky **Entity.** 

Obecně je osvědčeným postupem trávit čas plánováním entit před vytvořením entity navoděné počítačem na portálu. Potom vytvořte entitu získanou počítačem z promluvy příkladu s tolika podrobnostmi v dílčích součástech a popisovačech a omezeních, kolik znáte v té době. Kurz [rozložitelné entity](tutorial-machine-learned-entity.md) ukazuje, jak používat tuto metodu. 

V rámci plánování entit můžete vědět, že potřebujete entity odpovídající textu (například předem vytvořené entity, entity regulárních výrazů nebo entity seznamu). Můžete je vytvořit ze stránky **Entity** předtím, než jsou označeny v příkladech projevy. 

Při popisování můžete buď označit jednotlivé entity a pak vytvořit na nadřazenou entitu naučnou počítačem. Nebo můžete začít s nadřazenou entitou naučenou počítačem a rozložit se na podřízené entity. 

> [!TIP] 
>Označte všechna slova, která mohou označovat entitu, i v případě, že slova nejsou použita při extrahování v klientské aplikaci. 

## <a name="creating-an-entity-before-or-with-labeling"></a>Vytvoření entity před nebo s popiskem

V následující tabulce zjistíte, které entity, kde chcete vytvořit nebo přidat jednotlivé entity do aplikace. 

|Typ entity|Kde vytvořit entitu na portálu LUIS|
|--|--|
|Strojově naučená entita|Entity nebo podrobnosti záměru|
|Entita seznamu|Entity nebo podrobnosti záměru|
|Entiay regulárního výrazu|Entity|
|Entita Pattern.any|Entity|
|Předem sestavená entita|Entity|
|Předem vytvořená entita domény|Entity|

Můžete vytvořit všechny entity ze stránky **Entity** nebo můžete vytvořit několik entit jako součást označení entity v příkladu utterance na stránce **podrobností záměru.** Entitu můžete _označit_ pouze v příkladu utterance ze stránky **podrobností záměru.** 

## <a name="create-a-machine-learned-entity"></a>Vytvoření entity naváděné strojem

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>Vytvoření entity odpovídající textu

Pomocí entit odpovídajících textu poskytujete několik způsobů extrahování dat:

|Entity odpovídající textu|Účel|
|--|--|
|[Entita seznamu](#add-list-entities-for-exact-matches)|seznam kanonických jmen spolu se synonymy jako alternativní formy|
|Entiay regulárního výrazu|shoda textu pomocí entity regulárního výrazu|
|[Předem sestavená entita](tutorial-machine-learned-entity.md#add-prebuilt-number-to-help-extract-data)|odpovídají běžným datovým typům, jako je číslo, e-mail, datum|
|Předem vytvořená entita domény|shody pomocí vybraných domén předmětů|
|[Pattern.any](#add-a-patternany-entity)| tak, aby odpovídaly entitám, které lze snadno zaměnit s okolním textem|  

Předem sestavené entity fungují bez poskytnutí vlastních trénovacích dat. Ostatní entity potřebují poskytnout data školení zákazníků (například položky entity Seznam) nebo výraz (například regulární výraz nebo pattern.any).

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>Jak vytvořit novou vlastní entitu

1. Na portálu LUIS přejděte do části **Spravovat** a potom na stránku **Entity.** 
1. Vyberte **+ Vytvořit**, pak vyberte typ entity. 
1. Pokračujte v konfiguraci entity a po dokončení vyberte **Vytvořit.** 

### <a name="add-list-entities-for-exact-matches"></a>Přidání entit seznamu pro přesné shody

Entity seznamu představují pevnou uzavřenou sadu souvisejících slov. Zatímco vy, jako autor, můžete změnit seznam, LUIS nebude zvětšit nebo zmenšit seznam. Můžete také importovat do existující entity seznamu pomocí formátu [list entity .json(reference-entity-list.md#example-json-to-import-into-list-entity). 

Následující seznam ukazuje kanonický název a synonyma. 

|Barva - název položky seznamu|Barva - synonyma|
|--|--|
|Červený|karmínový, krev, jablko, hasičský vůz|
|Blue|nebe, azurová, kobalt|
|Green|kelly, vápno|

Postup slouží k vytvoření entity seznamu. Po vytvoření entity seznamu není nutné označovat příklad projevy v záměru. Položky seznamu a synonyma jsou spárovány pomocí přesného textu. 

1. V části **Sestavení** vyberte **Entity** v levém panelu a pak vyberte **+ Vytvořit**.

1. V **dialogovém** okně Vytvořit typ entity zadejte název `Colors` entity, například a vyberte **List**.
1. V **dialogovém okně Vytvořit entitu seznamu** zadejte do **podseznamu Přidat nový....** název položky seznamu, například `Green`, a přidejte synonyma.

    > [!div class="mx-imgBorder"]
    > ![Vytvořte seznam barev jako entitu seznamu na stránce podrobností entity.](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. Po přidání položek seznamu a synonym vyberte **Vytvořit**.

    Až budete hotovi se skupinou změn v aplikaci, nezapomeňte **trénovat** aplikaci. Netrénujte aplikaci po jedné změně. 

    > [!NOTE]
    > Tento postup ukazuje vytvoření a označení entity seznamu z příkladu utterance na stránce **podrobností záměru.** Můžete také vytvořit stejnou entitu ze stránky **Entity.**

## <a name="add-a-role-for-an-entity"></a>Přidání role pro entitu

Role je pojmenovaná podtyp entity založená na kontextu. 

### <a name="add-a-role-to-distinguish-different-contexts"></a>Přidání role k rozlišení různých kontextů

V následujícím utterance existují dvě umístění a každý je specifikován sémanticky slova kolem něj, jako `to` je například a `from`: 

`Pick up the package from Seattle and deliver to New York City.`

V tomto postupu `origin` `destination` přidejte a role předem sestavené geographyV2 entity.

1. V části **Sestavení** vyberte **Entity** v levém panelu.

1. Vyberte **+ Přidat předem vytvořenou entitu**. Vyberte **geographyV2** a pak vyberte **Hotovo**. Tím přidáte předem sestavenou entitu do aplikace.
    
    Pokud zjistíte, že váš vzor zahrnující entitu Pattern.any neextrahuje entity správně, můžete tento problém opravit pomocí [explicitního seznamu](reference-pattern-syntax.md#explicit-lists). 

1. Vyberte nově přidanou předem vytvořenou entitu geographyV2 ze seznamu entit na stránce **Entity.** 
1. Chcete-li přidat novou **+** roli, vyberte možnost **Žádné přidané role**.
1. Do textového pole **Typ role...** zadejte `Origin` název role a zadejte ji. Přidejte druhý název `Destination` role a zadejte jej. 

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s přidáním role Origin u entity Umístění](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    Role je přidána do předem sestavené entity, ale není přidána do žádné projevy pomocí této entity. 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Popisek textu s rolí v ukázkové utterance

1. Přejděte na stránku podrobnosti záměru, která obsahuje příklad projevy, které používají roli. 
1. Chcete-li označit rolí popisek, vyberte popisek entity (plná čára pod textem) v příkladové utterance a pak v rozevíracím seznamu vyberte **Zobrazit v paletě entit.** 

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s výběrem zobrazení v paletě entit](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    Paleta entit se otevře vpravo. 

1. Vyberte entitu, přejděte do dolní části palety a vyberte roli. 

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s výběrem zobrazení v paletě entit](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>Přidání entity pattern.any

[Pattern.any](luis-concept-entity-types.md) entity jsou platné pouze ve [vzorcích](luis-how-to-model-intent-pattern.md), nikoli záměry příklad projevy. Tento typ entity pomáhá luis najít konec entit různé délky a výběru slova. Vzhledem k tomu, že tato entita se používá ve vzoru, LUIS ví, kde je konec entity v šabloně utterance.

### <a name="steps-to-create-a-patternany-entity"></a>Kroky k vytvoření entity pattern.any

1. V části **Sestavení** vyberte **Entity** v levém panelu a pak vyberte **+ Vytvořit**.

1. V **dialogovém** okně Vybrat typ entity zadejte název entity do pole **Název** a vyberte **Pattern.Any** jako **Typ** a pak vyberte **Vytvořit**.

    Jakmile [vytvoříte vzor utterance](luis-how-to-model-intent-pattern.md) pomocí této entity, entita je extrahována s kombinovaným algoritmus učení počítače a text odpovídající. 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>Vytvoření utterance šablony vzoru pro použití entity pattern.any

Chcete-li použít entitu pattern.any, přidejte vzorek na stránku **Vzorky** v části Zlepšit `Where is **{HumanResourcesFormTitle}** on the server?`výkon **aplikace** se správnou syntaxí složené závorky, například .

Pokud zjistíte, že váš vzor zahrnující entitu Pattern.any neextrahuje entity správně, můžete tento problém opravit pomocí [explicitního seznamu](reference-pattern-syntax.md#explicit-lists). 

## <a name="do-not-change-entity-type"></a>Neměnit typ entity

Služba LUIS neumožňuje změnit typ entity, protože neví, co přidat nebo odebrat k vytvoření této entity. Chcete-li změnit typ, je lepší vytvořit novou entitu správného typu s mírně odlišným názvem. Po vytvoření entity odeberte v každé utterance starý název entity s popiskem a přidejte nový název entity. Jakmile jsou všechny projevy byly přeznačeny, odstraňte starou entitu. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Použití předem připravených modelů](howto-add-prebuilt-models.md) 

Další informace:
* Jak [trénovat](luis-how-to-train.md)
* Jak [testovat](luis-interactive-test.md)
* Jak [publikovat](luis-how-to-publish-app.md)
* Vzory:
    * [Koncepty](luis-concept-patterns.md)
    * [Syntaxe](reference-pattern-syntax.md)
* [Předem vytvořené entity úložiště GitHub](https://github.com/Microsoft/Recognizers-Text)
* [Koncepty extrakce dat](luis-concept-data-extraction.md)


 
