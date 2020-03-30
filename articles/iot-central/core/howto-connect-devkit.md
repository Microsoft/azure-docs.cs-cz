---
title: Připojení zařízení DevKit k aplikaci Azure IoT Central | Dokumenty společnosti Microsoft
description: Jako vývojář zařízení se dozvíte, jak připojit zařízení MXChip IoT DevKit k aplikaci Azure IoT Central pomocí Technologie IoT Plug and Play (preview).
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: 0a393ae8629f1742002344ee717a6719269a6722
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158548"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Připojení zařízení MXChip IoT DevKit k aplikaci Azure IoT Central

Tento článek ukazuje, jak připojit zařízení MXChip IoT DevKit (DevKit) k aplikaci Azure IoT Central. Zařízení používá certifikovaný model IoT Plug and Play (preview) pro zařízení DevKit ke konfiguraci připojení k IoT Central.

V tomto článku s návody:

- Získejte podrobnosti o připojení z aplikace IoT Central.
- Připravte zařízení a připojte ho k aplikaci IoT Central.
- Zobrazení telemetrie a vlastnosti ze zařízení v IoT Central.

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto článku budete potřebovat následující:

- Zařízení [DevKit](https://aka.ms/iot-devkit-purchase).
- Aplikace IoT Central. Postup můžete sledovat v [tématu Vytvoření aplikace IoT Central](./quick-deploy-iot-central.md).

## <a name="get-device-connection-details"></a>Získání podrobností o připojení zařízení

1. V aplikaci Azure IoT Central vyberte kartu **Šablony zařízení** a vyberte **+ Nový**. V části **Použití předkonfigurované šablony zařízení**vyberte **MXChip IoT DevKit**.

    ![Šablona zařízení pro MXChip IoT DevKit](media/howto-connect-devkit/device-template.png)

1. Vyberte **další: Přizpůsobit** a potom **vytvořit**.

1. Vyberte **karta Zařízení.** V seznamu zařízení vyberte **MXChip IoT DevKit** a vyberte **+ Nový** pro vytvoření nového zařízení ze šablony.

    ![Nové zařízení](media/howto-connect-devkit/new-device.png)

1. V automaticky otevírané okno zadejte `SampleDevKit` **ID zařízení** jako a **název zařízení** jako `MXChip IoT DevKit - Sample`. Ujistěte se, že je **možnost Simulované** vypnuto. Pak vyberte **Vytvořit**.

    ![ID a název zařízení](media/howto-connect-devkit/device-id-name.png)

1. Vyberte zařízení, které jste vytvořili, a pak vyberte **Připojit**. Poznamenejte si **obor ID**, **ID zařízení**a **primární klíč**. Tyto hodnoty potřebujete dále v tomto článku s návody.

    ![Informace o připojení zařízení](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>Příprava zařízení

1. Stáhněte si nejnovější [předem sestavený firmware Azure IoT Central Plug and Play (preview)](https://github.com/Azure-Samples/mxchip-iot-devkit-pnp/raw/master/bin/iotc_devkit.bin) pro zařízení DevKit z GitHubu.

1. Připojte zařízení DevKit k vývojovému počítači pomocí kabelu USB. V systému Windows se otevře okno průzkumníka souborů na jednotce namapované na úložiště na zařízení DevKit. Jednotka může být například nazvána **AZ3166 (D:).**

1. Přetáhněte soubor **iotc_devkit.bin** do okna jednotky. Po dokončení kopírování se zařízení restartuje s novým firmwarem.

    > [!NOTE]
    > Pokud se na obrazovce zobrazují chyby, jako je **no Wi-Fi**, je to proto, že DevKit ještě nebyl připojen k Wi-Fi.

1. Na devKitu podržte **tlačítko B**, stiskněte a uvolněte tlačítko **Reset** a poté uvolněte **tlačítko B**. Zařízení je nyní v režimu přístupového bodu. Pro potvrzení se na obrazovce zobrazí "IoT DevKit - AP" a IP adresa konfiguračního portálu.

1. V počítači nebo tabletu se připojte k názvu Sítě Wi-Fi zobrazeného na obrazovce zařízení. WiFi síť začíná **az-** následuje MAC adresa. Když se připojíte k této síti, nemáte přístup k internetu. Tento stav je očekáván a při konfiguraci zařízení se k této síti připojujete pouze krátce.

1. Otevřete webový prohlížeč [http://192.168.0.1/](http://192.168.0.1/)a přejděte na . Zobrazí se následující webová stránka:

    ![Konfigurační umělá konfigurace](media/howto-connect-devkit/config-ui.png)

    Na webové stránce zadejte:

    - Název sítě WiFi (SSID).
    - Heslo k síti WiFi.
    - Podrobnosti o připojení: zadejte **ID zařízení**, **Obor ID**a **Primární klíč SAS,** který jste si dříve poznamenali.

    > [!NOTE]
    > V současné době se IoT DevKit může připojit pouze k 2,4 GHz Wi-Fi, 5 GHz není podporováno kvůli hardwarovým omezením.

1. Zvolte **Konfigurovat zařízení**, zařízení DevKit se restartuje a spustí aplikaci:

    ![Restartování ui](media/howto-connect-devkit/reboot-ui.png)

    Na obrazovce DevKit se zobrazí potvrzení, že je aplikace spuštěná:

    ![DevKit běží](media/howto-connect-devkit/devkit-running.png)

DevKit nejprve zaregistruje nové zařízení v aplikaci IoT Central a pak začne odesílat data.

## <a name="view-the-telemetry"></a>Zobrazení telemetrie

V tomto kroku zobrazíte telemetrická data v aplikaci Azure IoT Central.

V aplikaci IoT Central vyberte **Karta Zařízení** a vyberte zařízení, které jste přidali. Na kartě **Přehled** uvidíte telemetrii ze zařízení DevKit:

![Přehled zařízení IoT Central](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>Kontrola kódu

Chcete-li kód zkontrolovat nebo upravit a zkompilovat, přejděte na [ukázky kódu](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/).

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak připojit zařízení DevKit k aplikaci Azure IoT Central, je dalším doporučeným krokem naučit se [nastavit vlastní šablonu zařízení](./howto-set-up-template.md) pro vlastní zařízení IoT.
