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
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 90658e030c907a9fd99dd8fb9a6e90698d72b1f0
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834465"
---
# <a name="active-and-inactive-events"></a>Aktivní a neaktivní události

Když vaše aplikace volá rozhraní API pro řazení, dostanete akci, kterou by měla aplikace zobrazit v poli **rewardActionId** .  Od tohoto okamžiku přizpůsobené očekává volání odměna, které má stejné ID události. Skóre odměňování se použije ke školení modelu pro budoucí volání pořadí. Pokud se pro ID události nepřijme žádné volání, použije se výchozí měna. Výchozí ceny jsou nastaveny v Azure Portal.

V některých scénářích může aplikace před tím, než bude vědět, zda bude výsledek použit nebo zobrazen uživateli, volat funkci Rank. K tomu může dojít v situacích, kdy například vykreslování stránky propagovaného obsahu přepíše marketingová kampaň. Pokud výsledek volání řazení nebyl nikdy použit a uživatel ho nikdy neviděl, neodešlete odpovídající volání odměna.

K těmto scénářům obvykle dochází v následujících případech:

* Předvedete si předvykreslování uživatelského rozhraní, které uživatel může nebo nemusí zobrazit.
* Vaše aplikace provádí prediktivní přizpůsobení, ve kterém se provádí volání pořadí s malým kontextem v reálném čase a aplikace může nebo nemusí používat výstup.

V těchto případech použijte přizpůsobení pro volání pořadí, které vyžaduje, aby událost byla _neaktivní_. Přizpůsobování neočekává pro tuto událost odměňování a nepoužije výchozí odměnu.
Pokud aplikace používá informace z volání pořadí, použijte později v obchodní logice, stačí událost _aktivovat_ . Jakmile je událost aktivní, přizpůsobování očekává nějakou odměnu události. Pokud neprovede žádné explicitní volání rozhraní API pro odměnu, přizpůsobuje se výchozí odměna.

## <a name="inactive-events"></a>Neaktivní události

Chcete-li zakázat školení pro událost, zakažte pořadí volání pomocí `learningEnabled = False`. V případě neaktivní události se učení implicitně aktivuje, pokud odešlete nějakou příležitost pro ID události nebo zavoláte rozhraní API pro `activate` pro daný ID události.

## <a name="learning-settings"></a>Nastavení učení

Nastavení učení určuje základní *parametry* školení modelů. Dva modely stejných dat, která jsou učená v různých nastaveních výuky, se liší.

### <a name="import-and-export-learning-policies"></a>Zásady učení pro import a export

Soubory zásad učení můžete importovat a exportovat z Azure Portal. Tuto metodu použijte, chcete-li uložit existující zásady, otestovat je, nahradit je a archivovat je v rámci správy zdrojového kódu jako artefakty pro budoucí referenci a audit.

Naučte [se](how-to-learning-policy.md) importovat a exportovat zásady učení.

### <a name="understand-learning-policy-settings"></a>Principy nastavení zásad učení

Nastavení v zásadách učení není určené ke změně. Nastavení změňte pouze v případě, že rozumíte tomu, jak ovlivňují přizpůsobování. Bez tohoto vědomí byste mohli způsobovat problémy, včetně neověřování modelů přizpůsobeného pro přizpůsobování.

Přizpůsobení používá [vowpalwabbit](https://github.com/VowpalWabbit) ke školení a hodnocení událostí. Informace o tom, jak upravit nastavení výuky pomocí vowpalwabbit, najdete v [dokumentaci k vowpalwabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) . Jakmile budete mít správné argumenty příkazového řádku, uložte příkaz do souboru s následujícím formátem (nahraďte hodnotu vlastnosti argumenty požadovaným příkazem) a nahrajte soubor pro import nastavení výuky v podokně **Nastavení modelu a učení** v Azure Portal pro váš prostředek pro přizpůsobování.

Následující `.json` jsou příkladem zásad učení.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

### <a name="compare-learning-policies"></a>Porovnání zásad učení

Můžete porovnat, jak různé zásady učení provádějí data z minulých protokolů v protokolech přizpůsobených pomocí [offline vyhodnocení](concepts-offline-evaluation.md).

[Nahrajte vlastní zásady učení](how-to-learning-policy.md) a porovnejte je s aktuálními zásadami učení.

### <a name="optimize-learning-policies"></a>Optimalizace výukových zásad

Přizpůsobený objekt může vytvořit optimalizované vzdělávací zásady při [offline testování](how-to-offline-evaluation.md). Optimalizované vzdělávací zásady, které mají lepší přínos při vyhodnocování offline, budou poskytovat lepší výsledky při použití online v přizpůsobeném nástroji.

Když optimalizujete zásady učení, můžete ji použít přímo na přizpůsobení tak, aby okamžitě nahradila aktuální zásady. Nebo můžete optimalizovanou zásadu Uložit pro další vyhodnocení a později se rozhodnout, jestli ji zahodíte, uložíte nebo použijete.
