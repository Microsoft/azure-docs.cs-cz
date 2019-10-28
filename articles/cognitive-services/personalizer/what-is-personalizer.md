---
title: Co je služba Personalizace?
titleSuffix: Azure Cognitive Services
description: Přizpůsobování je cloudová služba API, která umožňuje vybrat nejlepší prostředí pro zobrazování vašich uživatelů a seznámit se s jejich chováním v reálném čase.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 09/19/2019
ms.author: diberry
ms.openlocfilehash: 8a0b0dde50de1806762076025e4f74f19388f81b
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989599"
---
# <a name="what-is-personalizer"></a>Co je služba Personalizace?

Přizpůsobování Azure je cloudová služba API, která umožňuje vybrat nejlepší prostředí pro zobrazování vašich uživatelů a seznámit se s jejich celkovým chováním v reálném čase.

* Poskytněte informace o uživatelích a obsahu a dostanete nejvyšší akci pro zobrazení uživatelů. 
* Před použitím přizpůsobeného přizpůsobování nemusíte data čistit a označovat.
* Poskytněte vám zpětnou vazbu k přizpůsobenému uživateli, když vám to bude vyhovovat. 
* Zobrazení analýz v reálném čase. 

## <a name="how-does-personalizer-work"></a>Jak funguje přizpůsobování?

Přizpůsobení používá modely strojového učení k zjištění, jaká akce má být v kontextu v určitém pořadí nejvyšší. Klientská aplikace nabízí seznam možných akcí s informacemi o nich. a informace o kontextu, které mohou obsahovat informace o uživateli, zařízení atd. Přizpůsobení Určuje akci, která má být provedena. Jakmile klientská aplikace použije zvolenou akci, poskytuje zpětnou vazbu k přizpůsobenému programu ve formě skóre záměna. Po přijetí zpětné vazby automaticky upraví vlastní model, který se používá pro budoucí pořadí. V průběhu času bude přizpůsobovat jeden model, který může navrhnout nejlepší akci pro výběr v jednotlivých kontextech na základě jejich funkcí.

## <a name="how-do-i-use-the-personalizer"></a>Návody použít modul pro přizpůsobení?

![Výběr videa pro zobrazení uživateli pomocí přizpůsobení](media/what-is-personalizer/personalizer-example-highlevel.png)

1. Vyberte si prostředí pro přizpůsobení aplikace.
1. Vytvořte a nakonfigurujte instanci služby přizpůsobení ve Azure Portal. Každá instance je smyčkou přizpůsobování.
1. [Rozhraní API pro řazení](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) použijte k volání individuálního nastavení s informacemi (_funkcemi_) o vašich uživatelích a obsahu (_Akce_). Před použitím přizpůsobeného přizpůsobování nemusíte zadávat vyčištěná a označená data. Rozhraní API je možné volat přímo nebo pomocí sad SDK, které jsou k dispozici pro různé programovací jazyky.
1. V klientské aplikaci Zobrazte uživatele podle vlastní akce.
1. Použijte [API pro odměnu](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) a poskytněte zpětnou vazbu k přizpůsobenému objektu, který označuje, jestli akce přizpůsobené uživatelem vybrala uživatel. Toto je _[skóre záměna](concept-rewards.md)_ .
1. Podívejte se na analýzy v Azure Portal, abyste vyhodnotili, jak systém funguje a jak vaše data pomáhají přizpůsobit.

## <a name="where-can-i-use-personalizer"></a>Kde můžu použít přizpůsobování?

Klientská aplikace může například přidat přizpůsobení do:

* Přizpůsobení článku, který je zvýrazněný na webu příspěvky    
* Optimalizujte umístění reklamy na webu.
* Zobrazení individuální "Doporučené položky" na nákupním webu.
* Navrhněte prvky uživatelského rozhraní, jako jsou filtry, které se mají použít pro konkrétní fotografii.
* Vyberte odpověď robota pro chat k objasnění záměru uživatele nebo navrhnout akci.
* Určete prioritu návrhů toho, co má uživatel dělat jako další krok v obchodním procesu.

Přizpůsobování není služba pro uchovávání a správu informací o profilu uživatele nebo k protokolování předvoleb nebo historie jednotlivých uživatelů. Přizpůsobený modul se učí od jednotlivých funkcí interakce v akci kontextu v jediném modelu, který může získat maximální ceny, když dojde k podobným funkcím. 

## <a name="personalization-for-developers"></a>Individuální nastavení pro vývojáře

Služba pro přizpůsobení má dvě rozhraní API:

* Odeslat informace o vašich uživatelích a obsahu (_akcích_ _) pro_přizpůsobení. Přizpůsobuje odezvu na horní akci.
* Pošlete nám svůj názor na vlastní informace o tom, jak dobře se hodnocení vypracovalo jako [skóre odměňování](concept-rewards.md). 

![Základní sekvence událostí pro přizpůsobení](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>Další kroky

* [Co je nového v přizpůsobování?](whats-new.md)
* [Jak přizpůsobování funguje?](how-personalizer-works.md)
* [Co je posílení učení?](concepts-reinforcement-learning.md)
* [Informace o funkcích a akcích pro požadavek na řazení](concepts-features.md)
* [Informace o určování skóre pro žádost o odměnu](concept-rewards.md)
* [Použití interaktivní ukázky](https://personalizationdemo.azurewebsites.net/)
