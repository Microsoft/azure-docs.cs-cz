---
title: Aktivní a neaktivní události – Přizpůsobte si
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
ms.openlocfilehash: aa6f53901f21dcb0726454d641a4a2a66007f9e0
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429051"
---
# <a name="active-and-inactive-events"></a>Aktivní a neaktivní události

Když vaše aplikace volá rozhraní API pro řazení, dostanete akci, kterou by měla aplikace zobrazit v poli rewardActionId.  Od tohoto okamžiku bude přizpůsobovat, že bude volání bez záměna u stejného ID události. Skóre odměňování se použije ke školení modelu, který se použije pro budoucí volání pořadí. Pokud se pro ID události žádné volání bez jakýchkoli odměňování, použije se defaul. Výchozí ceny jsou zřízené na webu Azure Portal.

V některých případech může být nutné, aby aplikace vyvolala rozsah hodnocení, i když bude výsledek použit nebo displayedn uživateli. K tomu může dojít v situacích, kdy se například stránka vykreslení propagovaného obsahu přepíše pomocí marketingové kampaně. Pokud se výsledek volání pořadí nikdy nepoužil a uživatel ho nikdy nezískal, je to nesprávný postup pro jeho vyučování bez jakýchkoli jakýchkoli odměna, nula nebo jinak.
K tomu obvykle dochází v těchto případech:

* Možná budete předem vykreslovat některé uživatelské rozhraní, které uživatel může nebo nemusí zobrazit. 
* Vaše aplikace může provádět prediktivní přizpůsobení, ve kterém se zadávají volání pořadí s méně kontextem v reálném čase a jejich výstup může nebo nemusí být aplikací používán. 

V těchto případech je vhodným způsobem použití přizpůsobování volání řazení, které vyžaduje, aby událost byla _neaktivní_. Přizpůsobování neočekává pro tuto událost žádnou odměnu a nepoužije výchozí odměnu. Letr v obchodní logice, pokud aplikace používá informace ze volání pořadí, stačí _aktivovat_ událost. Od okamžiku, kdy je událost aktivní, přizpůsobené aplikace pro událost očekává nebo použije výchozí odměnu, pokud není k dispozici explicitní volání rozhraní API pro odměnu.

## <a name="get-inactive-events"></a>Získat neaktivní události

Chcete-li zakázat školení pro událost, zavolejte pořadí s `learningEnabled = False`.

Výuka k neaktivní události se implicitně aktivuje, pokud odešlete nějakou odměnu pro ID události, nebo pro tento ID události zavoláte rozhraní API `activate`.

## <a name="learning-settings"></a>Nastavení učení

Nastavení učení určuje konkrétní *parametry* pro školení modelů. Dva modely stejných dat, které jsou vyškolené v různých nastaveních výuky, se budou lišit.

### <a name="import-and-export-learning-policies"></a>Zásady učení pro import a export

Soubory zásad učení můžete importovat a exportovat z Azure Portal. To vám umožňuje uložit existující zásady, otestovat je, nahradit je a archivovat je v rámci správy zdrojového kódu jako artefakty pro budoucí referenci a audit.

### <a name="learning-policy-settings"></a>Nastavení zásad učení

Nastavení v **zásadách Učení** není určeno ke změně. Nastavení můžete změnit jenom v případě, že rozumíte tomu, jak přizpůsobené přizpůsobování ovlivňují. Změna nastavení bez těchto znalostí způsobí vedlejší účinky, včetně neověřování modelů přizpůsobeného pro přizpůsobování.

### <a name="comparing-effectiveness-of-learning-policies"></a>Porovnání efektivity studijních zásad

Můžete porovnat, jak by se různé zásady učení prováděly proti minulým datům v protokolech pro přizpůsobování pomocí [offline vyhodnocení](concepts-offline-evaluation.md).

[Nahrajte vlastní vzdělávací zásady](how-to-offline-evaluation.md) pro porovnání s aktuálními zásadami učení.

### <a name="discovery-of-optimized-learning-policies"></a>Zjišťování zásad optimalizovaného učení

Přizpůsobení může při [vyhodnocování offline](how-to-offline-evaluation.md)vytvořit lépe optimalizované výukové zásady. Lépe optimalizované studijní zásady, které mají lepší přínos při offline testování, budou při použití online v přizpůsobeném procesu poskytovat lepší výsledky.

Po vytvoření optimalizovaných zásad učení ji můžete použít přímo na přizpůsobení, aby se okamžitě nahradila aktuální zásada, nebo ji můžete uložit pro další vyhodnocení a rozhodnout se, jestli ji později zrušíte, uložte nebo použijete.
