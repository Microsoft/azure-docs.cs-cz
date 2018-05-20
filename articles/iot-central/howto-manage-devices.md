---
title: Spravovat zařízení v aplikaci Azure IoT centrální | Microsoft Docs
description: Jako operátor zjistěte, jak spravovat zařízení v Azure IoT centrální aplikaci.
services: iot-central
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: b56e352a1f39dbd536347f6c7e83a6aabfe32ecc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Spravovat zařízení v aplikaci Azure IoT centrální

Tento článek popisuje, jak jako operátor, ke správě zařízení v Microsoft Azure IoT centrální aplikaci. Jako operátor můžete:

- Použití **Explorer** můžete zobrazit, přidat a odstranit zařízení připojená k Azure IoT centrální aplikaci.
- Udržujte aktuální inventář zařízení.
- Zachovat metadata zařízení aktuální změnou hodnoty uložené ve vlastnostech zařízení.
- Řídí chování zařízení při aktualizaci nastavení na konkrétní zařízení z **nastavení** stránky.

## <a name="view-your-devices"></a>Zobrazení zařízení

Chcete-li zobrazit jednotlivé zařízení:

1. Zvolte **Explorer** v levé navigační nabídce. Tady najdete seznam vaše [zařízení šablony](howto-set-up-template.md).

1. Vyberte **šablona zařízení** klikněte v levém podokně.

1. V pravém podokně zobrazí seznam zařízení, které jsou vytvořené z této šablony zařízení. Zvolte k jednotlivým zařízením zobrazíte **podrobnosti o zařízení** stránky pro toto zařízení:

    ![Stránka Podrobnosti zařízení](./media/howto-manage-devices/image1.png)

## <a name="add-a-device"></a>Přidání zařízení

Přidání zařízení do Azure IoT centrální aplikace:

1. Zvolte **Explorer** v levé navigační nabídce.

1. Zvolte šablonu zařízení, ze kterého chcete vytvořit zařízení.

1. Zvolte + **nové**.

1. Zvolte **skutečné** nebo **Simulated**. Skutečné zařízení je pro fyzické zařízení, které můžete připojit k Azure IoT centrální aplikace. Simulované zařízení má vygenerované pomocí Azure IoT centrální ukázková data. Tento příklad používá skutečné zařízení. Zvolte **skutečné** přejděte na **podrobnosti o zařízení** stránku pro nové zařízení.

## <a name="delete-a-device"></a>Odstranění zařízení

Chcete odstranit z aplikace Azure IoT centrální buď skutečné nebo simulované zařízení:

1. Zvolte **Explorer** v navigační nabídce.

1. Zvolte šablonu zařízení zařízení, které chcete odstranit.

1. Zaškrtněte políčko vedle zařízení odstranit.

1. Zvolte **odstranit**.

## <a name="change-a-device-setting"></a>Změna nastavení zařízení

Nastavení řídí chování zařízení. Jinými slovy umožňují zadat vstupy do svého zařízení. Můžete zobrazit a aktualizovat nastavení zařízení na **podrobnosti o zařízení** stránky.

1. Zvolte **Explorer** v navigační nabídce.

1. Zvolte šablonu zařízení zařízení, jehož nastavení chcete změnit.

1. Vyberte **nastavení** kartě. Tady vidíte, všechna nastavení, které má vaše zařízení a jejich aktuální hodnoty. Pro každé nastavení se zobrazí, pokud je zařízení stále synchronizaci.

1. Upravte nastavení na požadované hodnoty. Můžete najednou upravit víc nastavení a provede jejich aktualizaci všech ve stejnou dobu.

1. Zvolte **aktualizace**. Jsou tyto hodnoty odeslány do vašeho zařízení. Pokud zařízení uznává Změna nastavení, stav nastavení přejde zpět na **synchronizovat**.

## <a name="change-a-property"></a>Změnit vlastnosti

Vlastnosti jsou přidružené k zařízení, například města a sériové číslo metadat zařízení. Můžete zobrazit a aktualizovat vlastnosti na **podrobnosti o zařízení** stránky.

1. Zvolte **Explorer** v navigační nabídce.

1. Zvolte šablonu zařízení zařízení, jehož vlastnosti chcete změnit.

1. Vyberte **vlastnosti** kartě, kde uvidíte všechny vlastnosti.

1. Upravte vlastnosti, aby požadované hodnoty. Můžete najednou upravit víc vlastností a provede jejich aktualizaci všech ve stejnou dobu.

1. Zvolte **aktualizace**.

> [!NOTE]
> Nelze změnit hodnotu _vlastnosti zařízení_. Vlastnosti zařízení se nastavují zařízení a v rámci Azure IoT centrální aplikaci jen pro čtení.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili způsobu správy zařízení v aplikaci Azure IoT centrální, zde je navržené další krok:

> [!div class="nextstepaction"]
> [Jak používat sady zařízení](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->