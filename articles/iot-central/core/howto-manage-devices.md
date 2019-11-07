---
title: Správa zařízení v aplikaci Azure IoT Central | Microsoft Docs
description: Jako operátor se naučíte spravovat zařízení ve vaší aplikaci Azure IoT Central.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: adcaa29ed894f2d61871f467369bcdd05f8cc593
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73601589"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Správa zařízení v aplikaci Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Tento článek popisuje, jak jako operátor můžete spravovat zařízení v aplikaci Azure IoT Central. Jako operátor můžete:

- Na stránce **zařízení** můžete zobrazit, přidat a odstranit zařízení připojená k vaší aplikaci Azure IoT Central.
- Udržujte si aktuální inventář svých zařízení.
- Udržujte svá metadata zařízení v aktuálním stavu změnou hodnot uložených ve vlastnostech zařízení.
- Řízení chování zařízení pomocí aktualizace nastavení na konkrétním zařízení na stránce **Nastavení** .

## <a name="view-your-devices"></a>Zobrazení zařízení

Postup zobrazení jednotlivých zařízení:

1. V levém podokně vyberte **zařízení** . Tady se zobrazí seznam [šablon zařízení](howto-set-up-template.md).

1. V seznamu **šablon** vyberte šablonu zařízení.

1. V pravém podokně stránky **zařízení** se zobrazí seznam zařízení vytvořených z této šablony zařízení. Vyberte individuální zařízení, na kterém se zobrazí stránka s podrobnostmi o zařízení pro toto zařízení:

    ![Stránka s podrobnostmi o zařízení](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>Přidání zařízení

Postup přidání zařízení do aplikace Azure IoT Central:

1. V levém podokně vyberte **zařízení** .

1. Vyberte šablonu zařízení, ze které chcete vytvořit zařízení.

1. Vyberte + **Nový**.

1. Vyberte **reálné** nebo **simulované**. Reálné zařízení je pro fyzické zařízení, které se připojujete k aplikaci Azure IoT Central. Simulované zařízení obsahuje ukázková data, která vám vygenerovala Azure IoT Central.

## <a name="import-devices"></a>Importovat zařízení

Pokud chcete k aplikaci připojit velký počet zařízení, můžete hromadně importovat zařízení ze souboru CSV. Soubor CSV by měl mít následující sloupce a hlavičky:

* **IOTC_DeviceID** – identifikátor zařízení by měl být malými písmeny.
* **IOTC_DeviceName** – tento sloupec je nepovinný.

Postup hromadné registrace zařízení v aplikaci:

1. V levém podokně vyberte **zařízení** .

1. Na levém panelu vyberte šablonu zařízení, pro kterou chcete zařízení hromadně vytvořit.

    > [!NOTE]
    > Pokud ještě nemáte šablonu zařízení, můžete importovat zařízení v části **nepřidružená zařízení** a zaregistrovat je bez šablony. Po dokončení importu zařízení je můžete přidružit k šabloně.

1. Vyberte **importovat**.

    ![Importovat akci](./media/howto-manage-devices/bulkimport1a.png)

1. Vyberte soubor CSV, který obsahuje seznam ID zařízení, která se mají importovat.

1. Po nahrání souboru se spustí import zařízení. Stav importu můžete sledovat v horní části mřížky zařízení.

1. Po dokončení importu se v mřížce zařízení zobrazí zpráva o úspěchu.

    ![Úspěšný import](./media/howto-manage-devices/bulkimport3a.png)

Pokud se operace importu zařízení nezdařila, zobrazí se v mřížce zařízení chybová zpráva. Vygeneruje se soubor protokolu, který zachytí všechny chyby, které si můžete stáhnout.

**Přidružení zařízení k šabloně**

Pokud zařízení zaregistrujete tak, že spustíte import v části **nepřidružená zařízení**, vytvoří se zařízení bez přidružení šablony zařízení. Aby bylo možné prozkoumat data a další podrobnosti o zařízení, musí být zařízení přidružená k šabloně. K přidružení zařízení se šablonou použijte tento postup:

1. V levém podokně vyberte **zařízení** .

1. Na levém panelu vyberte **nepřidružená zařízení**:

    ![Nepřidružená zařízení](./media/howto-manage-devices/unassociateddevices1a.png)

1. Vyberte zařízení, která chcete přidružit k šabloně:

1. Vyberte **přidružit**:

    ![Přidružit zařízení](./media/howto-manage-devices/unassociateddevices2a.png)

1. Zvolte šablonu ze seznamu dostupných šablon a vyberte **přidružit**.

1. Vybraná zařízení jsou přidružená k šabloně zařízení, kterou jste zvolili.

> [!NOTE]
> Po přidružení zařízení k šabloně nemůže být nepřidružená ani přidružená k jiné šabloně.

## <a name="export-devices"></a>Exportovat zařízení

Pokud chcete k IoT Central připojit reálné zařízení, budete potřebovat jeho připojovací řetězec. Podrobnosti o zařízení můžete exportovat hromadně a získat tak informace potřebné k vytvoření připojovacích řetězců zařízení. Proces exportu vytvoří soubor CSV s identitou zařízení, názvem zařízení a klíči pro všechna vybraná zařízení.

Postup hromadného exportu zařízení z aplikace:

1. V levém podokně vyberte **zařízení** .

1. Na levém panelu vyberte šablonu zařízení, ze které chcete zařízení exportovat.

1. Vyberte zařízení, která chcete exportovat, a pak vyberte akci **exportovat** .

    ![Export](./media/howto-manage-devices/export1a.png)

1. Spustí se proces exportu. Stav můžete sledovat v horní části mřížky.

1. Po dokončení exportu se zobrazí zpráva o úspěchu a odkaz ke stažení vygenerovaného souboru.

1. Vyberte **zprávu o úspěchu** a Stáhněte si soubor do místní složky na disku.

    ![Úspěšnost exportu](./media/howto-manage-devices/export2a.png)

1. Exportovaný soubor CSV obsahuje následující sloupce: ID zařízení, název zařízení, klíče zařízení a kryptografické otisky certifikátu x509:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Další informace o připojovacích řetězcích a připojení skutečných zařízení k vaší aplikaci IoT Central najdete v tématu [připojení zařízení ve službě Azure IoT Central](concepts-connectivity.md).

## <a name="delete-a-device"></a>Odstranění zařízení

Odstranění reálného nebo simulovaného zařízení z aplikace IoT Central v Azure:

1. V levém podokně vyberte **zařízení** .

1. Vyberte šablonu zařízení zařízení, které chcete odstranit.

1. Zaškrtněte políčko u zařízení, které chcete odstranit.

1. Vyberte **Odstranit**.

## <a name="change-a-device-setting"></a>Změna nastavení zařízení

Nastavení řídí chování zařízení. Jinými slovy, umožňují zadat vstupy do zařízení. Nastavení zařízení můžete zobrazit a aktualizovat na stránce s **podrobnostmi o zařízení** .

1. V levém podokně vyberte **zařízení** .

1. Vyberte šablonu zařízení zařízení, u kterého chcete změnit nastavení.

1. Klikněte na kartu **Nastavení** . Tady vidíte všechna nastavení, která má vaše zařízení, a jejich aktuální hodnoty. U každého nastavení uvidíte, jestli se zařízení pořád synchronizuje.

1. Upravte nastavení na hodnoty, které potřebujete. Současně můžete upravit více nastavení a současně je aktualizovat.

1. Vyberte **aktualizovat**. Hodnoty se odešlou do vašeho zařízení. Když zařízení potvrdí změnu nastavení, stav nastavení se vrátí zpět na **Synchronizovaný**.

## <a name="change-a-property"></a>Změna vlastnosti

Vlastnosti jsou metadata zařízení přidružená k zařízení, jako je město a sériové číslo. Vlastnosti můžete zobrazit a aktualizovat na stránce **Podrobnosti o zařízení** .

1. V levém podokně vyberte **zařízení** .

1. Vyberte šablonu zařízení zařízení, u kterého chcete změnit vlastnosti.

1. Klikněte na kartu **vlastnosti** , kde se zobrazí všechny vlastnosti.

1. Upravte vlastnosti aplikace na hodnoty, které potřebujete. Můžete upravit více vlastností najednou a současně je aktualizovat. Vyberte **aktualizovat**.

> [!NOTE]
> Nemůžete změnit hodnotu _vlastností zařízení_. Vlastnosti zařízení jsou nastavené zařízením a jsou určené jen pro čtení v rámci aplikace IoT Central v Azure.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak spravovat zařízení v aplikaci Azure IoT Central, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Jak používat sady zařízení](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
