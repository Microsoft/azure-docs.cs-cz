---
title: Principy správy zařízení šablony verzí pro aplikace Azure IoT Central | Dokumentace Microsoftu
description: Iterovat své šablony zařízení tak, že vytvoříte nové verze a bez dopadu na vaše živé připojená zařízení
author: sandeeppujar
ms.author: sandeepu
ms.date: 01/19/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 3b9e6a59b44db9295d86e3bc8a8dda9ec9761f38
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51009204"
---
# <a name="create-a-new-device-template-version"></a>Vytvoření nové šablony verze zařízení

Azure IoT Central umožňuje rychlý vývoj aplikací IoT. Vaše návrhy zařízení šablony můžete rychle iterovat přidání, úpravy nebo odstranění měření, nastavení nebo vlastnosti. Některé z těchto změn může být rušivá pro aktuálně připojená zařízení. Azure IoT Central identifikuje tyto nejnovější změny a poskytuje způsob, jak bezpečně tyto aktualizace nasadit do zařízení.

Šablona zařízení má číslo verze po jeho vytvoření. Ve výchozím nastavení je číslo verze 1.0.0. Při úpravě šablony zařízení, a pokud se tato změna může mít vliv na live připojených zařízení, Azure IoT Central vás vyzve k vytvoření nové šablony verze zařízení.

> [!NOTE]
> Další informace o tom, jak vytvořit šablonu zařízení najdete v článku [nastavit šablonu zařízení](howto-set-up-template.md)

## <a name="changes-that-prompt-a-version-change"></a>Změny, které mění číslo verze řádku

Obecné změny nastavení nebo vlastností šablony zařízení výzvu mění číslo verze.

> [!NOTE]
> Změny provedené v šabloně zařízení nezobrazovat výzvu k vytvoření nové verze, pokud žádné zařízení nebo připojené na většinu jedno zařízení.

Následující seznam popisuje uživatelské akce, které by mohly vyžadovat nová verze:

* Vlastnosti (povinné)
    * Přidání nebo odstranění požadovanou vlastnost
    * Změna pole názvu vlastnosti, název pole, které zařízení používají k odesílání zpráv.
*  Vlastnosti (volitelné)
    * Odstraňuje se volitelná vlastnost
    * Změna pole názvu vlastnosti, název pole, které zařízení používají k odesílání zpráv.
    * Změna volitelné vlastnosti pro požadovanou vlastnost
*  Nastavení
    * Přidání nebo odstranění nastavení
    * Změna pole názvu nastavení, název pole, které vaše zařízení používá k odesílání a příjem zpráv.

## <a name="what-happens-on-version-change"></a>Co se stane při změně verze?

Co se stane k pravidlům a řídicí panely zařízení, když dojde ke změně verze?

**Pravidla** by mohla obsahovat podmínky, které jsou závislé na vlastnosti. Pokud jste odebrali jeden nebo více z těchto vlastností, tato pravidla může nebudou fungovat ve vaší nové šablony verzi zařízení. Můžete přejít na tato konkrétní pravidla a aktualizovat podmínky, které mají stanovit pravidla. Pravidla pro předchozí verze by měla fungovat bez jakéhokoli dopadu.

**Řídicí panely zařízení** může obsahovat několik typů dlaždic. Některé dlaždice může obsahovat nastavení a vlastnosti. Při odebrání vlastnosti nebo nastavení použitá dlaždici, dlaždice se úplně nebo částečně přeruší. Můžete přejít na dlaždici a tento problém vyřešit, buď odebráním dlaždice nebo aktualizuje obsah na dlaždici.

## <a name="migrate-a-device-across-device-template-versions"></a>Migrovat zařízení mezi verzemi šablony zařízení

Můžete vytvořit více verzí šablony zařízení. V průběhu času budou mít více připojenými zařízeními pomocí těchto šablon zařízení. Zařízení z jedné verze šablony zařízení můžete migrovat do jiného. Následující kroky popisují, jak migrovat zařízení:

1. Přejděte **Explorer** stránky.
1. Vyberte zařízení, které je potřeba migrovat na jinou verzi.
1. Zvolte **migrovat zařízení**.
1. Vyberte číslo verze, kterou chcete migrovat zařízení a zvolte **migrace**.

![Jak migrovat zařízení](media\howto-version-devicetemplate\pick-version.png)

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak používat šablony verze zařízení v aplikaci Azure IoT Central, tady je navrhované další krok:

> [!div class="nextstepaction"]
> [Vytvoření pravidla telemetrie](howto-create-telemetry-rules.md)