---
title: Funkce - LUIS
titleSuffix: Azure Cognitive Services
description: Přidejte do jazykového modelu funkce, které poskytují rady o tom, jak rozpoznat vstup, který chcete označit nebo klasifikovat.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: 5b8257e24cf52d01be8065d97db17fd685aa316d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531894"
---
# <a name="machine-learned-features"></a>Funkce naučené strojem

Ve strojovém učení je _funkce_ rozlišovací vlastností nebo atributem dat, které váš systém sleduje & se učí. V jazyce porozumění (LUIS), funkce popisuje a vysvětluje, co je důležité o vaše záměry a entity.

Na [portálu LUIS preview](https://preview.luis.ai)jsou funkce _popisovače,_ protože se používají k _popisu_ záměru nebo entity.

## <a name="features-_descriptors_-in-language-understanding"></a>Funkce _(popisovače)_ v jazyce Porozumění

Funkce, označované také jako popisovače, popisují vodítka, která pomáhají jazykově rozumět při identifikaci ukázkových promluv. K funkcím patří:

* Seznam frází jako funkce pro záměry nebo entity
* Entity jako prvky pro záměry nebo entity

Prvky by měly být považovány za nezbytnou součást vašeho schématu pro rozklad modelu.

## <a name="what-is-a-phrase-list"></a>Co je seznam frází

Seznam frází je seznam slov, frází, čísel nebo jiných znaků, které pomáhají identifikovat koncept, který se pokoušíte identifikovat. Seznam nerozlišuje malá a velká písmena.

## <a name="when-to-use-a-phrase-list"></a>Kdy použít seznam frází

Se seznamem frází LUIS považuje kontext a generalizuje k identifikaci položek, které jsou podobné, ale není přesnou shodou textu. Pokud potřebujete, aby vaše aplikace LUIS bylo možné generalizovat a identifikovat nové položky, použijte seznam frází.

Pokud chcete mít možnost rozpoznat nové instance, jako je plánovač schůzek, který by měl rozpoznat názvy nových kontaktů, nebo aplikace inventáře, která by měla rozpoznat nové produkty, začněte s entitou naučenou počítačem. Pak vytvořte seznam frází, který pomáhá LUIS najít slova s podobným významem. Tento seznam frází vede LUIS rozpoznat příklady přidáním další význam hodnoty těchto slov.

Seznamy frází jsou jako slovník specifický pro doménu, který pomáhá zlepšit kvalitu porozumění záměrům i entitám.

## <a name="considerations-when-using-a-phrase-list"></a>Důležité informace při použití seznamu frází

Ve výchozím nastavení se na všechny modely v aplikaci použije seznam frází. To bude fungovat pro seznamy frází, které mohou přes všechny záměry a entity. Chcete-li rozluštitelnost, měli byste použít seznam frází pouze na modely, pro které je relevantní.

Pokud vytvoříte seznam frází (vytvořený globálně ve výchozím nastavení), později jej použijete jako deskriptor (prvek) na konkrétní model, bude odebrán z ostatních modelů. Toto odebrání přidává relevanci seznamu frází pro model, na který se používá, což pomáhá zlepšit přesnost, kterou poskytuje v modelu.

Příznak `enabledForAllModels` řídí tento obor modelu v rozhraní API.

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>Jak používat seznam frází

[Seznam frází vytvořte,](luis-how-to-add-features.md) pokud váš záměr nebo entita obsahuje slova nebo fráze, které jsou důležité, například:

* oborové termíny
* Slang
* Zkratky
* jazyk specifický pro společnost
* jazyk, který je z jiného jazyka, ale často používaný ve vaší aplikaci
* klíčová slova a fráze ve vašem příkladu projevy

**Nepřidávejte** všechna možná slova nebo fráze. Místo toho přidejte několik slov nebo frází najednou, pak přeškolte a publikujte. Jak seznam roste v průběhu času, můžete najít některé termíny mají mnoho forem (synonyma). Rozdělte je na jiný seznam.

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>Kdy použít entitu jako prvek

Entitu lze přidat jako prvek na úrovni záměru nebo entity.

### <a name="entity-as-a-feature-to-an-intent"></a>Entita jako prvek záměru

Přidejte entitu jako popisovač (funkce) k záměru, když je detekce této entity významná pro záměr.

Pokud je například záměrem rezervace letu a entita je informace o letence (například počet sedadel, původ a cíl), mělo by nalezení entity informací o letence přidat váhu předvídavosti záměru letu knihy.

### <a name="entity-as-a-feature-to-another-entity"></a>Entita jako prvek pro jinou entitu

Entita (A) by měla být přidána jako funkce k jiné entitě (B), pokud je detekce této entity (A) významná pro predikci entity (B).

Pokud je například zjištěna entita ulice (A), přičte nalezení adresy ulice (A) k predikci entity dodací adresy (B) váhu.

## <a name="best-practices"></a>Osvědčené postupy
Seznamte se s [doporučenými postupy](luis-concept-best-practices.md).

## <a name="next-steps"></a>Další kroky

* [Rozšíření](schema-change-prediction-runtime.md) modelů aplikací za běhu předpovědi
* Další informace o tom, jak přidat funkce do aplikace LUIS, najdete v tématu [Přidání funkcí.](luis-how-to-add-features.md)
