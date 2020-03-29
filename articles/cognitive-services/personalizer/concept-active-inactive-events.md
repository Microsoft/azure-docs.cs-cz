---
title: Aktivní a neaktivní události - Personalizátor
description: Tento článek popisuje použití aktivních a neaktivních událostí v rámci služby Personalizace.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: a8f27542208965e2b820b9fc45cfcc5353a7f193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624250"
---
# <a name="active-and-inactive-events"></a>Aktivní a neaktivní události

**Aktivní** událostí je jakékoli volání do hodnosti, kde víte, že ukážete výsledek zákazníkovi a určíte skóre odměny. Toto je výchozí chování.

**Neaktivní** událost je volání pořadí, kde si nejste jisti, zda uživatel někdy uvidí doporučenou akci, z důvodu obchodní logiky. To vám umožní zahodit událost, takže Personalizátor není vyškolen s výchozí odměnou. Neaktivní události by neměly volat rozhraní API pro odměňování.

Je důležité, aby učení smyčky znát skutečný typ události. Neaktivní událost nebude mít hovor o odměně. Aktivní událost by měla mít volání o odměnu, ale pokud není nikdy uskutečněno volání rozhraní API, použije se výchozí skóre odměny. Změňte stav události z neaktivní na aktivní, jakmile zjistíte, že bude mít vliv na uživatelské prostředí.

## <a name="typical-active-events-scenario"></a>Scénář typických aktivních událostí

Když vaše aplikace volá rozhraní API pořadí, obdržíte akci, kterou by měla aplikace zobrazit v poli **rewardActionId.**  Od tohoto okamžiku personalista očekává volání odměny se skóre odměny, které má stejné id události. Skóre odměny se používá k trénování modelu pro budoucí volání hodnosti. Pokud není přijato žádné volání o odměnu pro id události, použije se výchozí odměna. [Výchozí odměny](how-to-settings.md#configure-rewards-for-the-feedback-loop) se najdou u prostředků personalisty na webu Azure Portal.

## <a name="other-event-type-scenarios"></a>Jiné scénáře typu události

V některých případech aplikace může být nutné volat Rank ještě před ví, pokud výsledek bude použit nebo zobrazen uživateli. K tomu může dojít v situacích, kdy je například vykreslování propagovaného obsahu přepsáno marketingovou kampaní. Pokud nebyl výsledek volání hodnosti nikdy použit a uživatel jej nikdy neviděl, neposílejte odpovídající bonusový hovor.

Obvykle k těmto scénářům dochází, když:

* Jste prerendering uživatelskérozhraní, které uživatel může nebo nemusí dostat vidět.
* Vaše aplikace provádí prediktivní přizpůsobení, ve kterém rank volání jsou prováděny s malým kontextem v reálném čase a aplikace může nebo nemusí používat výstup.

V těchto případech použijte Personalizace k volání pořadí a požádejte událost, aby byla _neaktivní_. Personalizák nebude očekávat odměnu za tuto událost a nebude uplatňovat výchozí odměnu.

Později v obchodní logiky, pokud aplikace používá informace z Rank volání, stačí _aktivovat_ událost. Jakmile je událost aktivní, personalizátka očekává odměnu za událost. Pokud není provedeno žádné explicitní volání do rozhraní Reward API, personalizátor použije výchozí odměnu.

## <a name="inactive-events"></a>Neaktivní události

Chcete-li zakázat školení pro `learningEnabled = False`událost, volejte Rank pomocí .

Pro neaktivní událost učení je implicitně aktivován, pokud odešlete odměnu za eventId nebo volání `activate` rozhraní API pro tuto eventId.

## <a name="next-steps"></a>Další kroky

* Přečtěte [si, jak určit skóre odměny a jaká data je třeba vzít v úvahu](concept-rewards.md).
