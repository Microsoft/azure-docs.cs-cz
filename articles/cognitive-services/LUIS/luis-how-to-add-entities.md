---
title: Přidat entity – LUIS
titleSuffix: Azure Cognitive Services
description: Vytvořte entity pro extrakci klíčových dat z uživatelských projevy v aplikacích Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 80e1052cb7acbdcec2dcb94f1667cae3c554d18e
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932927"
---
# <a name="create-entities-without-utterances"></a>Vytvoření entity bez projevy

Entita představuje slovo nebo frázi v utterance, který chcete, aby byl extrahován. Entita představuje třídu, včetně kolekce podobných objektů (míst, věcí, osob, událostí nebo konceptů). Entity popisují informace související s cílem a někdy jsou nezbytné pro vaši aplikaci k provedení svých úkolů. Můžete vytvořit entity, když přidáte utterance k záměru nebo od (před nebo po) přidání utterance k záměru.

Můžete přidat, upravit nebo odstranit entity v aplikaci LUIS prostřednictvím **seznam entit** na **entity** stránky. Služba LUIS nabízí dva hlavní typy entit: [předem připravených entit](luis-reference-prebuilt-entities.md)a vlastní [vlastní entity](luis-concept-entity-types.md#types-of-entities).

Po vytvoření entity, která je v počítači zjištěna, je nutné označit tuto entitu ve všech příkladech utterance všech záměrů, ve kterých se nachází.

<a name="add-prebuilt-entity"></a>

## <a name="add-a-prebuilt-entity-to-your-app"></a>Přidat předem vytvořenou entitu do aplikace

Jsou přidány do aplikace běžné předem připravených entit *číslo* a *datetimeV2*. 

1. Ve vaší aplikaci z **sestavení** vyberte **entity** na levém panelu.
 
1. Na **entity** stránce **přidání předem připravených entit**.

1. V **přidání předem připravených entit** dialogové okno, vyberte **číslo** a **datetimeV2** předem připravených entit. Potom vyberte **Done** (Hotovo).

    ![Dialogové okno snímek obrazovky přidání předem připravených entit](./media/add-entities/list-of-prebuilt-entities.png)

<a name="add-simple-entities"></a>

## <a name="add-simple-entities-for-single-concepts"></a>Přidání jednoduchých entit pro jednoduché koncepty

Jednoduché entity popisuje jeden koncept. Pomocí následujícího postupu vytvořte entitu, která extrahuje názvy oddělení společnosti, jako *lidských zdrojů* nebo *operace*.   

1. Ve vaší aplikaci, vyberte **sestavení** a potom vyberte **entity** na levém panelu, vyberte **vytvořit novou entitu**.

1. V místním dialogovém okně zadejte `Location` v **název Entity** vyberte **jednoduché** z **typ Entity** seznamu a pak vyberte **provádí**.

    Jakmile se vytvoří tuto entitu, přejděte na všechny příkazy, které mají příklad projevy, které obsahují entity. Výběr textu v příkladu utterance a označte text jako entity. 

    A [seznam frází](luis-concept-feature.md) se běžně používá pro zvýšení signál jednoduchou entitu.

<a name="add-regular-expression-entities"></a>

## <a name="add-regular-expression-entities-for-highly-structured-concepts"></a>Přidání entit regulárních výrazů pro vysoce strukturované koncepty

Regulární výraz entita se používá vyžádá si data z utterance podle regulárních výrazů, které zadáte. 

1. Ve vaší aplikaci, vyberte **entity** v levém navigačním panelu a pak vyberte **vytvořit novou entitu**.

1. V místním dialogovém okně zadejte `Human resources form name` v **název Entity** vyberte **regulárního výrazu** z **typ Entity** seznamu, zadejte regulární výraz `hrf-[0-9]{6}`a pak vyberte **provádí**. 

    Tento regulární výraz odpovídá znakům `hrf-`literálu a pak 6 číslic představuje číslo formuláře pro formulář lidských zdrojů.

<a name="add-composite-entities"></a>

## <a name="add-composite-entities-to-group-into-a-parent-child-relationship"></a>Přidání složených entit do skupin do vztahu nadřízený-podřízený

Je možné definovat vztahy mezi entitami z různých typů tak, že vytvoříte složený entity. V následujícím příkladu obsahuje entitu regulárního výrazu a předem připravených entit názvu.  

V utterance `Send hrf-123456 to John Smith`, text `hrf-123456` je nalezena shoda pro tým lidských zdrojů [regulárního výrazu](#add-regular-expression-entities) a `John Smith` se extrahuje personName předem připravených entit. Každá entita je součástí větší, nadřazená entita. 

1. Ve vaší aplikaci, vyberte **entity** v levém navigačním panelu **sestavení** a potom vyberte **přidat předem připravených entit**.

1. Přidat předem připravených entit **PersonName**. Pokyny najdete v tématu [přidat předem připravených entit](#add-prebuilt-entity). 

1. Vyberte **entity** v levém navigačním panelu a pak vyberte **vytvořit novou entitu**.

1. V místním dialogovém okně zadejte `SendHrForm` v **název Entity** pole a pak vyberte **složené** z **typ Entity** seznamu.

1. Vyberte **přidat podřízenou položku** přidáte novou podřízenou položku.

1. V **podřízené č. 1**, vyberte entitu **číslo** ze seznamu.

1. V **podřízené č. 2**, vyberte entitu **lidské zdroje tvoří název** ze seznamu. 

1. Vyberte **Done** (Hotovo).

<a name="add-pattern-any-entities"></a>

## <a name="add-patternany-entities-to-capture-free-form-entities"></a>Přidat vzor. jakékoli entity pro zachycení entit Free-Form

[Pattern.Any](luis-concept-entity-types.md) entity platí jenom v [vzory](luis-how-to-model-intent-pattern.md), ne záměry. Tento typ entity pomáhá najít koncové entity různé délky a výběr slov LUIS. Protože tato entita se používá ve vzorku, LUIS ví, kde koncové entity je v šabloně utterance.

Pokud má aplikace `FindHumanResourcesForm` záměru, název extrahované formuláře může kolidovat s záměru předpovědi. Aby bylo možné vyjasnit, která slova jsou v názvu formuláře, použijte Pattern.any v rámci vzoru. Predikce služby LUIS začíná utterance. Nejprve utterance je zaškrtnuto a pro entity, když entity, které najde, pak vzor je zaškrtnuto a. 

V utterance `Where is Request relocation from employee new to the company on the server?`, název formuláře je velmi obtížné, protože není kontextově jasné, kde název končí a kde začíná rest utterance. Názvy může být jakékoli pořadí slov jediné slovo, včetně složitých frází pomocí interpunkce a nesmyslná řazení slov. Vzoru můžete vytvořit entitu kde může být extrahována entity úplné a přesné. Po nalezení názvu `FindHumanResourcesForm` záměr očekává se, že vzhledem k tomu, který je cílem pro vzor.

1. Z **sestavení** vyberte **entity** v levém panelu a pak vyberte **vytvořit novou entitu**.

1. V **Přidat entitu** dialogového okna zadejte `HumanResourcesFormTitle` v **název Entity** a vyberte **Pattern.any** jako **typ Entity**.

    Použití pattern.any entity, přidejte vzor na **vzory** stránku, **zvýšit výkon aplikace** části, se syntaxí správné složenou závorku, jako například `Where is **{HumanResourcesFormTitle}** on the server?`.

    Pokud zjistíte, že váš vzor zahrnující entitu Pattern.any neextrahuje entity správně, můžete tento problém opravit pomocí [explicitního seznamu](luis-concept-patterns.md#explicit-lists). 

<a name="add-a-role-to-pattern-based-entity"></a>

## <a name="add-a-role-to-distinguish-different-contexts"></a>Přidání role pro odlišení různých kontextů

Role je pojmenovaný podtyp založený na kontextu. Je k dispozici ve všech entitách, včetně předem sestavených a nestrojově zjištěných entit. 

Syntaxe role je **`{Entityname:Rolename}`** , kde je název entity následován dvojtečkou a potom názvem role. Například, `Move {personName} from {Location:Origin} to {Location:Destination}`.

1. Z **sestavení** vyberte **entity** na levém panelu.

1. Vyberte **Create new entity** (Vytvořit novou entitu). Zadejte název `Location`. Vyberte typ **jednoduché** a vyberte **provádí**. 

1. Na levém panelu vyberte **entity** a pak vyberte nové **umístění** entity vytvořené v předchozím kroku.

1. V **název Role** textového pole zadejte název role `Origin` a zadejte. Přidejte druhý název role `Destination`. 

    ![Snímek obrazovky přidání role zdroje do umístění entity](./media/add-entities/roles-enter-role-name-text.png)

<a name="add-list-entities"></a>

## <a name="add-list-entities-for-exact-matches"></a>Přidat seznam entit pro přesné shody

Seznam entit představují pevné, uzavřené sadu souvisejících slov. 

Pro aplikaci lidských zdrojů může mít seznam všech oddělení spolu s jakékoli synonyma pro jako vodítko použijte oddělení. Není nutné znát všechny hodnoty při vytvoření entity. Můžete přidat více až po kontrole projevy uživatelů s synonym.

1. Z **sestavení** vyberte **entity** v levém panelu a pak vyberte **vytvořit novou entitu**.

1. V **Přidat entitu** dialogovém okně `Department` v **název Entity** a vyberte **seznamu** jako **typ Entity**. Vyberte **Done** (Hotovo).
  
1. Na stránce seznamu entit umožňuje přidat normalizované názvy. V **hodnoty** textového pole zadejte název oddělení pro seznam, jako například `HumanResources` stisknutím klávesy Enter na klávesnici. 

1. Napravo od normalizovanou hodnotu zadejte synonyma, stisknutím klávesy Enter na klávesnici po každé položky.

1. Pokud chcete více normalizované položek pro seznam, vyberte **doporučujeme** zobrazíte možnosti z [sémantické slovníku](luis-glossary.md#semantic-dictionary).

    ![Snímek obrazovky s výběrem funkce doporučit pro zobrazení možností](./media/add-entities/hr-list-2.png)


1. Vyberte položku v seznamu doporučené a přidejte ho jako normalizované hodnoty, nebo vyberte **přidejte všechny** přidat všechny položky. 
    Hodnoty můžete importovat do existující entity seznamu pomocí následujícího formátu JSON:

    ```JSON
    [
        {
            "canonicalForm": "Blue",
            "list": [
                "navy",
                "royal",
                "baby"
            ]
        },
        {
            "canonicalForm": "Green",
            "list": [
                "kelly",
                "forest",
                "avacado"
            ]
        }
    ]  
    ```

<a name="change-entity-type"></a>

## <a name="do-not-change-entity-type"></a>Neměnit typ entity

Služba LUIS neumožňuje změnit typ entity, protože nebude vědět, co se má přidat nebo odebrat k vytvoření této entity. Chcete-li změnit typ, je lepší vytvořit novou entitu správného typu s mírně odlišný název. Po vytvoření entity v každé utterance, odeberte starý název s popiskem entity a přidejte nový název entity. Jakmile se všechny projevy mít relabeled, odstraňte staré entitu. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-pattern-from-an-example-utterance"></a>Vytvoření vzoru z příkladu utterance

Zobrazit [přidat vzorek z existující utterance na stránce záměr nebo entity](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="train-your-app-after-changing-model-with-entities"></a>Trénování aplikace po změně modelu s entitami

Po přidání, úprava nebo odebrání entity, [trénování](luis-how-to-train.md) a [publikovat](luis-how-to-publish-app.md) vaší aplikace pro vaše změny ovlivnit dotazy koncový bod. 

## <a name="next-steps"></a>Další postup

Další informace o předem připravených entit, najdete v článku [rozpoznávání textu](https://github.com/Microsoft/Recognizers-Text) projektu. 

Informace o tom, jak se entita zobrazuje v odpovědi JSON koncového bodu dotazu, naleznete v tématu [extrakce dat](luis-concept-data-extraction.md)

Teď, když jste přidali projevy záměry a entity, budete mít základní aplikaci LUIS. Zjistěte, jak [trénování](luis-how-to-train.md), [testování](luis-interactive-test.md), a [publikovat](luis-how-to-publish-app.md) vaší aplikace.
 
