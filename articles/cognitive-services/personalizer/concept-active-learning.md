---
title: Zásady učení - Personalizátor
description: Nastavení učení určují *hyperparametry* trénování modelu. Dva modely stejných dat, které jsou trénované na různých nastavení učení skončí jinak.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: abe6a2a2ec9b9978230d894c69193469f6e932e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219343"
---
# <a name="learning-policy-and-settings"></a>Zásady a nastavení učení

Nastavení učení určují *hyperparametry* trénování modelu. Dva modely stejných dat, které jsou trénované na různých nastavení učení skončí jinak.

[Zásady a nastavení výuky](how-to-settings.md#configure-rewards-for-the-feedback-loop) se najdou na vašem prostředku personalisty na webu Azure Portal.

## <a name="import-and-export-learning-policies"></a>Zásady importu a exportu učení

Soubory zásad učení můžete importovat a exportovat z portálu Azure. Pomocí této metody můžete uložit existující zásady, otestovat je, nahradit je a archivovat je do správy zdrojového kódu jako artefakty pro budoucí použití a audit.

Přečtěte [si, jak importovat](how-to-manage-model.md#import-a-new-learning-policy) a exportovat zásady učení na webu Azure Portal pro váš prostředek personalikátoru.

## <a name="understand-learning-policy-settings"></a>Principy nastavení zásad učení

Nastavení v zásadách učení nejsou určeny ke změně. Nastavení změňte pouze v případě, že rozumíte tomu, jak ovlivňují personalizátor. Bez těchto znalostí, můžete způsobit problémy, včetně zrušení přizpůsobení modely.

Personalizátář používá [vowpalwabbit](https://github.com/VowpalWabbit) trénovat a skóre události. Podívejte se do [dokumentace vowpalwabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) o tom, jak upravit nastavení učení pomocí vowpalwabbit. Jakmile budete mít správné argumenty příkazového řádku, uložte příkaz do souboru s následujícím formátem (nahraďte hodnotu vlastnosti argumentů požadovaným příkazem) a nahrajte soubor k importu nastavení učení v podokně **Model a nastavení učení** na portálu Azure pro váš prostředek personalizátoru.

Následuje `.json` příklad zásady učení.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>Porovnání zásad učení

Můžete porovnat, jak různé zásady učení provádět proti minulých dat v personalizátorprotokoly pomocí [offline hodnocení](concepts-offline-evaluation.md).

[Nahrajte vlastní zásady učení](how-to-manage-model.md) a porovnejte je se současnými zásadami učení.

## <a name="optimize-learning-policies"></a>Optimalizace zásad učení

Personalizár může vytvořit optimalizované zásady učení v [offline hodnocení](how-to-offline-evaluation.md). Optimalizovaná politika učení, která má lepší odměny v offline hodnocení, přinese lepší výsledky, když se používá online v Personalizár.

Po optimalizaci zásad učení, můžete použít přímo na personalista tak, aby okamžitě nahradí aktuální zásady. Nebo můžete uložit optimalizované zásady pro další vyhodnocení a později se rozhodnout, zda ji chcete zahodit, uložit nebo použít.

## <a name="next-steps"></a>Další kroky

* Naučte [se aktivní a neaktivní události](concept-active-inactive-events.md).
