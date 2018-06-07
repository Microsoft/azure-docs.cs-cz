---
title: Pochopení správy verzí šablony zařízení pro vaše aplikace Azure IoT centrální | Microsoft Docs
description: Iterace v šablony zařízení tak, že vytvoříte nové verze a bez dopadu na připojené zařízení za provozu
author: sandeeppujar
ms.author: sandeepu
ms.date: 01/19/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: b125d822596675b138560c14c76f9a3120ce3424
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628823"
---
# <a name="create-a-new-device-template-version"></a>Vytvořit novou verzi šablony zařízení

Microsoft Azure IoT centrální umožňuje rychlý vývoj aplikace či aplikace IoT. Návrhy vaše zařízení šablony můžete rychle iterovat přidání, úpravy nebo odstranění měření, nastavení nebo vlastnosti. Některé z těchto změn může být nežádoucí pro aktuálně připojené zařízení. Azure IoT střed identifikuje tyto nejnovější změny a poskytuje způsob, jak bezpečně nasadit tyto aktualizace do zařízení.

Šablona zařízení má číslo verze při jeho vytvoření. Ve výchozím nastavení je číslo verze 1.0.0. Pokud upravujete šablonu zařízení, a pokud tato změna by mohlo mít vliv za provozu připojená zařízení, Azure IoT centrální vyzve, abyste vytvořili novou verzi šablony zařízení.

> [!NOTE]
> Další informace o tom, jak vytvořit šablonu zařízení najdete v části [nastavit šablonu zařízení](howto-set-up-template.md)

## <a name="changes-that-prompt-a-version-change"></a>Změny, které výzva ke změně verze

Obecné změny nastavení nebo vlastnosti šablony zařízení výzva ke změně verze.

> [!NOTE]
> Změny provedené v šabloně zařízení nechcete zobrazovat výzvu k vytvoření nové verze, pokud žádné zařízení nebo je připojen na většinu jedno zařízení.

Následující seznam popisuje uživatelské akce, které mohou vyžadovat novou verzi:

* Vlastnosti (povinné)
    * Přidání nebo odstranění požadovaná vlastnost
    * Změna názvu pole vlastnosti, název pole, které se vaše zařízení používá k odeslání zprávy.
*  Vlastnosti (volitelné)
    * Odstraňování volitelná vlastnost
    * Změna názvu pole vlastnosti, název pole, které se vaše zařízení používá k odeslání zprávy.
    * Změna volitelná vlastnost na požadovanou vlastnost
*  Nastavení
    * Přidání nebo odstranění nastavení
    * Změna názvu pole nastavení, název pole, který je používán zařízení odesílat a přijímat zprávy.

## <a name="what-happens-on-version-change"></a>Co se stane při změně verze?

Co se stane pravidla a řídicí panely zařízení, když dojde ke změně verze?

**Pravidla** může obsahovat podmínky, které jsou závislé na vlastnosti. Pokud jste odebrali nejméně jeden z těchto vlastností, může být tato pravidla přerušený v nové verzi šablony zařízení. Můžete přejít na tato konkrétní pravidla a aktualizovat podmínky opravit pravidla. Pravidla pro předchozí verze by měla spolupracovat s žádný vliv.

**Řídicí panely zařízení** může obsahovat několik typů dlaždic. Některé dlaždice může obsahovat nastavení a vlastností. Pokud je odebrán vlastnosti nebo nastavení používané v dlaždici, dlaždice je úplně nebo částečně přerušená. Můžete přejít na dlaždici a vyřešte problém buď odebráním dlaždice nebo aktualizuje obsah dlaždice.

## <a name="migrate-a-device-across-device-template-versions"></a>Migrace mezi verzemi šablony zařízení zařízení

Můžete vytvořit několik verzí šablony zařízení. V čase budete mít více připojených zařízení pomocí těchto šablon zařízení. Zařízení můžete migrovat z jedné verze šablony zařízení do jiné. Následující kroky popisují, jak migrovat zařízení:

1. Přejděte na **Explorer** stránky.
1. Vyberte zařízení, které budete muset migrovat na jinou verzi.
1. Zvolte **migrovat zařízení**.
1. Vyberte číslo verze, kterou chcete migrovat na zařízení a zvolte **migrací**.

![Postup migrace zařízení](media\howto-version-devicetemplate\pick-version.png)

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili použití verzí šablony zařízení v aplikaci Azure IoT centrální, zde je navržené další krok:

> [!div class="nextstepaction"]
> [Postup vytvoření pravidla telemetrie](howto-create-telemetry-rules.md)