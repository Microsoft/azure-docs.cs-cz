---
title: Jak přizpůsobovat funkce přizpůsobování
description: _Smyčka_ přizpůsobování využívá Machine Learning k sestavení modelu, který předpovídá hlavní akci pro váš obsah. Model je vyškolen výhradně na vašich datech, která jste jim poslali pomocí volání pořadí a odměny.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: cfbe5cf8c19bfafb38f6149391e09350785ebf9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91303603"
---
# <a name="how-personalizer-works"></a>Jak služba Personalizace funguje

Prostředek pro přizpůsobování, vaše _výuková smyčka_, využívá Machine Learning k sestavení modelu, který předpovídá hlavní akci pro váš obsah. Model je vyškolen výhradně na vašich datech, která jste jim poslali pomocí volání **pořadí** a **odměny** . Každá smyčka je zcela nezávislá na sobě.

## <a name="rank-and-reward-apis-impact-the-model"></a>Rozhraní API pro pořadí a odměňování ovlivní model

Do rozhraní API pro řazení můžete odesílat _akce s funkcemi_ a _kontextovou funkcí_ . Rozhraní API **pořadí** se rozhodne použít buď:

* _Zneužití_: aktuální model pro určení nejlepší akce na základě minulých dat.
* _Prozkoumejte_: Vyberte jinou akci namísto horní akce. [Toto procento nakonfigurujete](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt) pro prostředek přizpůsobeného v Azure Portal.

Určíte skóre odměňování a odešlete toto skóre do API pro odměnu. API pro **odměnu** :

* Shromažďuje data pro výuku modelu pomocí zaznamenávání funkcí a odměňování skóre každého volání pořadí.
* Tato data používá k aktualizaci modelu na základě konfigurace zadané v _zásadách Učení_.

## <a name="your-system-calling-personalizer"></a>Vlastní přizpůsobování volání systému

Následující obrázek ukazuje architekturu pro volání pořadí a odměny:

![alternativní text](./media/how-personalizer-works/personalization-how-it-works.png "Jak funguje přizpůsobení")

1. Do rozhraní API pro řazení můžete odesílat _akce s funkcemi_ a _kontextovou funkcí_ .

    * Přizpůsobování určuje, jestli se má zneužít aktuální model, nebo prozkoumat nové volby modelu.
    * Výsledky hodnocení se odesílají do centra EventHub.
1. Nejvyšší pořadí se vrátí do vašeho systému jako _ID akce odměna_.
    Váš systém prezentuje obsah a určí skóre odměňování podle vašich vlastních obchodních pravidel.
1. Váš systém vrátí skóre odměňování do výukové smyčky.
    * Když přizpůsobování obdrží odměnu, bude se tato měna odesílat do centra EventHub.
    * Rozsah a měna jsou korelační.
    * Model AI se aktualizuje na základě výsledků korelace.
    * Odvozený modul se aktualizuje novým modelem.

## <a name="personalizer-retrains-your-model"></a>Přizpůsobení přeškolí váš model.

Přizpůsobuje váš model na základě nastavení **aktualizace četnosti modelu** na prostředku přizpůsobeného v Azure Portal.

Přizpůsobení používá všechna aktuálně zachovaná data na základě nastavení **uchovávání dat** v počtu dnů na prostředku přizpůsobeného v Azure Portal.

## <a name="research-behind-personalizer"></a>Výzkum za přizpůsobením

Přizpůsobování vychází z špičkové vědy a výzkumu v oblasti [posílení učení](concepts-reinforcement-learning.md) , včetně papírů, výzkumných aktivit a pokračujících oblastí průzkumu v Microsoft Research.

## <a name="next-steps"></a>Další kroky

Další informace o [hlavních scénářích](where-can-you-use-personalizer.md) pro přizpůsobování