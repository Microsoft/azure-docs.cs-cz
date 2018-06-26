---
title: Spravovat zařízení v aplikaci Azure IoT centrální | Microsoft Docs
description: Jako operátor zjistěte, jak spravovat zařízení v Azure IoT centrální aplikaci.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: cf803c03d266f2a400e47fc551dea62936456177
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937614"
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

    [![Stránka Podrobnosti zařízení](./media/howto-manage-devices/image1.png)](./media/howto-manage-devices/image1.png#lightbox)

## <a name="add-a-device"></a>Přidání zařízení

Přidání zařízení do Azure IoT centrální aplikace:

1. Zvolte **Explorer** v levé navigační nabídce.

1. Zvolte šablonu zařízení, ze kterého chcete vytvořit zařízení.

1. Zvolte + **nové**.

1. Zvolte **skutečné** nebo **Simulated**. Skutečné zařízení je pro fyzické zařízení, které můžete připojit k Azure IoT centrální aplikace. Simulované zařízení má vygenerované pomocí Azure IoT centrální ukázková data. Tento příklad používá skutečné zařízení. Zvolte **skutečné** přejděte na **podrobnosti o zařízení** stránku pro nové zařízení.


## <a name="import-devices"></a>Import zařízení

Pro připojení k vaší aplikaci, Azure IoT centrální velkého počtu zařízení nabízí hromadně import zařízení pomocí souboru CSV. 

Požadavky na soubor CSV:
1. Soubor CSV by měl mít jen jeden sloupec, který obsahuje ID zařízení.

1. Soubor by neměl mít všechny hlavičky.


Pro hromadné registrace zařízení ve vaší aplikaci:

1. Zvolte **Explorer** v levé navigační nabídce.

1. Na levém panelu vyberte šablonu zařízení, pro které chcete k hromadné vytvoření zařízení.

 >   [!NOTE] 
    Pokud nemáte šablonu zařízení ještě můžete importovat zařízení v části **zrušeno přiřazení zařízení** a registrovat bez žádné šablony. Po importu zařízení, můžete pak přidružit je šablonu jako další krok.

1. Klikněte na **Importovat**.

    [![Importovat akce](./media/howto-manage-devices/BulkImport1.png)](./media/howto-manage-devices/BulkImport1.png#lightbox)

1. Vyberte soubor CSV, který obsahuje seznam identifikátorů zařízení určených k importu.

1. Jakmile soubor byl odeslán, začne běžet import zařízení. Můžete sledovat stav importu v horní části zařízení mřížky.

1. Po dokončení importu se zobrazí zpráva o úspěšném provedení na zařízení mřížky.

    [![Import úspěch](./media/howto-manage-devices/BulkImport3.png)](./media/howto-manage-devices/BulkImport3.png#lightbox)

Pokud zařízení import operace selže, zobrazí se chybová zpráva na zařízení mřížky. Soubor protokolu zaznamenávání všechny chyby se generuje a můžete stáhnout kliknutím chybovou zprávu.


**Přidružení zařízení k šablonu**

Pokud zařízení zaregistrujete spuštěním import pod **zrušeno přiřazení zařízení**, pak zařízení jsou vytvořeny bez přidružení šablony jakékoli zařízení. Zařízení musí být přidružena k šabloně chcete dynamicky prozkoumávat data a další podrobnosti o zařízení. Postupujte podle těchto kroků zařízení přidružit šablonu:
1. Zvolte **Explorer** v levé navigační nabídce.
1. Na levém panelu, vyberte **zrušeno přiřazení zařízení**.
    [![Nepřidružený zařízení](./media/howto-manage-devices/UnassociatedDevices1.png)](./media/howto-manage-devices/UnassociatedDevices1.png#lightbox)
1. Vyberte zařízení, které chcete přidružit šablonu.
1. Klikněte na tlačítko **přidružit** možnost.
    [![Přidružení zařízení](./media/howto-manage-devices/UnassociatedDevices2.png)](./media/howto-manage-devices/UnassociatedDevices2.png#lightbox)
1. Vyberte šablonu ze seznamu dostupných šablon a klikněte na **přidružit** tlačítko.
1. Vybraná zařízení budou přesunuty v šabloně, příslušné zařízení.

 >   [!NOTE] 
    Jakmile zařízení už není přidružena k šabloně nemůže být zrušeno přiřazení nebo související s jinou šablonu.

## <a name="export-devices"></a>Export zařízení

Zřízení zařízení pro připojení k centrální IoT, budete potřebovat řetězec připojení zařízení, je generován střed IoT. Funkce exportu můžete získat připojovací řetězce a dalších vlastností zařízení hromadné z vaší aplikace. Export vytvoří soubor CSV s identitu zařízení, název zařízení a primární připojovací řetězec pro vybraná zařízení.

Chcete-li hromadně export zařízení z vaší aplikace:
1. Zvolte **Explorer** v levé navigační nabídce.

1. Na levém panelu zvolte šablonu zařízení, pro který chcete exportovat zařízení.

1. Vyberte zařízení, která chcete exportovat a klikněte **exportovat** akce.

    [![Export](./media/howto-manage-devices/Export1.png)](./media/howto-manage-devices/Export1.png#lightbox)

1. Spustí proces exportu a může sledovat stav v horní části mřížky. 

1. Po dokončení exportu se zobrazí zpráva o úspěšném provedení spolu s odkazem ke stažení vygenerovaný soubor.

1. Klikněte na **zprávu o úspěchu** soubor stáhnout do místní složky na disku.

    [![Export úspěch](./media/howto-manage-devices/Export2.png)](./media/howto-manage-devices/Export2.png#lightbox)

1. Exportovaný soubor CSV bude mít následující informace:
    1. Název
    1. ID zařízení
    1. Primární připojovací řetězec


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
