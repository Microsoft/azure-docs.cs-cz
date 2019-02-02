---
title: Správa zařízení v aplikaci Azure IoT Central | Dokumentace Microsoftu
description: Jakožto Obsluha zjistěte, jak spravovat zařízení v aplikaci Azure IoT Central.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 5e423478e894487795b376340b13155950b4ef8d
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667042"
---
# <a name="manage-devices-in-your-azure-iot-central-application-new-ui-design"></a>Správa zařízení v aplikaci Azure IoT Central (návrh nové uživatelské rozhraní)

Tento článek popisuje, jak jako operátor ke správě zařízení v aplikaci Azure IoT Central. Jakožto Obsluha můžete:

- Použití **Device Explorer** stránky k zobrazení, přidání a odstranění zařízení připojená k vaší aplikaci Azure IoT Central.
- Zachovat aktuální inventář zařízení.
- Vaše metadata zařízení aktualizovat tak, že změníte hodnotám uloženým ve vlastnostech zařízení.
- Řízení chování zařízení aktualizací nastavení do konkrétních zařízení **nastavení** stránky.

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="view-your-devices"></a>Zobrazení zařízení

Chcete-li zobrazit jednotlivá zařízení:

1. Zvolte **Device Explorer** v levé navigační nabídce. Tady se zobrazí seznam vašich [šablon](howto-set-up-template-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

1. Výběr šablony v zařízení **šablony** seznamu.

1. V pravém podokně **Device Explorer** stránce se zobrazí seznam zařízení, které jsou vytvořené z této šablony zařízení. Zvolte k individuálnímu zařízení a najdete na stránce Podrobnosti o zařízení pro toto zařízení:

    ![Stránka Podrobnosti zařízení](./media/howto-manage-devices-experimental/devicelist.png)

## <a name="add-a-device"></a>Přidání zařízení

Přidání zařízení do aplikace Azure IoT Central:

1. Zvolte **Device Explorer** v levé navigační nabídce.

1. Výběr šablony zařízení, ze kterého chcete vytvořit zařízení.

1. Zvolte + **nové**.

1. Zvolte **skutečné** nebo **simulované**. Skutečné zařízení je u fyzických zařízení, která připojení k aplikaci Azure IoT Central. Simulované zařízení obsahuje ukázková data generovaná pro vás Azure IoT Central.

## <a name="import-devices"></a>Importovat zařízení

K připojení velkého počtu zařízení pro vaši aplikaci, můžete hromadně importovat zařízení ze souboru CSV. Soubor CSV musí obsahovat následující sloupce a záhlaví:

* **IOTC_DeviceID** – ID zařízení by měl obsahovat jenom malá písmena.
* **IOTC_DeviceName** – v tomto sloupci je volitelný.

K hromadné registraci zařízení ve vaší aplikaci:

1. Zvolte **Device Explorer** v levé navigační nabídce.

1. Na levém panelu vyberte šablonu zařízení, pro které chcete hromadně vytvořit zařízení.

    > [!NOTE]
    > Pokud nemáte šablonu zařízení, ale pak můžete importovat zařízení v rámci **zrušeno přiřazení zařízení** a jejich registrace bez šablony. Po importu zařízení je můžete přiřadit pomocí šablony.

1. Klikněte na **Importovat**.

    ![Akce importu](./media/howto-manage-devices-experimental/BulkImport1.png)

1. Vyberte soubor CSV obsahující seznam ID zařízení k importu.

1. Importovat zařízení se začne tento soubor se odeslal. Můžete sledovat stav importu v horní části stránky mřížky zařízení.

1. Po dokončení importu zprávu o úspěšném dokončení se zobrazí v mřížce zařízení.

    ![Importovat úspěch](./media/howto-manage-devices-experimental/BulkImport3.png)

Pokud zařízení import operace se nezdaří, zobrazí chybová zpráva na zařízení mřížky. Je vygenerován soubor protokolu zachycení všech chyb, že si můžete stáhnout.

**Přidružení zařízení pomocí šablony**

Když si zaregistrujete zařízení spuštěním importovat v části **zrušeno přiřazení zařízení**, pak zařízení jsou vytvořeny bez jakékoli šablony přidružení zařízení. Zařízení musí být spojen se šablonou a podívejte se data a další podrobnosti o zařízení. Použijte následující postup zařízení přidružit šablonu:

1. Zvolte **Device Explorer** v levé navigační nabídce.

1. Na levém panelu, vyberte **zrušeno přiřazení zařízení**:

    ![Nepřidružené zařízení](./media/howto-manage-devices-experimental/UnassociatedDevices1.png)

1. Vyberte zařízení, které chcete přidružit šablonu:

1. Klikněte na tlačítko **přidružit**:

    ![Přidružit zařízení](./media/howto-manage-devices-experimental/UnassociatedDevices2.png)

1. Vyberte šablonu ze seznamu dostupných šablon a klikněte na tlačítko **přidružit** tlačítko.

1. Vybraná zařízení jsou přidružená k šabloně zařízení, kterou jste zvolili.

> [!NOTE]
> Po přidružený k šabloně zařízení nemůže být zrušeno přiřazení nebo související s jinou šablonou.

## <a name="export-devices"></a>Export zařízení

Chcete-li připojit skutečné zařízení k IoT Central, musíte svůj připojovací řetězec. Můžete to taky podrobnosti o zařízení hromadné získat připojovací řetězce a další vlastnosti. Proces exportu vytvoří soubor CSV s identitou zařízení, název zařízení a primární připojovací řetězec pro všechna vybraná zařízení.

Chcete-li hromadně exportovat zařízení z vaší aplikace:

1. Zvolte **Device Explorer** v levé navigační nabídce.

1. Na levém panelu vyberte šablonu zařízení, ze kterého chcete exportovat zařízení.

1. Vyberte zařízení, která chcete exportovat a pak klikněte na tlačítko **exportovat** akce.

    ![Export](./media/howto-manage-devices-experimental/Export1.png)

1. Spustí se proces exportu. Můžete sledovat stav v horní části stránky mřížky.

1. Po dokončení exportu zprávu o úspěšném dokončení se zobrazí spolu s odkazem na stažení generovaného souboru.

1. Klikněte na **zpráva o úspěchu** stáhněte soubor do místní složky na disku.

    ![Export úspěch](./media/howto-manage-devices-experimental/Export2.png)

1. Vyexportovaný soubor CSV obsahuje následující sloupce: ID zařízení, název zařízení, zařízení klíče a X509 kryptografické otisky certifikátů:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY   
    * IOTC_X509THUMBPRINT_SECONDARY

## <a name="delete-a-device"></a>Odstranění zařízení

Chcete-li odstranit buď reálných nebo simulovaných zařízení z aplikace Azure IoT Central:

1. Zvolte **Device Explorer** v navigační nabídce.

1. Zvolte šablonu zařízení na zařízení, které chcete odstranit.

1. Zaškrtněte políčko vedle zařízení odstranit.

1. Zvolte **odstranit**.

## <a name="change-a-device-setting"></a>Změnit nastavení zařízení

Nastavení řídí chování zařízení. Jinými slovy umožňují zadat vstupy do svého zařízení. Můžete zobrazit a aktualizovat nastavení zařízení na **podrobnosti o zařízení** stránky.

1. Zvolte **Device Explorer** v navigační nabídce.

1. Výběr šablony zařízení zařízení, jehož nastavení chcete změnit.

1. Zvolte **nastavení** kartu. Zde můžete zobrazit všechna nastavení, které má vaše zařízení a jejich aktuálními hodnotami. Pro každé nastavení se zobrazí, pokud zařízení je stále ještě probíhá synchronizace.

1. Změňte nastavení a hodnoty, které potřebujete. Můžete upravit několik nastavení v čase a aktualizovat všechny najednou.

1. Zvolte **aktualizace**. Jsou tyto hodnoty odeslány do vašeho zařízení. Když zařízení potvrdí změny nastavení, stav nastavení přejde zpět do **synchronizované**.

## <a name="change-a-property"></a>Změnit vlastnosti

Vlastnosti jsou metadata zařízení přidružená k zařízení, jako je například city a sériové číslo. Můžete zobrazit a aktualizovat vlastnosti na **podrobnosti o zařízení** stránky.

1. Zvolte **Device Explorer** v navigační nabídce.

1. Výběr šablony zařízení zařízení, jehož vlastnosti chcete změnit.

1. Zvolte **vlastnosti** kartu, kde uvidíte všechny vlastnosti.

1. Upravte vlastnosti aplikace, které mají hodnoty, které potřebujete. Můžete změnit více vlastností najednou a aktualizovat všechny najednou. Zvolte **aktualizace**.

> [!NOTE]
> Nelze změnit hodnotu _vlastnosti zařízení_. Vlastnosti zařízení jsou nastaveny podle zařízení a jsou jen pro čtení v aplikaci Azure IoT Central.

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak spravovat zařízení v aplikaci Azure IoT Central, tady je navrhované další krok:

> [!div class="nextstepaction"]
> [Použití sady zařízení](howto-use-device-sets.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)

<!-- Next how-tos in the sequence -->
