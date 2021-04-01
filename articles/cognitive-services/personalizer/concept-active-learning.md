---
title: Zásady učení – přizpůsobování
description: Nastavení učení určuje základní *parametry* školení modelů. Dva modely stejných dat, která jsou učená v různých nastaveních výuky, se liší.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1d2038e3796df843736eb80f7e5645f4141c82f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "91253628"
---
# <a name="learning-policy-and-settings"></a>Zásady učení a nastavení

Nastavení učení určuje základní *parametry* školení modelů. Dva modely stejných dat, která jsou učená v různých nastaveních výuky, se liší.

[Zásady učení a nastavení](how-to-settings.md#configure-rewards-for-the-feedback-loop) se nastavují na prostředku přizpůsobeného v Azure Portal.

## <a name="import-and-export-learning-policies"></a>Zásady učení pro import a export

Soubory zásad učení můžete importovat a exportovat z Azure Portal. Tuto metodu použijte, chcete-li uložit existující zásady, otestovat je, nahradit je a archivovat je v rámci správy zdrojového kódu jako artefakty pro budoucí referenci a audit.

Přečtěte si, [jak](how-to-manage-model.md#import-a-new-learning-policy) naimportovat a exportovat zásady učení v Azure Portal pro váš prostředek pro přizpůsobení.

## <a name="understand-learning-policy-settings"></a>Principy nastavení zásad učení

Nastavení v zásadách učení není určené ke změně. Nastavení změňte pouze v případě, že rozumíte tomu, jak ovlivňují přizpůsobování. Bez tohoto vědomí byste mohli způsobovat problémy, včetně neověřování modelů přizpůsobeného pro přizpůsobování.

Přizpůsobení používá [vowpalwabbit](https://github.com/VowpalWabbit) ke školení a hodnocení událostí. Informace o tom, jak upravit nastavení výuky pomocí vowpalwabbit, najdete v [dokumentaci k vowpalwabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) . Jakmile budete mít správné argumenty příkazového řádku, uložte příkaz do souboru s následujícím formátem (nahraďte hodnotu vlastnosti argumenty požadovaným příkazem) a nahrajte soubor pro import nastavení výuky v podokně **Nastavení modelu a učení** v Azure Portal pro váš prostředek pro přizpůsobování.

Následuje `.json` příklad zásad učení.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>Porovnání zásad učení

Můžete porovnat, jak různé zásady učení provádějí data z minulých protokolů v protokolech přizpůsobených pomocí [offline vyhodnocení](concepts-offline-evaluation.md).

[Nahrajte vlastní zásady učení](how-to-manage-model.md) a porovnejte je s aktuálními zásadami učení.

## <a name="optimize-learning-policies"></a>Optimalizace výukových zásad

Přizpůsobený objekt může vytvořit optimalizované vzdělávací zásady při [offline testování](how-to-offline-evaluation.md). Optimalizované vzdělávací zásady, které mají lepší přínos při vyhodnocování offline, budou poskytovat lepší výsledky při použití online v přizpůsobeném nástroji.

Když optimalizujete zásady učení, můžete ji použít přímo na přizpůsobení tak, aby okamžitě nahradila aktuální zásady. Nebo můžete optimalizovanou zásadu Uložit pro další vyhodnocení a později se rozhodnout, jestli ji zahodíte, uložíte nebo použijete.

## <a name="next-steps"></a>Další kroky

* Projděte si [aktivní a neaktivní události](concept-active-inactive-events.md).
