---
title: Vytvoření a správa telemetrie pravidla v aplikaci Azure IoT centrální | Microsoft Docs
description: Telemetrie pravidla ve službě Azure IoT střed umožňují monitorovat zařízení skoro v reálném čase a automaticky vyvolání akce, například odeslání e-mailu, když se pravidlo spustí.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: caca4e9db898b3766995fde8c5eebd4767abd85b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629809"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Vytvoření pravidla telemetrie a nastavit oznámení v aplikaci Azure IoT centrální

Microsoft Azure IoT centrální můžete použít pro vzdálené monitorování připojená zařízení. Pravidla ve službě Azure IoT střed umožňují sledovat vaše zařízení skoro v reálném čase a automaticky vyvolání akce, například odeslání e-mailu, když se pravidlo spustí. V několika kliknutí můžete definovat podmínku, která má monitorovat data zařízení a nakonfigurujte akci, která má být vyvolána. Tento článek vysvětluje telemetrie pravidlo podrobně.

Střed Azure IoT používá [telemetrie měření](howto-set-up-template.md) k zaznamenání dat o zařízení. Každý typ měření má klíčové atributy, které definují měření. Můžete vytvořit pravidla pro každý typ měření zařízení monitorování a generování výstrah v případě, že pravidlo spustí. Pravidlo telemetrie se aktivuje při telemetrie vybrané zařízení protne zadanou prahovou hodnotu.

## <a name="create-a-telemetry-rule"></a>Vytvoření pravidla telemetrie

V této části se dozvíte, jak vytvořit pravidlo telemetrie. Tento příklad používá klimatizace připojených zařízení, které odesílá telemetrická data teploty a vlhkosti. Toto pravidlo monitoruje teplota hlášená zařízení a odešle e-mail, když se překročí 80 stupňů.

1. Přejděte na stránku podrobností zařízení pro zařízení, který chcete přidat pravidlo.

1. Pokud ještě jste nevytvořili všechna pravidla, zobrazí se následující obrazovka:

    ![Zatím žádná pravidla](media\howto-create-telemetry-rules\image1.png)

1. Na **pravidla** , zvolte **+ nové pravidlo** zobrazíte typy pravidel, můžete vytvořit.

    ![Typy pravidel](media\howto-create-telemetry-rules\image2.png)

1. Vyberte **Telemetrie** dlaždici otevřít formulář vytvoření pravidla.

    ![Pravidlo telemetrie](media\howto-create-telemetry-rules\image3.png)

1. Zvolte název, který umožňuje identifikovat pravidla v této šabloně zařízení.

1. Postup při povolení okamžitě pravidla pro všechna zařízení, které jsou vytvořené z této šablony, přepněte **Povolit pravidlo**.

### <a name="configure-the-rule-condition"></a>Konfigurace podmínka pro pravidlo

V této části se dozvíte, jak přidat podmínku, kterou chcete monitorovat teplotní telemetrie.

1. Vyberte **+** vedle **podmínku**.

1. Vyberte **teploty** typ telemetrie z rozevíracího seznamu. Potom vyberte operátor a zadejte prahovou hodnotu. Můžete přidat více podmínek telemetrie. Pokud je zadáno několik podmínek, musí být splněny všechny podmínky pravidla pro aktivaci.

    ![Přidejte podmínku Telemetrie](media\howto-create-telemetry-rules\image4.png)

    > [!NOTE]
    > Vyberte alespoň jedno měření telemetrie, když definujete pravidlo podmínku telemetrie.

### <a name="configure-the-action"></a>Konfigurace akce

V této části se dozvíte, jak určit, co pravidlo dělá, když podmínku odpovídá přidáním akce.

1. Vyberte **+** vedle **akce**. Zde se zobrazí seznam akcí, k dispozici. Chcete-li verzi public Preview **e-mailu** je jedinou podporovanou akcí.

    ![Akce pro přidání](media\howto-create-telemetry-rules\image5.png)

1. Vyberte **e-mailu** akce, zadejte platnou e-mailovou adresu do **k** pole a zadejte poznámku k vložit do těla e-mailu, když se pravidlo spustí.

    > [!NOTE]
    > E-maily jsou odesílány pouze uživatele, kteří byly přidány k aplikaci a alespoň jednou přihlásí. Další informace o [Správa uživatelů](howto-administer.md) v centrální Azure IoT.

   ![Konfigurace akce](media\howto-create-telemetry-rules\image6.png)

1. Chcete-li uložit pravidlo, zvolte **Uložit**. Pravidlo přejde během několika minut za provozu a spustí monitorování telemetrie odesílány do vaší aplikace. Pokud je podmínka uvedená v pravidle odpovídá, pravidlo spustí akci nakonfigurovanou e-mailu.

## <a name="parameterize-the-rule"></a>Parametrizace pravidlo

Pravidla můžete odvození určitých vales z **vlastnosti zařízení** jako parametry. Pomocí parametrů je užitečné v situacích, kde se liší telemetrie prahové hodnoty pro různá zařízení. Při vytváření pravidla, vyberte vlastnosti zařízení, která určuje mezní hodnotu, jako například **maximální ideální prahová hodnota**, místo absolutní hodnotu, jako je například 80 stupňů. Když pravidlo provede, odpovídá telemetrie zařízení s hodnota zadaná pro vlastnost zařízení.

Pomocí parametrů je efektivní způsob, jak snížit počet pravidel pro správu za šablona zařízení.

Akce je také možné nakonfigurovat pomocí **vlastnosti zařízení** jako parametr. Pokud jako vlastnost zařízení je uložený e-mailovou adresu, pak můžete použít, když definujete **k** adresu.

## <a name="delete-a-rule"></a>Odstranění pravidla

Pokud pravidlo již nepotřebujete, odstraňte jej otevřením pravidlo a zvolením **odstranit**. Odstraňuje se pravidlo odebere z šablony zařízení a všechna související zařízení.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>Povolí nebo zakáže pravidlo pro šablonu zařízení

Přejděte do zařízení a vyberte pravidlo, které chcete povolit nebo zakázat. Přepnutím **Povolit pravidlo pro všechna zařízení této šablony** tlačítka na pravidlo povolí nebo zakáže pravidlo pro všechny zařízení přidružená k šabloně zařízení.

## <a name="enable-or-disable-a-rule-for-a-device"></a>Povolí nebo zakáže pravidlo pro zařízení

Přejděte do zařízení a vyberte pravidlo, které chcete povolit nebo zakázat. Přepnutí **pravidlo povolení pro toto zařízení** tlačítko k povolení nebo zakázání pravidla pro toto zařízení.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili Postup upravení pravidla v aplikaci Azure IoT centrální, zde je navržené další krok:

> [!div class="nextstepaction"]
> [Jak spravovat zařízení](howto-manage-devices.md).