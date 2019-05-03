---
title: Co je Personalizer
titleSuffix: Azure Cognitive Services
description: Azure Personalizer je Cloudová služba rozhraní API, který umožňuje zvolit nejvhodnější zobrazíte na uživatele, učit se z jejich chování v reálném čase.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: c969029bcc0412267507efe81549ec6f8b2988ce
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027114"
---
# <a name="what-is-personalizer"></a>Co je Personalizer?

Azure Personalizer je Cloudová služba rozhraní API, který umožňuje zvolit nejvhodnější zobrazíte na uživatele, učit se z jejich chování v reálném čase.

* Zadání informací o uživatelích a obsahu a přijímat hlavní akce se zobrazí uživatelům. 
* Není nutné vyčistit a označovat data před použitím Personalizer.
* Je vhodné vám poskytnou Personalizer zpětnou vazbu. 
* Zobrazení analýzy v reálném čase. 
* Použijte Personalizer větší úsilí vědy data v rámci ověření existující experimentů.

## <a name="how-does-personalizer-work"></a>Jak funguje Personalizer?

Personalizer používá modely machine learningu Pokud chcete zjistit, jaká akce má být pořadí nejvyšší v kontextu. Obsahuje seznam možných akcí, s informacemi o nich; klientské aplikace a informace o kontextu, které mohou zahrnovat informace o uživateli, zařízení atd. Personalizer určuje akce má být provedena. Jakmile vaše klientská aplikace používá zvolené akci, poskytuje zpětnou vazbu k Personalizer ve formě reward skóre. Po dokončení smyčku zpětné vazby Personalizer automaticky aktualizuje svůj vlastní model použít pro budoucí rozměry.

## <a name="how-do-i-use-the-personalizer"></a>Použití Personalizer

![Pomocí Personalizer zvolit video, chcete-li zobrazit uživateli](media/what-is-personalizer/personalizer-example-highlevel.png)

1. Zvolte prostředí ve vaší aplikaci můžete přizpůsobit.
1. Vytvoření a konfigurace služby pro přizpůsobení na webu Azure Portal
1. Použití sady SDK pro volání Personalizer s informacemi (_funkce_) o vašich uživatelích a obsah (_akce_). Není nutné poskytovat čištění, před použitím Personalizer označené jako data. 
1. V klientské aplikaci zobrazí uživateli zvolila Personalizer akce.
1. Pomocí sady SDK pro poskytnutí zpětné vazby Personalizer označující, pokud uživatel vybral na Personalizer akce. Jedná se _oceňujte skóre_, obvykle mezi -1 a 1.
1. Zobrazit analytics na webu Azure Portal k vyhodnocení, jak systém funguje a jak pomáhá data individuálního nastavení.

## <a name="where-can-i-use-personalizer"></a>Kde můžu použít Personalizer?

Klientské aplikace můžete přidat například Personalizer na:

* Přizpůsobte, co článek je zvýrazněn na webu zpráv.    
* Optimalizujte umístění ad na webu.
* Zobrazte přizpůsobené "doporučenou položku" na nákupního webu.
* Navrhněte prvky uživatelského rozhraní, jako jsou filtry, které platí pro konkrétní fotografie.
* Zvolte chatovací robot odpovědi pro upřesnění záměru uživatele nebo v něm navrhnout akci.
* Prioritizujte návrhy, co uživatel by měl jako další krok v procesu podnikání dělat.

## <a name="personalization-for-developers"></a>Přizpůsobení pro vývojáře

Služba personalizer má dvě rozhraní API:

* Odeslat informace (_funkce_) o vašich uživatelích a obsah (_akce_) můžete přizpůsobit. Personalizer odpoví hlavní akce.
* Odeslat zpětnou vazbu o tom, jak dobře hodnocení pracovala jako číslo v rozsahu od 0 do 1 obvykle Personalizer (v předchozím oddílu říká, že hodnota -1 a 1). 

![Základní pořadí událostí pro přizpůsobení](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>Další postup

[Rychlé zprovoznění: Vytvořit smyčku zpětné vazby vC#](csharp-quickstart-commandline-feedback-loop.md)
