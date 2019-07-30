---
title: Active Learning – Přizpůsobte si
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: 8c1579be3d11ae14ca45ee861de2d4f705e5d62c
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663710"
---
# <a name="active-learning-and-learning-policies"></a>Zásady aktivního učení a učení 

Když vaše aplikace volá rozhraní API pro řazení, dostanete si rozměr obsahu. Obchodní logika může toto pořadí použít k určení, zda by měl být obsah zobrazen uživateli. Když zobrazíte seřazený obsah, jedná se o _aktivní_ událost pořadí. Když aplikace nezobrazuje daný obsah, jedná se o _neaktivní_ událost pořadí. 

K přizpůsobení se vrátí informace o události aktivního pořadí. Tyto informace se používají k pokračování v školení modelu prostřednictvím aktuálních zásad učení.

## <a name="active-events"></a>Aktivní události

Aktivní události by se měly vždy zobrazit uživateli a k uzavření výukové smyčky by mělo být vráceno volání odměny. 

### <a name="inactive-events"></a>Neaktivní události 

Neaktivní události by neměly měnit podkladový model, protože uživatel neměl možnost zvolit si z hodnoceného obsahu.

## <a name="dont-train-with-inactive-rank-events"></a>Nevytvářejte výuku s neaktivními událostmi Rank 

U některých aplikací možná budete muset volat rozhraní API řazení, aniž byste věděli, jestli aplikace zobrazí výsledky uživateli. 

K tomu dojde v těchto případech:

* Možná budete předem vykreslovat některé uživatelské rozhraní, které uživatel může nebo nemusí zobrazit. 
* Vaše aplikace může provádět prediktivní přizpůsobení, ve kterém se zadávají volání pořadí s méně kontextem v reálném čase a jejich výstup může nebo nemusí být aplikací používán. 

### <a name="disable-active-learning-for-inactive-rank-events-during-rank-call"></a>Zakázat aktivní učení pro události neaktivního pořadí během volání pořadí

Chcete-li zakázat automatické učení, zavolejte `learningEnabled = False`pořadí pomocí.

Pokud pro danou dobu odešlete nějakou odměnu, bude učení pro neaktivní událost implicitně aktivované.

## <a name="learning-policies"></a>Zásady učení

Zásady učení určují konkrétní *parametry* pro školení modelů. Dva modely stejných dat, které jsou vyškolené v různých zásadách učení, se budou chovat jinak.

### <a name="importing-and-exporting-learning-policies"></a>Import a export zásad učení

Soubory zásad učení můžete importovat a exportovat z Azure Portal. To vám umožňuje uložit existující zásady, otestovat je, nahradit je a archivovat je v rámci správy zdrojového kódu jako artefakty pro budoucí referenci a audit.

### <a name="learning-policy-settings"></a>Nastavení zásad učení

Nastavení v **zásadách Učení** není určeno ke změně. Nastavení můžete změnit jenom v případě, že rozumíte tomu, jak přizpůsobené přizpůsobování ovlivňují. Změna nastavení bez těchto znalostí způsobí vedlejší účinky, včetně neověřování modelů přizpůsobeného pro přizpůsobování.

### <a name="comparing-effectiveness-of-learning-policies"></a>Porovnání efektivity studijních zásad

Můžete porovnat, jak by se různé zásady učení prováděly proti minulým datům v protokolech pro přizpůsobování pomocí [offline vyhodnocení](concepts-offline-evaluation.md).

[Nahrajte vlastní vzdělávací zásady](how-to-offline-evaluation.md) pro porovnání s aktuálními zásadami učení.

### <a name="discovery-of-optimized-learning-policies"></a>Zjišťování zásad optimalizovaného učení

Přizpůsobení může při [vyhodnocování offline](how-to-offline-evaluation.md)vytvořit lépe optimalizované výukové zásady. Lépe optimalizované studijní zásady, které mají lepší přínos při offline testování, budou při použití online v přizpůsobeném procesu poskytovat lepší výsledky.

Po vytvoření optimalizovaných zásad učení ji můžete použít přímo na přizpůsobení, aby se okamžitě nahradila aktuální zásada, nebo ji můžete uložit pro další vyhodnocení a rozhodnout se, jestli ji později zrušíte, uložte nebo použijete.