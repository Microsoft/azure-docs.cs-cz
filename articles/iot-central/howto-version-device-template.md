---
title: Principy správy verzí šablon zařízení pro aplikace Azure IoT Central | Microsoft Docs
description: Iterujte na šablonách zařízení vytvořením nových verzí a bez dopadu na živá zařízení připojená k síti.
author: sandeeppujar
ms.author: sandeepu
ms.date: 07/08/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: c718794528989fbc46b404617f16d3a91ade6011
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877310"
---
# <a name="create-a-new-device-template-version"></a>Vytvořit novou verzi šablony zařízení

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

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

**Pravidla** mohou obsahovat podmínky, které jsou závislé na vlastnostech. Pokud jste odebrali jednu nebo více těchto vlastností, tato pravidla mohou být v nové verzi šablony zařízení poškozena. Můžete přejít na tato konkrétní pravidla a aktualizovat podmínky pro opravu pravidel. Pravidla pro předchozí verzi by měla fungovat bez dopadu.

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