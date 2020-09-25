---
title: Skóre záměna – Přizpůsobte si
description: Skóre odměňování indikuje, jak dobře vlastní individuální nastavení RewardActionID a způsobilo pro uživatele. Hodnota skóre odměňování je určena vaší obchodní logikou na základě pozorování chování uživatele. Přizpůsobené vlaky modely strojového učení vyhodnocením neprospěchu.
ms.service: cognitive-services
ms.subservice: personalizer
ms.date: 02/20/2020
ms.topic: conceptual
ms.openlocfilehash: 58ce4d7593b23807f4b31e3e71cbfdcd873b1fcc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91253492"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>Skóre odměňování indikuje úspěch přizpůsobení

Skóre odměňování indikuje, jak dobře vlastní individuální nastavení [RewardActionID](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/rank/rank#response)a způsobilo pro uživatele. Hodnota skóre odměňování je určena vaší obchodní logikou na základě pozorování chování uživatele.

Přizpůsobené vlaky modely strojového učení vyhodnocením neprospěchu.

Přečtěte si, [jak](how-to-settings.md#configure-rewards-for-the-feedback-loop) nakonfigurovat výchozí skóre pro odměnu v Azure Portal pro váš prostředek pro přizpůsobování.

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Použití API pro odměnu pro posílání skóre nedostatku do přizpůsobení

Do přizpůsobení přizpůsobeného pomocí API pro [odměnu](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/reward)se posílají ceny. Odměňování je obvykle číslo od 0 do 1. V některých scénářích může být záporná odměna s hodnotou-1 a měla by se používat jenom v případě, že máte zkušenosti s posílením učení (RL). Přizpůsobené vlaky je modelem, který dosáhne nejvyšší možné ceny v průběhu času.

Ceny jsou odesílány poté, co došlo k chování uživatele, což může být několik dní později. Maximální doba přizpůsobování času bude počkat, dokud se událost nepovažuje za neurčitou, nebo pokud se výchozí měna nakonfiguruje s [dobou čekání](#reward-wait-time) na Azure Portal.

Pokud se skóre pro událost nedostalo v **době čekání na odměnu**, použije se **Výchozí měna** . **[Výchozí odměna](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** je obvykle nastavena na hodnotu nula.


## <a name="behaviors-and-data-to-consider-for-rewards"></a>Chování a data, která je třeba zvážit při neprospěchu

Vezměte v úvahu tyto signály a chování pro kontext skóre odměňování:

* Přímý vstup uživatele pro návrhy, pokud se týkají možnosti (znamená to, že X?).
* Délka relace.
* Čas mezi relacemi.
* Analýza interakcí uživatele mínění.
* Přímé otázky a mini průzkumy, kde robot žádá uživatele o odezvu na užitečnost, přesnost.
* Reakce na výstrahy nebo zpoždění reakce na výstrahy.

## <a name="composing-reward-scores"></a>Vytváření skóre záměna

V obchodní logice musí být vypočítáno skóre odměňování. Skóre lze znázornit jako:

* Jedno odeslané číslo
* Skóre odesláno okamžitě (například 0,8) a další skóre bylo odesláno později (obvykle 0,2).

## <a name="default-rewards"></a>Výchozí ceny

Pokud v [době čekání na odměnu](#reward-wait-time)nedojde k žádné záměna, doba od volání metody Rank implicitně aplikuje **výchozí odměnu** na tuto událost pořadí.

## <a name="building-up-rewards-with-multiple-factors"></a>Sestavování na více faktorech

Pro efektivní přizpůsobení můžete vytvořit skóre pro odměnu na základě více faktorů.

Můžete například použít tato pravidla pro přizpůsobení seznamu obsahu videa:

|Chování uživatele|Hodnota částečného skóre|
|--|--|
|Uživatel kliknul na nejvyšší položku.|+ 0,5|
|Uživatel otevřel skutečný obsah této položky.|+ 0,3|
|Uživatel sledoval 5 minut obsahu nebo 30%, podle toho, co je delší.|+ 0,2|
|||

Pak můžete k rozhraní API odeslat celkovou odměnu.

## <a name="calling-the-reward-api-multiple-times"></a>Vícenásobné volání rozhraní API pro odměnu

Můžete také volat API pro odměnu pomocí stejného ID události, která posílá různé výsledky odměňování. Když aplikace přizpůsobuje tyto odměny, určí konečnou odměnu této události jejich agregací, jak je uvedeno v konfiguraci přizpůsobení.

Hodnoty agregace:

*  **First**: vybere pro událost první obdržené skóre odměňování a zbytek se zahodí.
* **Sum**: vezme všechny skóre pro celou měnu shromážděné pro ID události a přidá je dohromady.

Všechny odměny za události, které jsou obdrženy po **dobu čekání na odměnu**, se zahodí a neovlivňují školení modelů.

Pokud přidáte skóre pro každou měnu, vaše konečná odměna může být mimo očekávaný rozsah skóre. Tím nedojde k selhání služby.

## <a name="best-practices-for-calculating-reward-score"></a>Osvědčené postupy pro výpočet skóre odměňování

* **Vezměte v úvahu pravdivé indikátory úspěšného přizpůsobení**: snadno se můžete domnívat, že se jedná o kliknutí, ale dobrá odměna je založená na tom, co chcete, aby uživatelé *měli místo toho* , co chcete, aby mohli *dělat*.  Například odměňování za kliknutí může vést k výběru obsahu, který je clickbait náchylnější.

* **Použití skóre odměňování pro to, jak dobrým způsobem přizpůsobení fungovalo**: přizpůsobením návrhu filmu by se snado, že uživatel sleduje film a dává jim vysoké hodnocení. Vzhledem k tomu, že hodnocení filmu pravděpodobně závisí na mnoha věcech (kvalita jednání, nálada uživatele), není dobrým signálem pro to, jak dobře *přizpůsobení* fungovalo. Uživatel sleduje několik prvních minut filmu, ale může to být lepší signál účinnosti přizpůsobení a odeslání odměna 1 až 5 minut bude lepším signálem.

* **Ceny se vztahují jenom na RewardActionID**: přizpůsobování platí pro pochopení účinnosti akce zadané v RewardActionID. Pokud se rozhodnete zobrazit další akce a uživatel na ně klikne, odměna by měla být nulová.

* **Zvažte nezamýšlené důsledky**: Vytvořte funkce odměňování, které vedou k odpovědným výsledkům s [etickým a odpovědným používáním](ethics-responsible-use.md).

* **Použití přírůstků**: Přidání částečných odčítání pro menší uživatelské chování pomáhá přizpůsobovat, aby se zajistilo lepší ceny. Tato přírůstková odměna umožňuje algoritmu poznat, že je přiblížný k uživateli v konečném požadovaném chování.
    * Pokud se zobrazuje seznam filmů, pokud uživatel po chvíli najede myší na jednu z nich, můžete určit, že došlo k nějakému uživateli – zapojení. Chování se může počítat s skóre pro odměnu 0,1.
    * Pokud uživatel stránku otevřel a pak skončil, může být skóre odměňování 0,2.

## <a name="reward-wait-time"></a>Doba čekání na odměnu

Přizpůsobování bude korelovat informace o volání pořadí s neprospěchem v souvislosti s voláními na základě počtu volání ke výukě modelu. Můžou se nacházet v různou dobu. Přizpůsobování čeká na omezený čas, počínaje okamžikem, kdy došlo k volání pořadí, i v případě, že bylo volání řazení provedeno jako neaktivní událost a aktivováno později.

Pokud **Doba čekání na odměnu** vyprší a neexistují žádné informace o záplatcích, použije se pro tuto událost výchozí odměna za školení. Maximální doba čekání je 6 dnů.

## <a name="best-practices-for-reward-wait-time"></a>Osvědčené postupy pro dobu čekání na odměnu

Pro lepší výsledky použijte tato doporučení.

* Nechte si volnou dobu čekání, jak je to možné, a zároveň nechat dostatek času na získání zpětné vazby od uživatele.

* Nevybírejte dobu trvání, která je kratší než čas potřebný k získání zpětné vazby. Například pokud se některé z vašich vydaných vašich vydaných z vašich vydaných uživatelů dokončí po zobrazení 1 minuty videa, délka experimentu by měla být aspoň dvojitá.

## <a name="next-steps"></a>Další kroky

* [Zpětnovazební učení](concepts-reinforcement-learning.md)
* [Vyzkoušejte rozhraní API pro řazení](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Vyzkoušejte API pro odměnu](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)
