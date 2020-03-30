---
title: Správa zařízení v aplikaci Azure IoT Central | Dokumenty společnosti Microsoft
description: Jako operátor se dozvíte, jak spravovat zařízení v aplikaci Azure IoT Central.
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 8725a822c575ce80b9810d56bfd072241ded4c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157938"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Správa zařízení v aplikaci Azure IoT Central



Tento článek popisuje, jak jako operátor, ke správě zařízení ve vaší aplikaci Azure IoT Central. Jako operátor můžete:

- Na stránce **Zařízení** můžete zobrazit, přidat a odstranit zařízení připojená k vaší aplikaci Azure IoT Central.
- Udržujte aktuální inventář svých zařízení.
- Udržujte metadata zařízení aktuální změnou hodnot uložených ve vlastnostech zařízení ze zobrazení.
- Ovládejte chování svých zařízení aktualizací nastavení na konkrétním zařízení ze svých zobrazení.

## <a name="view-your-devices"></a>Zobrazení zařízení

Zobrazení jednotlivých zařízení:

1. V levém podokně zvolte **Zařízení.** Zde vidíte seznam všech zařízení a šablon zařízení.

1. Zvolte šablonu zařízení.

1. V pravém podokně stránky **Zařízení** se zobrazí seznam zařízení vytvořených z této šablony zařízení. Vyberte si jednotlivé zařízení, abyste viděli stránku s podrobnostmi o zařízení pro toto zařízení:

    ![Stránka s podrobnostmi o zařízení](./media/howto-manage-devices/devicelist.png)


## <a name="add-a-device"></a>Přidání zařízení

Přidání zařízení do aplikace Azure IoT Central:

1. V levém podokně zvolte **Zařízení.**

1. Vyberte šablonu zařízení, ze které chcete zařízení vytvořit.

1. Zvolte + **Nový**.

1. Zapněte **simulované** přepínače **na Zapnuto** nebo **Vypnuto**. Skutečné zařízení je pro fyzické zařízení, které připojíte k aplikaci Azure IoT Central. Simulované zařízení má ukázková data vygenerovaná pro vás Azure IoT Central.

1. Klikněte na **Vytvořit**.

1. Toto zařízení se nyní zobrazí v seznamu zařízení pro tuto šablonu. Výběrem zařízení zobrazíte stránku s podrobnostmi o zařízení, která obsahuje všechna zobrazení zařízení.

## <a name="import-devices"></a>Import zařízení

Chcete-li k aplikaci připojit velký počet zařízení, můžete zařízení hromadně importovat ze souboru CSV. Soubor CSV by měl mít následující sloupce a záhlaví:

* **IOTC_DeviceID** - ID zařízení by mělo být malé.
* **IOTC_DeviceName** - tento sloupec je volitelný.

Chcete-li hromadně zaregistrovat zařízení ve vaší aplikaci:

1. V levém podokně zvolte **Zařízení.**

1. Na levém panelu zvolte šablonu zařízení, pro kterou chcete zařízení hromadně vytvořit.

    > [!NOTE]
    > Pokud ještě nemáte šablonu zařízení, můžete importovat zařízení v části **Všechna zařízení** a zaregistrovat je bez šablony. Po importu zařízení je pak můžete migrovat do šablony.

1. Vyberte **Importovat**.

    ![Akce importu](./media/howto-manage-devices/bulkimport1a.png)


1. Vyberte soubor CSV se seznamem ID zařízení, která mají být importována.

1. Import zařízení se spustí po nahrání souboru. Stav importu můžete sledovat na panelu Operace se zařízením. Tento panel se automaticky zobrazí po zahájení importu nebo k němu můžete přistupovat pomocí ikony zvonku v pravém horním rohu.

1. Po dokončení importu se na panelu Operace se zařízením zobrazí zpráva o úspěchu.

    ![Úspěch importu](./media/howto-manage-devices/bulkimport3a.png)


Pokud se operace importu zařízení nezdaří, zobrazí se na panelu Operace zařízení chybová zpráva. Je vygenerován soubor protokolu zachycující všechny chyby, které si můžete stáhnout.

**Migrace zařízení do šablony**

Pokud zaregistrujete zařízení spuštěním importu v části **Všechna zařízení**, budou zařízení vytvořena bez přidružení šablony zařízení. Zařízení musí být přidružena k šabloně, aby bylo možné prozkoumat data a další podrobnosti o zařízení. Chcete-li přidružit zařízení k šabloně, postupujte takto:

1. V levém podokně zvolte **Zařízení.**

1. Na levém panelu zvolte **Všechna zařízení**:

    ![Nepřidružená zařízení](./media/howto-manage-devices/unassociateddevices1a.png)


1. Pomocí filtru v mřížce určete, zda je hodnota ve sloupci **Šablona zařízení** pro některá z vašich zařízení "Nepřidružená".

1. Vyberte zařízení, která chcete přidružit k šabloně:

1. Vyberte **Migrovat**:

    ![Přidružení zařízení](./media/howto-manage-devices/unassociateddevices2a.png)


1. Vyberte šablonu ze seznamu dostupných šablon a vyberte **Migrovat**.

1. Vybraná zařízení jsou přidružena k vybrané šabloně zařízení.


## <a name="export-devices"></a>Exportzařízení

Chcete-li připojit skutečné zařízení k IoT Central, potřebujete jeho připojovací řetězec. Můžete exportovat podrobnosti o zařízení hromadně získat informace potřebné k vytvoření připojovacích řetězců zařízení. Proces exportu vytvoří soubor CSV s identitou zařízení, názvem zařízení a klíči pro všechna vybraná zařízení.

Použití zařízení pro hromadnou export z aplikace:

1. V levém podokně zvolte **Zařízení.**

1. V levém podokně zvolte šablonu zařízení, ze které chcete zařízení exportovat.

1. Vyberte zařízení, která chcete exportovat, a pak vyberte akci **Exportovat.**

    ![Export](./media/howto-manage-devices/export1a.png)


1. Spustí se proces exportu. Stav můžete sledovat pomocí panelu Operace se zařízením.

1. Po dokončení exportu se zobrazí zpráva o úspěchu spolu s odkazem na stažení generovaného souboru.

1. Chcete-li soubor stáhnout do místní složky na disku, vyberte odkaz **Stáhnout soubor.**

    ![Úspěch exportu](./media/howto-manage-devices/export2a.png)


1. Exportovaný soubor CSV obsahuje následující sloupce: ID zařízení, název zařízení, klíče zařízení a kryptografické otisky certifikátu X509:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Další informace o připojovacích řetězcích a připojení reálných zařízení k aplikaci IoT Central najdete [v tématu Připojení zařízení v Azure IoT Central](concepts-get-connected.md).

## <a name="delete-a-device"></a>Odstranit zařízení

Odstranění skutečného nebo simulovaného zařízení z aplikace Azure IoT Central:

1. V levém podokně zvolte **Zařízení.**

1. Vyberte šablonu zařízení zařízení, které chcete odstranit.

1. Pomocí filtračních nástrojů můžete zařízení filtrovat a vyhledávat. Zaškrtněte políčko vedle zařízení, která chcete odstranit.

1. Zvolte **Odstranit**. Stav tohoto odstranění můžete sledovat na panelu Operace se zařízením.

## <a name="change-a-property"></a>Změna vlastnosti

Vlastnosti cloudu jsou metadata zařízení přidružená k zařízení, jako je město a sériové číslo. Zapisovatelné vlastnosti řídí chování zařízení. Jinými slovy umožňují poskytovat vstupy do vašeho zařízení.  Vlastnosti zařízení jsou nastaveny zařízením a jsou jen pro čtení v rámci IoT Central. Vlastnosti můžete zobrazit a aktualizovat v **zobrazeních Podrobnosti o zařízení** pro vaše zařízení.

1. V levém podokně zvolte **Zařízení.**

1. Vyberte šablonu zařízení zařízení, jehož vlastnosti chcete změnit, a vyberte cílové zařízení.

1. Vyberte zobrazení, které obsahuje vlastnosti pro vaše zařízení, toto zobrazení umožňuje vstupní hodnoty a vyberte **Uložit** v horní části stránky. Zde vidíte vlastnosti, které má vaše zařízení, a jejich aktuální hodnoty. Vlastnosti cloudu a zapisovatelné vlastnosti mají upravitelná pole, zatímco vlastnosti zařízení jsou jen pro čtení. U zapisovatelných vlastností můžete zobrazit jejich stav synchronizace v dolní části pole. 

1. Upravte vlastnosti na hodnoty, které potřebujete. Můžete upravit více vlastností najednou a aktualizovat je všechny najednou.

1. Zvolte **Uložit**. Pokud jste uložili zapisovatelné vlastnosti, hodnoty se odešlou do zařízení. Když zařízení potvrdí změnu pro zapisovatelnou vlastnost, stav se vrátí zpět do **synchronizované**. Pokud jste uložili vlastnost cloudu, hodnota se aktualizuje.


## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili spravovat zařízení v aplikaci Azure IoT Central, tady je další doporučený krok:

> [!div class="nextstepaction"]
> [Jak používat skupiny zařízení](tutorial-use-device-groups.md)

<!-- Next how-tos in the sequence -->
