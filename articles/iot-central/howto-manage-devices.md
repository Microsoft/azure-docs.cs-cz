---
title: Správa zařízení v aplikaci Azure IoT Central | Dokumentace Microsoftu
description: Jakožto Obsluha zjistěte, jak spravovat zařízení v aplikaci Azure IoT Central.
author: ellenfosborne
ms.author: elfarber
ms.date: 11/02/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: dc241612149de5c4ea5c1d2e698741e77d429fc7
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51004879"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Správa zařízení v aplikaci Azure IoT Central

Tento článek popisuje, jak jako operátor ke správě zařízení v aplikaci Azure IoT Central. Jakožto Obsluha můžete:

- Použití **Device Explorer** stránky k zobrazení, přidání a odstranění zařízení připojená k vaší aplikaci Azure IoT Central.
- Zachovat aktuální inventář zařízení.
- Vaše metadata zařízení aktualizovat tak, že změníte hodnotám uloženým ve vlastnostech zařízení.
- Řízení chování zařízení aktualizací nastavení do konkrétních zařízení **nastavení** stránky.

## <a name="view-your-devices"></a>Zobrazení zařízení

Chcete-li zobrazit jednotlivá zařízení:

1. Zvolte **Device Explorer** v levé navigační nabídce. Tady se zobrazí seznam vašich [šablon](howto-set-up-template.md).

1. V levém podokně vyberte šablonu zařízení v rámci šablony.

1. V pravém podokně stránky Device Explorer zobrazí se seznam zařízení, které jsou vytvořené z této šablony zařízení, jak je znázorněno níže. Zvolte k individuálnímu zařízení a najdete na stránce Podrobnosti o zařízení pro toto zařízení:

    [![Stránka Podrobnosti zařízení](./media/howto-manage-devices/image1.png)](./media/howto-manage-devices/image1.png#lightbox)

## <a name="add-a-device"></a>Přidání zařízení

Přidání zařízení do aplikace Azure IoT Central:

1. Zvolte **Device Explorer** v levé navigační nabídce.

1. Výběr šablony zařízení, ze kterého chcete vytvořit zařízení.

1. Zvolte + **nové**.

1. Zvolte **skutečné** nebo **simulované**. Skutečné zařízení je u fyzických zařízení, která připojení k aplikaci Azure IoT Central. Simulované zařízení obsahuje ukázková data generovaná pro vás Azure IoT Central. Tento příklad používá skutečné zařízení. Zvolte **skutečné** přejděte **podrobnosti o zařízení** stránku pro nové zařízení.


## <a name="import-devices"></a>Importovat zařízení

K připojení velkého počtu zařízení pro vaši aplikaci, Azure IoT Central nabídky hromadně importovat zařízení prostřednictvím souboru CSV. Soubor CSV by měl obsahovat následující sloupce (a hlavičky)
1.  IOTC_DeviceID  **<span style="color:Red">(by měl být malými písmeny)</span>**
1.  IOTC_DeviceName (volitelné)


K hromadné registraci zařízení ve vaší aplikaci:

1. Zvolte **Device Explorer** v levé navigační nabídce.

1. Na levém panelu vyberte šablonu zařízení, pro které chcete hromadně vytvořit zařízení.

 >   [!NOTE] 
    Pokud nemáte šablonu zařízení, ale pak můžete importovat zařízení v rámci **zrušeno přiřazení zařízení** a jejich registrace bez jakékoli šablony. Po importu zařízení, můžete pak přidružit k jejich šablony jako další krok.

1. Klikněte na **Importovat**.

    [![Akce importu](./media/howto-manage-devices/BulkImport1.png)](./media/howto-manage-devices/BulkImport1.png#lightbox)

1. Vyberte soubor CSV obsahující seznam ID zařízení k importu.

1. Importovat zařízení se začne tento soubor se odeslal. Můžete sledovat stav importu v horní části stránky mřížky zařízení.

1. Po dokončení importu zprávu o úspěšném dokončení se zobrazí v mřížce zařízení.

    [![Importovat úspěch](./media/howto-manage-devices/BulkImport3.png)](./media/howto-manage-devices/BulkImport3.png#lightbox)

Pokud zařízení importovat operace se nezdaří, zobrazí se chybová zpráva v mřížce zařízení. Soubor protokolu zachytávání všechny chyby se vygeneruje a kliknutím na chybové zprávy si můžete stáhnout.


**Přidružení zařízení pomocí šablony**

Když si zaregistrujete zařízení spuštěním importovat v části **zrušeno přiřazení zařízení**, pak zařízení jsou vytvořeny bez jakékoli šablony přidružení zařízení. Zařízení musí být spojen se šablonou a podívejte se data a další podrobnosti o zařízení. Použijte následující postup zařízení přidružit šablonu:
1. Zvolte **Device Explorer** v levé navigační nabídce.
1. Na levém panelu, vyberte **zrušeno přiřazení zařízení**.
    [![Nepřidružené zařízení](./media/howto-manage-devices/UnassociatedDevices1.png)](./media/howto-manage-devices/UnassociatedDevices1.png#lightbox)
1. Vyberte zařízení, které chcete přidružit k šabloně.
1. Klikněte na tlačítko **přidružit** možnost.
    [![Přidružení zařízení](./media/howto-manage-devices/UnassociatedDevices2.png)](./media/howto-manage-devices/UnassociatedDevices2.png#lightbox)
1. Vyberte šablonu ze seznamu dostupných šablon a klikněte na tlačítko **přidružit** tlačítko.
1. Vybraná zařízení se přesunou v šabloně příslušných zařízení.

 >   [!NOTE] 
    Jakmile se zařízení po přidružený k šabloně nemůže být zrušeno přiřazení nebo související s jinou šablonou.

## <a name="export-devices"></a>Export zařízení

Ke zřízení zařízení pro připojení k IoT Central, budete potřebovat připojovací řetězec zařízení, který je generován IoT Central. Chcete-li získat připojovací řetězce a další vlastnosti zařízení hromadné z vaší aplikace můžete použít funkce exportu. Export vytvoří soubor CSV s identitou zařízení, název zařízení a primární připojovací řetězec pro všechna vybraná zařízení.

Chcete-li hromadně exportovat zařízení z vaší aplikace:
1. Zvolte **Device Explorer** v levé navigační nabídce.

1. Na levém panelu vyberte šablonu zařízení, pro který chcete exportovat zařízení.

1. Vyberte zařízení, která chcete exportovat a pak klikněte na tlačítko **exportovat** akce.

    [![Export](./media/howto-manage-devices/Export1.png)](./media/howto-manage-devices/Export1.png#lightbox)

1. Spustí proces exportu a může sledovat stav v horní části stránky mřížky. 

1. Po dokončení exportu zprávu o úspěšném dokončení se zobrazí spolu s odkazem na stažení generovaného souboru.

1. Klikněte na **zpráva o úspěchu** stáhněte soubor do místní složky na disku.

    [![Export úspěch](./media/howto-manage-devices/Export2.png)](./media/howto-manage-devices/Export2.png#lightbox)

1. Vyexportovaný soubor CSV bude mít informace následující sloupce: **Id zařízení, název zařízení, zařízení Priamry/sekundárního klíče a primárního a sekundárního certifikátu thumbrpints**
    *   IOTC_DEVICEID
    *   IOTC_DEVICENAME
    *   IOTC_SASKEY_PRIMARY
    *   IOTC_SASKEY_SECONDARY
    *   IOTC_X509THUMBPRINT_PRIMARY 
    *   IOTC_X509THUMBPRINT_SECONDARY

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

1. Upravte nastavení na požadované hodnoty. Můžete upravit nastavení více najednou a aktualizovat všechny najednou.

1. Zvolte **aktualizace**. Jsou tyto hodnoty odeslány do vašeho zařízení. Když zařízení potvrdí změny nastavení, stav nastavení přejde zpět do **synchronizované**.

## <a name="change-a-property"></a>Změnit vlastnosti

Vlastnosti jsou metadata zařízení přidružená k zařízení, jako je například city a sériové číslo. Můžete zobrazit a aktualizovat vlastnosti na **podrobnosti o zařízení** stránky.

1. Zvolte **Device Explorer** v navigační nabídce.

1. Výběr šablony zařízení zařízení, jehož vlastnosti chcete změnit.

1. Zvolte **vlastnosti** kartu, kde uvidíte všechny vlastnosti.

1. Upravte vlastnosti aplikace, které mají požadované hodnoty. Můžete změnit více vlastností najednou a aktualizovat všechny najednou. Zvolte **aktualizace**.

> [!NOTE]
> Nelze změnit hodnotu _vlastnosti zařízení_. Vlastnosti zařízení jsou nastaveny podle zařízení a jsou jen pro čtení v aplikaci Azure IoT Central.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak spravovat zařízení v aplikaci Azure IoT Central, tady je navrhované další krok:

> [!div class="nextstepaction"]
> [Použití sady zařízení](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
