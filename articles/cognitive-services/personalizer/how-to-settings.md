---
title: Konfigurace služby Personalizace
description: Konfigurace služby zahrnuje, jak služba zachází s odměnami, jak často služba zkoumá, jak často je model přeškolen a kolik dat je uloženo.
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: ac31a9f907defeb44dbd4748a4395d3aec34d30c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219354"
---
# <a name="configure-personalizer-learning-loop"></a>Konfigurace výukové smyčky personalizátorů

Konfigurace služby zahrnuje, jak služba zachází s odměnami, jak často služba zkoumá, jak často je model přeškolen a kolik dat je uloženo.

Nakonfigurujte výukové smyčky na stránce **Konfigurace** na portálu Azure pro tento prostředek personalikátor.

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="configure-rewards-for-the-feedback-loop"></a>Konfigurace odměn pro zpětnou vazbu

Nakonfigurujte službu pro využití odměn ve vzdělávací smyčce. Změny následujících hodnot obnoví aktuální model personalisty a přeškolí jej pomocí dat za poslední dva dny.

> [!div class="mx-imgBorder"]
> ![Konfigurace hodnot odměn pro zpětnou vazbu](media/settings/configure-model-reward-settings.png)

|Hodnota|Účel|
|--|--|
|Doba čekání na odměnu|Nastavuje dobu, po kterou bude personalizátor shromažďovat hodnoty odměn za volání hodnosti, a to od okamžiku, kdy dojde k volání hodnosti. Tato hodnota je nastavena dotazem: "Jak dlouho by měl personalista čekat na volání odměn?" Každá odměna, která dorazí po tomto okně, bude zaznamenána, ale nebude použita k učení.|
|Výchozí odměna|Pokud personalista během okna Čekací doby odměny přidruženého k hovoru hodnosti nepřijme žádný bonusový hovor, přiřadí mu výchozí odměnu. Ve výchozím nastavení a ve většině scénářů je výchozí odměna nulová (0).|
|Agregace odměn|Pokud je pro stejné volání rozhraní API hodnocení přijato více odměn, použije se tato metoda agregace: **sum** or **earliest**. Nejdříve vybere nejbližší přijaté skóre a zbytek zahodí. To je užitečné, pokud chcete jedinečnou odměnu mezi případně duplicitní hovory. |

Po změně těchto hodnot nezapomeňte vybrat **uložit**.

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>Konfigurace průzkumu tak, aby se učení mohlo přizpůsobit

Personalizace je schopen objevovat nové vzory a přizpůsobit se změnám chování uživatelů v průběhu času zkoumáním alternativy namísto použití trénovaného modelu předpověď. Hodnota **Průzkum** určuje, jaké procento volání hodnosti je zodpovězeno průzkumem.

Změny této hodnoty obnoví aktuální model personalisty a přeškolí jej pomocí dat za poslední 2 dny.

![Hodnota průzkumu určuje, jaké procento rank volání jsou zodpovězeny s průzkumem](media/settings/configure-exploration-setting.png)

Po změně této hodnoty nezapomeňte vybrat **uložit**.

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>Konfigurace četnosti aktualizací modelu pro trénování modelu

**Frekvence aktualizace modelu** určuje, jak často je model trénovaný.

|Nastavení frekvence|Účel|
|--|--|
|1 minuta|Frekvence aktualizace v jedné minutě jsou užitečné při **ladění** kódu aplikace pomocí personalikátu, provádění ukázek nebo interaktivního testování aspektů strojového učení.|
|15 minut|Vysoké frekvence aktualizace modelu jsou užitečné pro situace, kdy chcete **pozorně sledovat změny** v chování uživatelů. Mezi příklady patří weby, které běží na živé zprávy, virální obsah, nebo živé nabídky produktů. V těchto scénářích můžete použít 15minutovou frekvenci. |
|1 hodina|U většiny případů použití je efektivní nižší četnost aktualizací.|

![Frekvence aktualizace modelu určuje, jak často je nový model personalisty přetrénovaný.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Po změně této hodnoty nezapomeňte vybrat **uložit**.

## <a name="data-retention"></a>Uchovávání dat

**Doba uchovávání dat** určuje, kolik dní personalizace uchovává datové protokoly. Minulé datové protokoly jsou nutné k provádění [offline hodnocení](concepts-offline-evaluation.md), které se používají k měření účinnosti personalista a optimalizaci zásad učení.

Po změně této hodnoty nezapomeňte vybrat **uložit**.

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>Nastavení, která zahrnují obnovení modelu

Následující akce zahrnují okamžité přeškolení modelu s daty za poslední 2 dny.

* Odměna
* Průzkum

Chcete-li [vymazat](how-to-manage-model.md) všechna data, použijte stránku **Model a nastavení učení **.

## <a name="next-steps"></a>Další kroky

[Přečtěte si, jak spravovat model](how-to-manage-model.md)
