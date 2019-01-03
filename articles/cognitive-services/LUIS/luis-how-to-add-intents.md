---
title: Přidání záměrů
titleSuffix: Language Understanding - Azure Cognitive Services
description: Přidání záměrů do vaší aplikace LUIS k identifikaci skupin dotazy nebo příkazy, které mají stejné záměry.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.component: language-understanding
ms.topic: article
ms.date: 10/24/2018
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: c8bcbe011dc4ff9841a06b914a90ae7f9a14ee74
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53598495"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Přidání záměrů k určení uživatelů záměr projevů

Přidat [záměry](luis-concept-intent.md) do vaší aplikace LUIS k identifikaci skupin dotazy nebo příkazy, které mají stejný účel. 

V horním navigačním panelu se spravují záměry **sestavení** části, klikněte na levém panelu **záměry**. 

## <a name="create-an-app"></a>Vytvoření nové aplikace

1. Přihlaste se k [LUIS](https://www.luis.ai) portálu.

1. Vyberte **Create new app** (Vytvořit novou aplikaci). 

1. Pojmenujte novou aplikaci `MyHumanResourcesApp`. Vyberte **Angličtina** jazykovou verzi. Popis je volitelný. 

1. Vyberte **Done** (Hotovo). 

## <a name="add-intent"></a>Přidat záměr

1. Aplikace se otevře **záměry** seznamu.

1. Na stránce **Intents** (Záměry) vyberte **Create new intent** (Vytvořit nový záměr).

1. V **vytvořit nové záměr** dialogového okna zadejte název záměru `GetEmployeeInformation`a klikněte na tlačítko **provádí**.

    ![Přidat záměr](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

## <a name="add-an-example-utterance"></a>Přidat utterance příklad

Příklad projevy jsou příkladem text otázky uživatele nebo příkazy. Představuje Language Understanding (LUIS), že je potřeba přidat příklad projevy záměru.

1. Na **GetEmployeeInformation** záměru podrobností zadejte relevantní utterance očekáváte, že od uživatelů, jako například `Does John Smith work in Seattle?` do textového pole pod název záměru a potom stiskněte klávesu Enter.
 
    ![Stránce s podrobnostmi o snímek obrazovky záměrů, se zvýrazněným utterance](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    Služba LUIS převede všechny projevy na malá písmena a přidá mezery kolem tokenů, jako je například spojovníky.

## <a name="intent-prediction-discrepancy-errors"></a>Chyby nesrovnalosti záměru predikcí 

Utterance v záměru pravděpodobně záměru předpovědi relevanci vybrané záměr a skóre předpovědi. LUIS označuje této nesrovnalosti s červeným rámečkem okolo položky **označené záměr** na řádku utterance příklad. 

![Stránce s podrobnostmi o snímek obrazovky záměrů, s chybami nesrovnalosti utterance predikcí](./media/luis-how-to-add-intents/prediction-discrepancy-intent.png) 

V horním navigačním panelu vyberte **Train**. Predikce nesrovnalosti je nyní pryč.

## <a name="add-a-custom-entity"></a>Přidání vlastní entity

Jakmile utterance se přidá k záměru, můžete vybrat text z v rámci utterance k vytvoření vlastní entity. Vlastní entity je způsob, jak text značky pro extrakci, spolu s správné záměr. 

1. Vybrat slovo, `Seattle`, v utterance. Hranaté závorky jsou vykreslovány vedle kolem textu a zobrazí se rozevírací nabídka. 

    ![Stránce s podrobnostmi o snímek obrazovky záměrů, vytvoření vlastní entity](./media/luis-how-to-add-intents/create-custom-entity.png) 

    Tento příklad vybere jednoslovné označit jako entity. Můžete označit jeden funguje a frází jako entity.

1. Začátek textového pole nabídky, zadejte `Location`a pak vyberte **vytvořit novou entitu**. 

    ![Stránce s podrobnostmi o snímek obrazovky záměrů, vytváří se vlastní entitu s názvem](./media/luis-how-to-add-intents/create-custom-entity-name.png) 

1. V **jaký typ entity chcete vytvořit?** automaticky otevírané okno pro vytvoření entity, ověřit, zda **název entity** je _umístění_a **typ entity**  je _jednoduché_. Vyberte **Done** (Hotovo).

## <a name="entity-prediction-discrepancy-errors"></a>Entita předpovědi nesrovnalosti chyby 

Entita je podtrženo červenou barvou pro označení [entity předpovědi nesrovnalosti](luis-how-to-add-example-utterances.md#entity-status-predictions). Protože je to první výskyt entity, nejsou dost příklady LUIS s vysokou spolehlivostí, který tento text je označen správného entity. Odebere této nesrovnalosti školení aplikace. 

![Stránce s podrobnostmi o snímek obrazovky záměrů, vlastní entitu s názvem modře zvýrazněna](./media/luis-how-to-add-intents/create-custom-entity-name-blue-highlight.png) 

Text se zvýrazní modrou barvu, která entity.  

## <a name="add-a-prebuilt-entity"></a>Přidat předem připravených entit

Informace najdete v tématu [předem připravených entit](luis-how-to-add-entities.md#add-prebuilt-entity).

## <a name="using-the-contextual-toolbar"></a>Použití kontextových panelu nástrojů

Při výběru nejmíň jeden příklad projevy v seznamu, zaškrtněte políčka nalevo od utterance, panelu nástrojů nad seznamem utterance umožňuje provádět následující akce:

* Záměr přiřazení: utterance(s) přesunout na jiný záměr
* Odstranit utterance(s)
* Filtry entity: Zobrazit pouze projevy obsahující filtrované entitu
* Zobrazit všechny a pouze chyby: Zobrazit projevy s chybami předpovědi nebo zobrazit všechny projevy
* Zobrazení entity/tokeny: zobrazení entity s názvy entit nebo zobrazit nezpracovaný text utterance
* S ikonou lupy: Vyhledejte projevy, které obsahují určitý text

## <a name="working-with-an-individual-utterance"></a>Práce s jednotlivé utterance

Na jednotlivé utterance z nabídky tlačítko se třemi tečkami napravo od utterance lze provést následující akce:

* Úpravy: změnit text utterance
* Odstranit: odebrání záměr utterance. Pokud chcete i utterance, lepší metodou je přesunout **žádný** záměr. 
* Přidejte vzor: Vzor umožňuje provést běžné utterance a označit replaceable text a ignorable – text, a tím snižuje potřebu další projevy v záměr. 

**Označené záměr** sloupec vám umožní změnit záměr utterance.

## <a name="train-your-app-after-changing-model-with-intents"></a>Po změně modelu pomocí tříd Intent trénování vaší aplikace

Po přidání, úprava nebo odebrání záměrů, [trénování](luis-how-to-train.md) a [publikovat](luis-how-to-publish-app.md) vaší aplikace tak, aby provedené změny se použijí pro koncový bod dotazy. 

## <a name="next-steps"></a>Další postup

Další informace o přidávání [příklad projevy](luis-how-to-add-example-utterances.md) s entitami. 
