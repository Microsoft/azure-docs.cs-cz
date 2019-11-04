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
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 54c9d79c62052daeee76de5dffb1099dc7d75180
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467716"
---
# <a name="create-entities-without-utterances"></a>Vytváření entit bez projevy

Entita představuje slovo nebo frázi uvnitř utterance, který chcete extrahovat. Entita představuje třídu, včetně kolekce podobných objektů (míst, věcí, osob, událostí nebo konceptů). Entity popisují informace týkající se záměru a někdy jsou nezbytné, aby vaše aplikace mohla provádět její úkoly. Můžete vytvořit entity, když přidáte utterance k záměru nebo od (před nebo po) přidání utterance k záměru.

Entity můžete do aplikace LUIS přidat, upravit nebo odstranit pomocí **seznamu entity** na stránce **entity** . LUIS nabízí dva hlavní typy entit: [předem připravené entity](luis-reference-prebuilt-entities.md)a vlastní [entity](luis-concept-entity-types.md#types-of-entities).

Po vytvoření entity, která je v počítači zjištěna, je nutné označit tuto entitu ve všech příkladech utterance všech záměrů, ve kterých se nachází.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<a name="add-prebuilt-entity"></a>

## <a name="add-a-prebuilt-entity-to-your-app"></a>Přidat předem vytvořenou entitu do aplikace

Mezi běžné předem připravené entity přidané do aplikace patří *Number* a *datetimeV2*. 

1. V aplikaci v části Build ( **sestavení** ) vyberte **entity** na levém panelu.
 
1. Na stránce **entity** vyberte **Přidat předem připravené entity**.

1. V dialogovém okně **Přidat předem připravené entity** vyberte **počet** a **datetimeV2** předem sestavené entity. Potom vyberte **Done** (Hotovo).

    ![Snímek obrazovky dialogového okna Přidat předem vytvořenou entitu](./media/add-entities/list-of-prebuilt-entities.png)

<a name="add-simple-entities"></a>

## <a name="add-simple-entities-for-single-concepts"></a>Přidání jednoduchých entit pro jednoduché koncepty

Jednoduchá Entita popisuje jeden koncept. Pomocí následujícího postupu můžete vytvořit entitu, která extrahuje názvy oddělení společnosti, jako jsou *lidské zdroje* nebo *operace*.   

1. V aplikaci vyberte část **sestavení** , pak na levém panelu vyberte **entity** a pak vyberte **vytvořit novou entitu**.

1. V automaticky otevíraném okně zadejte do pole **název entity** text `Location`, v seznamu **typ entity** vyberte možnost **jednoduché** a potom vyberte **Hotovo**.

    Jakmile je tato entita vytvořena, přejdete na všechny záměry, které obsahují příklad projevy, který obsahuje entitu. Vyberte text v příkladu utterance a označte text jako entitu. 

    [Seznam frází](luis-concept-feature.md) se běžně používá ke zvýšení signálu jednoduché entity.

<a name="add-regular-expression-entities"></a>

## <a name="add-regular-expression-entities-for-highly-structured-concepts"></a>Přidání entit regulárních výrazů pro vysoce strukturované koncepty

Entita regulárního výrazu se používá k vyžádání dat z utterance na základě regulárního výrazu, který zadáte. 

1. V aplikaci v levém navigačním panelu vyberte **entity** a pak vyberte **vytvořit novou entitu**.

1. V místním dialogovém okně zadejte `Human resources form name` do pole **název entity** , v seznamu **typ entity** vyberte **regulární výraz** , zadejte regulární výraz `hrf-[0-9]{6}`a potom vyberte **Hotovo**. 

    Tento regulární výraz odpovídá znakům literálu `hrf-`, a 6 číslic, které reprezentují číslo formuláře pro formulář lidských zdrojů.

<a name="add-composite-entities"></a>

## <a name="add-composite-entities-to-group-into-a-parent-child-relationship"></a>Přidání složených entit do skupin do vztahu nadřízený-podřízený

Vytvořením složené entity můžete definovat vztahy mezi entitami různých typů. V následujícím příkladu entita obsahuje regulární výraz a předem sestavenou entitu názvu.  

V `Send hrf-123456 to John Smith`utterance je text `hrf-123456` shodný s [regulárním výrazem](#add-regular-expression-entities) lidských zdrojů a `John Smith` je extrahován s předem vytvořenou entitou Person. Každá entita je součástí větší nadřazené entity. 

1. V aplikaci vyberte **entity** z levé navigační části **sestavení** a pak vyberte **Přidat předem vytvořenou entitu**.

1. Přidejte předem sestavenou **osobu**EntityName. Pokyny najdete v tématu [Přidání předem sestavených entit](#add-prebuilt-entity). 

1. V levém navigačním panelu vyberte **entity** a pak vyberte **vytvořit novou entitu**.

1. V automaticky otevíraném dialogovém okně zadejte do pole **název entity** `SendHrForm` a pak vyberte **složené** ze seznamu **typ entity** .

1. Vyberte **Přidat podřízenou** položku, chcete-li přidat novou podřízenou položku.

1. V části **podřízené #1**v seznamu vyberte **číslo** entity.

1. V části **podřízené #2**v seznamu vyberte **název formuláře entity lidské prostředky** . 

1. Vyberte **Done** (Hotovo).

<a name="add-pattern-any-entities"></a>

## <a name="add-patternany-entities-to-capture-free-form-entities"></a>Přidat vzor. jakékoli entity pro zachycení entit Free-Form

[Vzor. jakékoli](luis-concept-entity-types.md) entity jsou platné pouze ve [vzorcích](luis-how-to-model-intent-pattern.md), nikoli v záměrech. Tento typ entity pomáhá LUIS najít konec entit s různou délkou a volbou Wordu. Vzhledem k tomu, že tato entita se používá ve vzoru, LUIS ví, kde je konec entity v šabloně utterance.

Pokud má aplikace `FindHumanResourcesForm` záměr, může dojít ke konfliktu názvu extrahované formuláře s předpovědí záměru. Chcete-li vyjasnit, která slova jsou v nadpisu formuláře, použijte vzor. any v rámci vzoru. Předpověď LUIS začíná na utterance. Nejprve je utterance a při nalezení entit zkontrolován a spárován s entitami, potom je vzor zkontrolován a spárován. 

V `Where is Request relocation from employee new to the company on the server?`utterance je název formuláře obtížné, protože není kontextem zřejmé, kde končí název a kde zbytek utterance začíná. Názvy mohou být libovolné pořadí slov, včetně jednoho slova, složité fráze s interpunkčním znaménkem a nesmyslná řazení slov. Vzor umožňuje vytvořit entitu, kde lze extrahovat úplnou a přesnou entitu. Po nalezení nadpisu je záměr `FindHumanResourcesForm` předvídatelný, protože to je záměr pro vzor.

1. V části **sestavení** vyberte **entity** na levém panelu a pak vyberte **vytvořit novou entitu**.

1. V dialogovém okně **Přidat entitu** zadejte do pole **název entity** `HumanResourcesFormTitle` a jako **typ entity**vyberte **model. any** .

    Chcete-li použít vzor. jakékoli entity, přidejte vzor na stránce **vzory** v části **zlepšení výkonu aplikace** se správnou syntaxí složené závorky, například `Where is **{HumanResourcesFormTitle}** on the server?`.

    Pokud zjistíte, že váš vzor zahrnující entitu Pattern.any neextrahuje entity správně, můžete tento problém opravit pomocí [explicitního seznamu](luis-concept-patterns.md#explicit-lists). 

<a name="add-a-role-to-pattern-based-entity"></a>

## <a name="add-a-role-to-distinguish-different-contexts"></a>Přidání role pro odlišení různých kontextů

Role je pojmenovaný podtyp založený na kontextu. Je k dispozici ve všech entitách, včetně předem sestavených a nestrojově zjištěných entit. 

Syntaxe role je **`{Entityname:Rolename}`** , kde je název entity následován dvojtečkou a potom názvem role. Například, `Move {personName} from {Location:Origin} to {Location:Destination}`.

1. V části **Build (sestavení** ) vyberte **entity** na levém panelu.

1. Vyberte **Create new entity** (Vytvořit novou entitu). Zadejte název `Location`. Vyberte typ **jednoduchý** a vyberte **Hotovo**. 

1. Na levém panelu vyberte **entity** a pak vyberte nové **umístění** entity vytvořené v předchozím kroku.

1. Do textového pole **název role** zadejte název role `Origin` a zadejte. Přidejte název druhé role `Destination`. 

    ![Snímek obrazovky s přidáním role původu do entity umístění](./media/add-entities/roles-enter-role-name-text.png)

<a name="add-list-entities"></a>

## <a name="add-list-entities-for-exact-matches"></a>Přidat seznam entit pro přesné shody

Seznam entit představuje pevně uzavřenou sadu příbuzných slov. 

U aplikací pro lidské zdroje můžete mít seznam všech oddělení spolu s případnými synonymy pro oddělení. Při vytváření entity nemusíte znát všechny hodnoty. Po kontrole reálných uživatelských projevy pomocí synonym můžete přidat další.

1. V části **sestavení** vyberte **entity** na levém panelu a pak vyberte **vytvořit novou entitu**.

1. Do pole **název entity** zadejte v dialogovém okně **přidat entitu** `Department` a jako **typ entity**vyberte **seznam** . Vyberte **Done** (Hotovo).
  
1. Stránka Seznam entit vám umožní přidat normalizované názvy. Do textového pole **hodnoty** zadejte název oddělení pro seznam, například `HumanResources` pak na klávesnici stiskněte klávesu ENTER. 

1. Napravo od normalizované hodnoty zadejte synonyma a stisknutím klávesy ENTER na klávesnici za každou položku.

1. Pokud chcete pro seznam použít více normalizovaných položek, vyberte možnost **doporučit** pro zobrazení možností ze [sémantického slovníku](luis-glossary.md#semantic-dictionary).

    ![Snímek obrazovky s výběrem funkce doporučit pro zobrazení možností](./media/add-entities/hr-list-2.png)


1. Vyberte položku v seznamu Doporučené a přidejte ji jako normalizovanou hodnotu, nebo výběrem **Přidat vše** přidejte všechny položky. 
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

LUIS neumožňuje změnit typ entity, protože nevíte, co přidat nebo odebrat k vytvoření této entity. Aby bylo možné typ změnit, je vhodnější vytvořit novou entitu správného typu s trochu odlišným názvem. Po vytvoření entity odstraňte v každém utterance starý název entity a přidejte nový název entity. Po přeznačení všech projevy se odstraní stará entita. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-pattern-from-an-example-utterance"></a>Vytvoření vzoru z příkladu utterance

Přečtěte si téma [Přidání vzoru z existující stránky utterance na záměr nebo entitu](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="train-your-app-after-changing-model-with-entities"></a>Výuka aplikace po změně modelu pomocí entit

Po přidání, úpravě nebo odebrání entit, [školení](luis-how-to-train.md) a [publikování](luis-how-to-publish-app.md) aplikace pro změny, které mají vliv na dotazy koncového bodu. 

## <a name="next-steps"></a>Další kroky

Další informace o předem připravených entitách najdete v tématu věnovaném [rozpoznávání – textový](https://github.com/Microsoft/Recognizers-Text) projekt. 

Informace o tom, jak se entita zobrazuje v odpovědi na koncový bod JSON, najdete v tématu věnovaném [extrakci dat](luis-concept-data-extraction.md) .

Teď, když jste přidali záměry, projevy a entity, máte základní aplikaci LUIS. Naučte se, jak [vlakovat](luis-how-to-train.md), [testovat](luis-interactive-test.md)a [publikovat](luis-how-to-publish-app.md) vaši aplikaci.
 
