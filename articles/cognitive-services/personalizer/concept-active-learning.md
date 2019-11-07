---
title: Aktivní a neaktivní události – Přizpůsobte si
titleSuffix: Azure Cognitive Services
description: Tento článek popisuje použití aktivních a neaktivních událostí, nastavení učení a zásad učení v rámci služby přizpůsobeného nástroji.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: 1641a1020193395d7d2ddb9c4893bd7bc89cdcd0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681858"
---
# <a name="active-and-inactive-events"></a>Aktivní a neaktivní události

Když vaše aplikace volá rozhraní API pro řazení, dostanete akci, kterou by měla aplikace zobrazit v poli **rewardActionId** .  Od tohoto okamžiku přizpůsobené očekává volání odměna, které má stejné ID události. Skóre odměňování se použije ke školení modelu pro budoucí volání pořadí. Pokud se pro ID události nepřijme žádné volání, použije se výchozí měna. Výchozí ceny jsou nastaveny v Azure Portal.

V některých scénářích může aplikace před tím, než bude vědět, zda bude výsledek použit nebo zobrazen uživateli, volat funkci Rank. K tomu může dojít v situacích, kdy například vykreslování stránky propagovaného obsahu přepíše marketingová kampaň. Pokud výsledek volání řazení nebyl nikdy použit a uživatel ho nikdy neviděl, neodešlete odpovídající volání odměna.

K těmto scénářům obvykle dochází v následujících případech:

* Předvedete si předvykreslování uživatelského rozhraní, které uživatel může nebo nemusí zobrazit. 
* Vaše aplikace provádí prediktivní přizpůsobení, ve kterém se provádí volání pořadí s malým kontextem v reálném čase a aplikace může nebo nemusí používat výstup. 

V těchto případech použijte přizpůsobení pro volání pořadí, které vyžaduje, aby událost byla _neaktivní_. Přizpůsobování neočekává pro tuto událost odměňování a nepoužije výchozí odměnu. Pokud aplikace používá informace z volání pořadí, použijte později v obchodní logice, stačí událost _aktivovat_ . Jakmile je událost aktivní, přizpůsobování očekává nějakou odměnu události. Pokud neprovede žádné explicitní volání rozhraní API pro odměnu, přizpůsobuje se výchozí odměna.

## <a name="inactive-events"></a>Neaktivní události

Chcete-li zakázat školení pro událost, zakažte pořadí volání pomocí `learningEnabled = False`. V případě neaktivní události se učení implicitně aktivuje, pokud odešlete nějakou příležitost pro ID události nebo zavoláte rozhraní API pro `activate` pro daný ID události.

## <a name="learning-settings"></a>Nastavení učení

Nastavení učení určuje základní *parametry* školení modelů. Dva modely stejných dat, která jsou učená v různých nastaveních výuky, se liší.

### <a name="import-and-export-learning-policies"></a>Zásady učení pro import a export

Soubory zásad učení můžete importovat a exportovat z Azure Portal. Tuto metodu použijte, chcete-li uložit existující zásady, otestovat je, nahradit je a archivovat je v rámci správy zdrojového kódu jako artefakty pro budoucí referenci a audit.

### <a name="understand-learning-policy-settings"></a>Principy nastavení zásad učení

Nastavení v zásadách učení není určené ke změně. Nastavení změňte pouze v případě, že rozumíte tomu, jak ovlivňují přizpůsobování. Bez tohoto vědomí byste mohli způsobovat problémy, včetně neověřování modelů přizpůsobeného pro přizpůsobování.

### <a name="compare-learning-policies"></a>Porovnání zásad učení

Můžete porovnat, jak různé zásady učení provádějí data z minulých protokolů v protokolech přizpůsobených pomocí [offline vyhodnocení](concepts-offline-evaluation.md).

[Nahrajte vlastní zásady učení](how-to-offline-evaluation.md) a porovnejte je s aktuálními zásadami učení.

### <a name="optimize-learning-policies"></a>Optimalizace výukových zásad

Přizpůsobený objekt může vytvořit optimalizované vzdělávací zásady při [offline testování](how-to-offline-evaluation.md). Optimalizované vzdělávací zásady, které mají lepší přínos při vyhodnocování offline, budou poskytovat lepší výsledky při použití online v přizpůsobeném nástroji.

Když optimalizujete zásady učení, můžete ji použít přímo na přizpůsobení tak, aby okamžitě nahradila aktuální zásady. Nebo můžete optimalizovanou zásadu Uložit pro další vyhodnocení a později se rozhodnout, jestli ji zahodíte, uložíte nebo použijete.
