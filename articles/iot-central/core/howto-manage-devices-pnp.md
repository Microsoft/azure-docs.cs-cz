---
title: Správa zařízení v aplikaci Azure IoT Central | Microsoft Docs
description: Jako operátor se naučíte spravovat zařízení ve vaší aplikaci Azure IoT Central.
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 01346ba518a07da1ebb606393c2834994cfb42e2
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990348"
---
# <a name="manage-devices-in-your-azure-iot-central-application-preview-features"></a>Správa zařízení v aplikaci Azure IoT Central (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Tento článek popisuje, jak jako operátor můžete spravovat zařízení v aplikaci Azure IoT Central. Jako operátor můžete:

- Na stránce **zařízení** můžete zobrazit, přidat a odstranit zařízení připojená k vaší aplikaci Azure IoT Central.
- Udržujte si aktuální inventář svých zařízení.
- Udržujte svá metadata zařízení v aktuálním stavu změnou hodnot uložených ve vlastnostech zařízení z vašich zobrazení.
- Řízení chování zařízení pomocí aktualizace nastavení na konkrétním zařízení z vašich zobrazení

## <a name="view-your-devices"></a>Zobrazení zařízení

Postup zobrazení jednotlivých zařízení:

1. V levém podokně vyberte **zařízení** . Tady se zobrazí seznam všech zařízení a šablon zařízení.

1. Vyberte šablonu zařízení.

1. V pravém podokně stránky **zařízení** se zobrazí seznam zařízení vytvořených z této šablony zařízení. Vyberte individuální zařízení, na kterém se zobrazí stránka s podrobnostmi o zařízení pro toto zařízení:

    ![Stránka s podrobnostmi o zařízení](./media/howto-manage-devices-pnp/devicelist.png)


## <a name="add-a-device"></a>Přidání zařízení

Postup přidání zařízení do aplikace Azure IoT Central:

1. V levém podokně vyberte **zařízení** .

1. Vyberte šablonu zařízení, ze které chcete vytvořit zařízení.

1. Vyberte + **Nový**.

1. **Zapnout nebo** **vypnout** **simulované** přepínání Reálné zařízení je pro fyzické zařízení, které se připojujete k aplikaci Azure IoT Central. Simulované zařízení obsahuje ukázková data, která vám vygenerovala Azure IoT Central.

1. Klikněte na **Vytvořit**.

1. Toto zařízení se nyní zobrazí v seznamu zařízení pro tuto šablonu. Výběrem zařízení zobrazíte stránku s podrobnostmi o zařízení, která obsahuje všechna zobrazení pro dané zařízení.

## <a name="import-devices"></a>Importovat zařízení

Pokud chcete k aplikaci připojit velký počet zařízení, můžete hromadně importovat zařízení ze souboru CSV. Soubor CSV by měl mít následující sloupce a hlavičky:

* **IOTC_DeviceID** – identifikátor zařízení by měl být malými písmeny.
* **IOTC_DeviceName** – tento sloupec je nepovinný.

Postup hromadné registrace zařízení v aplikaci:

1. V levém podokně vyberte **zařízení** .

1. Na levém panelu vyberte šablonu zařízení, pro kterou chcete zařízení hromadně vytvořit.

    > [!NOTE]
    > Pokud ještě nemáte šablonu zařízení, můžete zařízení importovat do **všech zařízení** a zaregistrovat je bez šablony. Po dokončení importu zařízení je můžete migrovat do šablony.

1. Vyberte **importovat**.

    ![Importovat akci](./media/howto-manage-devices-pnp/bulkimport1a.png)


1. Vyberte soubor CSV, který obsahuje seznam ID zařízení, která se mají importovat.

1. Po nahrání souboru se spustí import zařízení. Stav importu můžete sledovat na panelu operace zařízení. Tento panel se automaticky zobrazí po spuštění importu nebo k němu můžete přistupovat přes ikonu zvonku v pravém horním rohu.

1. Po dokončení importu se na panelu operace zařízení zobrazí zpráva o úspěchu.

    ![Úspěšný import](./media/howto-manage-devices-pnp/bulkimport3a.png)


Pokud se operace importu zařízení nezdařila, zobrazí se na panelu operace zařízení chybová zpráva. Vygeneruje se soubor protokolu, který zachytí všechny chyby, které si můžete stáhnout.

**Migrace zařízení do šablony**

Pokud zaregistrujete zařízení spuštěním importu v části **všechna zařízení**, vytvoří se zařízení bez přidružení šablony zařízení. Aby bylo možné prozkoumat data a další podrobnosti o zařízení, musí být zařízení přidružená k šabloně. K přidružení zařízení se šablonou použijte tento postup:

1. V levém podokně vyberte **zařízení** .

1. Na levém panelu vyberte **všechna zařízení**:

    ![Nepřidružená zařízení](./media/howto-manage-devices-pnp/unassociateddevices1a.png)


1. Pomocí filtru v mřížce určíte, jestli je hodnota ve sloupci **Šablona zařízení** nepřidružená pro všechna vaše zařízení.

1. Vyberte zařízení, která chcete přidružit k šabloně:

1. Vyberte **migrovat**:

    ![Přidružit zařízení](./media/howto-manage-devices-pnp/unassociateddevices2a.png)


1. Zvolte šablonu ze seznamu dostupných šablon a vyberte **migrovat**.

1. Vybraná zařízení jsou přidružená k šabloně zařízení, kterou jste zvolili.


## <a name="export-devices"></a>Exportovat zařízení

Pokud chcete k IoT Central připojit reálné zařízení, budete potřebovat jeho připojovací řetězec. Podrobnosti o zařízení můžete exportovat hromadně a získat tak informace potřebné k vytvoření připojovacích řetězců zařízení. Proces exportu vytvoří soubor CSV s identitou zařízení, názvem zařízení a klíči pro všechna vybraná zařízení.

Postup hromadného exportu zařízení z aplikace:

1. V levém podokně vyberte **zařízení** .

1. V levém podokně vyberte šablonu zařízení, ze které chcete zařízení exportovat.

1. Vyberte zařízení, která chcete exportovat, a pak vyberte akci **exportovat** .

    ![Exportovat](./media/howto-manage-devices-pnp/export1a.png)


1. Spustí se proces exportu. Stav můžete sledovat pomocí panelu operace zařízení.

1. Po dokončení exportu se zobrazí zpráva o úspěchu a odkaz ke stažení vygenerovaného souboru.

1. Vyberte odkaz **Stáhnout soubor** a Stáhněte soubor do místní složky na disku.

    ![Úspěšnost exportu](./media/howto-manage-devices-pnp/export2a.png)


1. Exportovaný soubor CSV obsahuje následující sloupce: ID zařízení, název zařízení, klíče zařízení a kryptografické otisky certifikátu x509:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Další informace o připojovacích řetězcích a připojení skutečných zařízení k aplikaci IoT Central najdete v tématu [připojení zařízení ve službě Azure IoT Central](overview-iot-central-get-connected-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="delete-a-device"></a>Odstranění zařízení

Odstranění reálného nebo simulovaného zařízení z aplikace IoT Central v Azure:

1. V levém podokně vyberte **zařízení** .

1. Vyberte šablonu zařízení zařízení, které chcete odstranit.

1. Pomocí nástrojů filtru můžete filtrovat a hledat vaše zařízení. Zaškrtněte políčko u zařízení, která chcete odstranit.

1. Vyberte **Odstranit**. Stav tohoto odstranění můžete sledovat na panelu operace zařízení.

## <a name="change-a-property"></a>Změna vlastnosti

Vlastnosti cloudu jsou metadata zařízení přidružená k zařízení, jako je město a sériové číslo. Vlastnosti, které je možné zapisovat, řídí chování zařízení. Jinými slovy, umožňují zadat vstupy do zařízení.  Vlastnosti zařízení jsou nastavené zařízením a jsou určené jen pro čtení v rámci IoT Central. V zobrazeních **podrobností** o zařízení můžete zobrazit a aktualizovat vlastnosti.

1. V levém podokně vyberte **zařízení** .

1. Zvolte šablonu zařízení zařízení, u kterého chcete změnit vlastnosti a vyberte cílové zařízení.

1. Zvolte zobrazení, které obsahuje vlastnosti vašeho zařízení. Toto zobrazení vám umožní zadat hodnoty a v horní části stránky vybrat **Uložit** . Tady vidíte vlastnosti vašeho zařízení a jejich aktuální hodnoty. Vlastnosti cloudu a zapisovatelné vlastnosti mají upravitelná pole, zatímco vlastnosti zařízení jsou jen pro čtení. V případě zapisovatelných vlastností můžete zobrazit stav synchronizace v dolní části pole. 

1. Upravte vlastnosti na hodnoty, které potřebujete. Současně můžete upravovat více vlastností a současně je aktualizovat.

1. Zvolte **Uložit**. Pokud jste uložili vlastnosti zapisovatelné pro zápis, hodnoty se odešlou do vašeho zařízení. Když zařízení potvrdí změnu vlastnosti pro zápis, vrátí se zpátky do **synchronizovaného**stavu. Pokud jste uložili vlastnost cloudu, hodnota se aktualizuje.


## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak spravovat zařízení v aplikaci Azure IoT Central, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Jak používat sady zařízení](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

<!-- Next how-tos in the sequence -->
