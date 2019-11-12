---
title: Správa verzí šablon zařízení pro Azure IoT Central aplikace | Microsoft Docs
description: Iterujte na šablonách zařízení vytvořením nových verzí a bez dopadu na živá zařízení připojená k síti.
author: sandeeppujar
ms.author: sandeepu
ms.date: 07/08/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: feaa8abcb6635573b3680b77befa5ccb462ec73a
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930129"
---
# <a name="create-a-new-device-template-version"></a>Vytvořit novou verzi šablony zařízení

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Azure IoT Central umožňuje rychlý vývoj aplikací IoT. Můžete rychle iterovat v návrzích šablon zařízení přidáním, úpravou nebo odstraněním měření, nastavení nebo vlastností. Některé z těchto změn můžou být pro aktuálně připojená zařízení rušivé. Azure IoT Central identifikuje tyto zásadní změny a poskytuje způsob, jak tyto aktualizace bezpečně nasadit do zařízení.

Šablona zařízení má číslo verze při jeho vytváření. Ve výchozím nastavení je číslo verze 1.0.0. Pokud upravíte šablonu zařízení a tato změna by mohla mít vliv na živá připojená zařízení, Azure IoT Central vás vyzve k vytvoření nové verze šablony zařízení.

> [!NOTE]
> Další informace o tom, jak vytvořit šablonu zařízení, najdete v tématu [nastavení šablony zařízení](howto-set-up-template.md) .

## <a name="changes-that-prompt-a-version-change"></a>Změny, které se dotazují na změnu verze

V části Obecné změny nastavení nebo vlastností šablony zařízení se zobrazí výzva ke změně verze.

> [!NOTE]
> Změny provedené v šabloně zařízení se nezobrazí při vytváření nové verze, když není připojeno žádné zařízení nebo zařízení na jednom.

Následující seznam popisuje akce uživatelů, které mohou vyžadovat novou verzi:

* Vlastnosti (povinné)
    * Přidání nebo odstranění požadované vlastnosti
    * Změna názvu pole vlastnosti, názvu pole, kterou vaše zařízení používá k odesílání zpráv.
*  Vlastnosti (volitelné)
    * Odstranění volitelné vlastnosti
    * Změna názvu pole vlastnosti, názvu pole, kterou vaše zařízení používá k odesílání zpráv.
    * Změna volitelné vlastnosti na požadovanou vlastnost
*  Nastavení
    * Přidání nebo odstranění nastavení
    * Změna názvu pole nastavení, názvu pole, které vaše zařízení používá k posílání a přijímání zpráv.

## <a name="what-happens-on-version-change"></a>Co se stane při změně verze?

Co se stane s pravidly a řídicími panely zařízení, když dojde ke změně verze?

**Pravidla** v předchozí verzi šablony zařízení nadále fungují beze změny. Pravidla se automaticky nemigrují na novou verzi šablony zařízení. Pravidla pro novou verzi šablony můžete vytvořit obvyklým způsobem. Další informace najdete v článku [Vytvoření pravidla telemetrie a nastavení oznámení v](howto-create-telemetry-rules.md) článku s postupem pro Azure IoT Central aplikace.

**Řídicí panely zařízení** mohou obsahovat několik typů dlaždic. Některé dlaždice mohou obsahovat nastavení a vlastnosti. Pokud je odebrána vlastnost nebo nastavení použité v dlaždici, je dlaždice zcela nebo částečně přerušena. Můžete přejít na dlaždici a opravit problém buď odebráním dlaždice, nebo aktualizací obsahu dlaždice.

## <a name="migrate-a-device-across-device-template-versions"></a>Migrace zařízení napříč verzemi šablon zařízení

Můžete vytvořit několik verzí šablony zařízení. V průběhu času budete mít pomocí těchto šablon zařízení více připojených zařízení. Zařízení můžete migrovat z jedné verze šablony zařízení do jiné. Následující postup popisuje, jak migrovat zařízení:

1. Přejít na stránku **Device Explorer** .
1. Vyberte zařízení, které potřebujete migrovat na jinou verzi.
1. Vyberte **migrovat zařízení**.
1. Vyberte číslo verze, do kterého chcete migrovat zařízení, a zvolte **migrovat**.

![Postup migrace zařízení](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili používat verze šablon zařízení ve vaší aplikaci Azure IoT Central, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Jak vytvořit pravidla telemetrie](howto-create-telemetry-rules.md)