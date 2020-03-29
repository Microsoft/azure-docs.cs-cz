---
title: Jak personalizace funguje - Personalizace
description: Smyčka _Personalizátor_ používá strojové učení k vytvoření modelu, který předpovídá nejvyšší akci pro váš obsah. Model je trénován výhradně na vašich datech, které jste mu poslali pomocí volání Hodnost a odměny.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 836c207213ac52a60e27da6fc957418187059023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623755"
---
# <a name="how-personalizer-works"></a>Jak služba Personalizace funguje

Personalizátor zdroj, _vaše učení smyčky_, používá strojové učení k vytvoření modelu, který předpovídá nejlepší akce pro váš obsah. Model je trénován výhradně na vašich datech, které jste mu poslali pomocí volání **Hodnost** a **odměny.** Každá smyčka je zcela nezávislá na sobě.

## <a name="rank-and-reward-apis-impact-the-model"></a>Api pro hodnocení a odměny ovlivňují model

Do rozhraní API hodnocení odesíláte _akce s funkcemi_ a _kontextovými funkcemi._ **Rozhraní RANK** API se rozhodne použít buď:

* _Exploit_: Aktuální model pro rozhodnutí o nejlepší akci na základě minulých dat.
* _Prozkoumat_: Vyberte jinou akci místo horní akce. [Toto procento nakonfigurujete](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt) pro prostředek personalisty na webu Azure Portal.

Určíte skóre odměny a odešlete toto skóre do rozhraní API pro odměny. Bonus **API:**

* Shromažďuje data pro trénování modelu zaznamenáváním funkcí a odměňují skóre každého volání hodnosti.
* Tato data použije k aktualizaci modelu na základě konfigurace zadané v _zásadách učení_.

## <a name="your-system-calling-personalizer"></a>Váš systém volá Personalikátor

Následující obrázek znázorňuje architektonický tok volání rank a odměny:

![alternativní text](./media/how-personalizer-works/personalization-how-it-works.png "Jak individuální nastavení funguje")

1. Do rozhraní API hodnocení odesíláte _akce s funkcemi_ a _kontextovými funkcemi._

    * Personalizátor rozhodne, zda chcete zneužít aktuální model nebo prozkoumat nové možnosti pro model.
    * Výsledek hodnocení je odeslán na EventHub.
1. Nejvyšší hodnost se vrací do vašeho systému jako _ID akce odměny_.
    Váš systém prezentuje tento obsah a určuje skóre odměny na základě vašich vlastních obchodních pravidel.
1. Váš systém vrátí skóre odměny do výukové smyčky.
    * Když personalizátor obdrží odměnu, odměna se odešle na EventHub.
    * Hodnost a odměna jsou v korelaci.
    * Model AI je aktualizován na základě výsledků korelace.
    * Modul odvození je aktualizován s novým modelem.

## <a name="personalizer-retrains-your-model"></a>Personalizace retrains váš model

Personalizátor retrains váš model na základě **nastavení aktualizace frekvence modelu** na prostředek personalikátor na webu Azure portal.

Personalizár používá všechna data, která jsou aktuálně uchována, na základě nastavení **uchovávání dat** v počtu dní na vašem prostředku personalisty na webu Azure Portal.

## <a name="research-behind-personalizer"></a>Výzkum personalisty

Personalizák je založen na špičkové vědě a výzkumu v oblasti [posilování učení,](concepts-reinforcement-learning.md) včetně dokumentů, výzkumných aktivit a probíhajících oblastí průzkumu v microsoft research.

## <a name="next-steps"></a>Další kroky

Informace o [nejlepších scénářích](where-can-you-use-personalizer.md) pro personalizaci