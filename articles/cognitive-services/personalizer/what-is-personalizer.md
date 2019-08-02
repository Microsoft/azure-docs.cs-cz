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
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 99750971e11171c0b315cac38089c216d42c7ba6
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663669"
---
# <a name="what-is-personalizer"></a>Co je služba Personalizace?

Azure Personalizace je cloudová služba rozhraní API umožňující zvolit nejvhodnější prostředí, které se má zobrazovat vašim uživatelům, a učit se z jejich chování v reálném čase.

* Poskytněte informace o uživatelích a obsahu a dostanete nejvyšší akci pro zobrazení uživatelů. 
* Před použitím přizpůsobeného přizpůsobování nemusíte data čistit a označovat.
* Poskytněte vám zpětnou vazbu k přizpůsobenému uživateli, když vám to bude vyhovovat. 
* Zobrazení analýz v reálném čase. 
* Přizpůsobte si použití přizpůsobeného v rámci většího úsilí v oblasti datové vědy k ověření stávajících experimentů.

## <a name="how-does-personalizer-work"></a>Jak funguje přizpůsobování?

Přizpůsobení používá modely strojového učení k zjištění, jaká akce má být v kontextu v určitém pořadí nejvyšší. Klientská aplikace nabízí seznam možných akcí s informacemi o nich. a informace o kontextu, které mohou obsahovat informace o uživateli, zařízení atd. Přizpůsobení Určuje akci, která má být provedena. Jakmile klientská aplikace použije zvolenou akci, poskytuje zpětnou vazbu k přizpůsobenému programu ve formě skóre záměna. Po přijetí zpětné vazby automaticky upraví vlastní model, který se používá pro budoucí pořadí.

## <a name="how-do-i-use-the-personalizer"></a>Návody použít modul pro přizpůsobení?

![Výběr videa pro zobrazení uživateli pomocí přizpůsobení](media/what-is-personalizer/personalizer-example-highlevel.png)

1. Vyberte si prostředí pro přizpůsobení aplikace.
1. Vytvořte a nakonfigurujte instanci služby přizpůsobení ve Azure Portal. Každá instance je smyčkou přizpůsobování.
1. Pomocí sady SDK můžete volat přizpůsobení s informacemi (_funkcemi_) týkajícími se vašich uživatelů a obsahem (_Akce_). Před použitím přizpůsobeného přizpůsobování nemusíte zadávat vyčištěná a označená data. 
1. V klientské aplikaci Zobrazte uživatele podle vlastní akce.
1. Použijte sadu SDK k poskytnutí zpětné vazby k přizpůsobenému uživateli, který označuje, jestli akce přizpůsobené uživatelem vybrala uživatel. Toto je _skóre odměňování_, obvykle mezi-1 a 1.
1. Podívejte se na analýzy v Azure Portal, abyste vyhodnotili, jak systém funguje a jak vaše data pomáhají přizpůsobit.

## <a name="where-can-i-use-personalizer"></a>Kde můžu použít přizpůsobování?

Klientská aplikace může například přidat přizpůsobení do:

* Přizpůsobení článku, který je zvýrazněný na webu příspěvky    
* Optimalizujte umístění reklamy na webu.
* Zobrazení individuální "Doporučené položky" na nákupním webu.
* Navrhněte prvky uživatelského rozhraní, jako jsou filtry, které se mají použít pro konkrétní fotografii.
* Vyberte odpověď robota pro chat k objasnění záměru uživatele nebo navrhnout akci.
* Určete prioritu návrhů toho, co má uživatel dělat jako další krok v obchodním procesu.

## <a name="personalization-for-developers"></a>Individuální nastavení pro vývojáře

Služba pro přizpůsobení má dvě rozhraní API:

* Odeslat informace o __ vašich uživatelích a obsahu (akcích) __ pro přizpůsobení. Přizpůsobuje odezvu na horní akci.
* Pošlete nám svůj názor na přizpůsobené informace o tom, jak dobře se hodnocení vypracovalo jako číslo obvykle mezi 0 a 1 (předchozí oddíl uvádíme-1 a 1). 

![Základní sekvence událostí pro přizpůsobení](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>Další postup

* [Rychlé zprovoznění: Vytvoření smyčky zpětné vazby vC#](csharp-quickstart-commandline-feedback-loop.md)
* [Použití interaktivní ukázky](https://personalizationdemo.azurewebsites.net/)
