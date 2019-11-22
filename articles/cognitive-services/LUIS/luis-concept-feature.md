---
title: Funkce – LUIS
titleSuffix: Azure Cognitive Services
description: Přidání funkcí do jazykového modelu poskytnout nápovědu, jak rozpoznat vstup, který chcete klasifikovat a označovat.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: b151043babd6c67a17f704bb671a415fcc3ba7b2
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280903"
---
# <a name="machine-learned-features"></a>Funkce učené počítačem 

Ve službě Machine Learning je _funkce_ odlišující se vlastností a atributů dat, které systém sleduje & se naučí. V Language Understanding (LUIS) funkce popisuje a vysvětluje, co je důležité o vašich záměrech a entitách.

Na [portálu Luis ve verzi Preview](https://preview.luis.ai)jsou funkce _popisovače_ , protože se používají k _popisu_ záměru nebo entity.  

## <a name="features-_descriptors_-in-language-understanding"></a>Funkce (_popisovače_) v Language Understanding

Funkce, které jsou známé také jako popisovače, popisují popsaných prvků, které vám pomůžou Language Understanding identifikovat příklad projevy. K funkcím patří: 

* Seznam frází jako funkce pro záměry nebo entit
* Entity jako funkce pro záměry nebo entity

Funkce by měly být považovány za nezbytnou součást schématu pro rozložení modelu. 

## <a name="what-is-a-phrase-list"></a>Co je seznam frází

Seznam frází je seznam slov, frází, číslic nebo jiných znaků, které vám pomohou identifikovat koncept, který se pokoušíte identifikovat. V seznamu se nerozlišují malá a velká písmena. 

## <a name="when-to-use-a-phrase-list"></a>Kdy použít seznam frází

Seznam frází LUIS považuje za kontext a generalizy k identifikaci položek, které jsou podobné, ale nikoli přesného textu. Pokud potřebujete, aby vaše aplikace LUIS dokázala zobecnit a identifikovat nové položky, použijte seznam frází. 

Pokud chcete být schopni rozpoznat nové instance, jako je třeba Plánovač schůzky, který by měl rozpoznávat názvy nových kontaktů, nebo aplikaci inventáře, která by měla rozpoznávat nové produkty, začněte s entitou učenou počítačem. Pak vytvořte seznam frází, který pomůže LUIS hledání slov s podobným významem. Tento seznam frází LUIS k rozpoznávání příkladů přidáním dalšího významu k hodnotě těchto slov. 

Fráze seznamy jsou jako slovník jazyka specifického pro doménu, která usnadní vylepšení kvality znalost záměry a entity. 

## <a name="considerations-when-using-a-phrase-list"></a>Předpoklady při použití seznamu frází

Seznam frází se ve výchozím nastavení aplikuje na všechny modely v aplikaci. To bude fungovat pro seznamy frází, které mohou být více záměry a entitami. V případě deformulace byste měli použít seznam frází jenom na modely, ke kterým je relevantní. 

Pokud vytvoříte seznam frází (ve výchozím nastavení vytvořen globálně), pak ho později použijete jako popisovač (funkce) na konkrétní model, odebere se z ostatních modelů. Toto odebrání přispěje k seznamu frází pro model, pro který je použit, což pomáhá zlepšit přesnost, kterou poskytuje model. 

Příznak `enabledForAllModels` řídí tento obor modelu v rozhraní API. 

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>Jak používat seznam frází

[Vytvořte seznam frází](luis-how-to-add-features.md) , pokud má váš záměr nebo entita slova nebo fráze, které jsou důležité, například:

* oborové výrazy
* slangem
* zkratky
* jazyk specifický pro společnost
* jazyk, který pochází z jiného jazyka, ale často se používá ve vaší aplikaci
* Klíčová slova a fráze v příkladu projevy

Nepřidávat **všechna** možná slova ani fráze. Místo toho přidejte několik slov nebo frází najednou a pak je přehlaste a publikujte. Jakmile se seznam rozroste v průběhu času, může se stát, že některé výrazy mají mnoho forem (synonym). Rozdělte je do jiného seznamu. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>Kdy použít entitu jako funkci 

Entitu lze přidat jako funkci na úrovni záměru nebo entitu. 

### <a name="entity-as-a-feature-to-an-intent"></a>Entita jako součást záměru

Přidejte entitu jako popisovač (funkce) k záměru, když je zjišťování dané entity pro záměr významné.

Například pokud je záměrem rezervovat určitý let a entita má informace o lístkech (například počet míst, původ a cíl), pak hledání entity s informacemi o lístkech by mělo přidat váhu k předpovědi záměru letu. 

### <a name="entity-as-a-feature-to-another-entity"></a>Entita jako funkce pro jinou entitu

Entita (A) by měla být přidána jako funkce jiné entitě (B), pokud je zjišťování této entity (A) významné pro předpověď entity (B).

Například pokud je zjištěna entita adresy (A), pak hledání ulice (A) přidá váhu předpovědi pro entitu dodací adresy (B). 

## <a name="best-practices"></a>Osvědčené postupy
Přečtěte si [osvědčené postupy](luis-concept-best-practices.md).

## <a name="next-steps"></a>Další kroky

Zobrazit [přidat funkce](luis-how-to-add-features.md) získat další informace o tom, jak přidat funkce do vaší aplikace LUIS.