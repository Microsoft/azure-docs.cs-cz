---
title: Připojení Windows IoT Core k aplikaci Azure IoT Central | Microsoft Docs
description: Jako vývojář zařízení se naučíte připojit zařízení MXChip IoT DevKit k vaší aplikaci Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 452d18908406214bb7e1253363a42d8ba8287d96
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454050"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Připojení zařízení s Windows IoT Core k aplikaci Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Tento článek popisuje, jak jako vývojář zařízení připojit zařízení s Windows IoT Core k vaší aplikaci Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku budete potřebovat následující:

- Aplikace Azure IoT Central vytvořená ze šablony **starší verze aplikace** . Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).

- Zařízení s operačním systémem Windows 10 IoT Core. Další informace najdete v tématu [nastavení zařízení s Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

- Vývojový počítač s nainstalovanou aplikací [Node. js](https://nodejs.org/) verze 8.0.0 nebo novější. Můžete spustit `node --version` na příkazovém řádku a ověřit svou verzi. Node.js je k dispozici pro širokou škálu operačních systémů.

## <a name="add-a-device-template"></a>Přidání šablony zařízení

V aplikaci Azure IoT Central přidejte novou šablonu zařízení **Windows IoT Core** s následujícími charakteristikami:

- Měření telemetrie pro zařízení: **vlhkost**, **teplota**a **tlak**.
- Nastavení pro řízení **rychlosti ventilátoru**.
- **Číslo** a **umístění**vlastnosti v zařízení, které je v cloudu.

1. Z šablon zařízení ![vyberte **+ nový**](media/howto-connect-windowsiotcore/adddevicetemplate.png)
   

2. Vyberte **Windows IoT Core** a vytvořte šablonu zařízení Windows iot Core ![přidat šablonu zařízení](media/howto-connect-windowsiotcore/newdevicetemplate.png)

Úplné podrobnosti o konfiguraci šablony zařízení najdete v [podrobnostech o šabloně zařízení Windows IoT Core](#device-template-details).

## <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT Central můžete pomocí stránky **Device Explorer** přidat reálné zařízení ze šablony zařízení s **Windows 10 IoT Core** . Poznamenejte si podrobnosti o připojení zařízení (**ID oboru**, **ID zařízení**a **primární klíč**).

## <a name="prepare-the-device"></a>Příprava zařízení

Aby se zařízení mohlo připojit k IoT Central, potřebuje připojovací řetězec:

1. K vygenerování připojovacího řetězce použijte `dps-keygen` nástroj příkazového řádku:

    Chcete-li nainstalovat [Nástroj Generátor klíčů](https://github.com/Azure/dps-keygen), spusťte následující příkaz:

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. Pokud chcete vygenerovat připojovací řetězec, spusťte následující příkaz s podrobnostmi připojení, které jste si poznamenali dříve:

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. Zkopírujte připojovací řetězec z výstupu `dps-keygen`, který se má použít ve vašem kódu zařízení.

Aby kód zařízení měl přístup k připojovacímu řetězci, uložte ho do souboru s názvem **Connection. String. iothub** do složky `C:\Data\Users\DefaultAccount\Documents\` na zařízení s Windows 10 IoT Core.

K zkopírování souboru **Connection. String. iothub** z počítače do složky `C:\Data\Users\DefaultAccount\Documents\` na zařízení můžete použít [portál zařízení Windows](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Pomocí webového prohlížeče přejděte na portál zařízení Windows na zařízení.
1. Pokud chcete procházet soubory na zařízení, vyberte **aplikace > Průzkumníku souborů**.
1. Přejděte na **Folders\Documents uživatele**. Pak nahrajte soubor **Connection. iothub** :

    ![Nahrát připojovací řetězec](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>Nasazení a spuštění

Pokud chcete nasadit a spustit ukázkovou aplikaci na svém zařízení, můžete použít [portál zařízení Windows](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Pomocí webového prohlížeče přejděte na portál zařízení Windows na zařízení.
1. Pokud chcete nasadit a spustit **klientskou aplikaci Azure IoT Hub** , vyberte **aplikace > ukázek pro rychlé spuštění**. Pak zvolte **klient služby Azure IoT Hub**.
1. Pak zvolte **nasadit a spustit**.

    ![Nasazení a spuštění](media/howto-connect-windowsiotcore/quick-run.png)

Po několika minutách můžete v aplikaci IoT Central zobrazit telemetrii ze svého zařízení.

[Portál zařízení s Windows](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) obsahuje nástroje, které můžete použít k řešení potíží s vaším zařízením:

- Stránka **Správce aplikací** vám umožní řídit aplikace běžící na vašem zařízení.
- Pokud nemáte monitor připojený k vašemu zařízení, můžete na stránce **nastavení zařízení** zachytit snímky obrazovky ze svého zařízení. Příklad:

    ![Snímek obrazovky aplikace](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>Stažení zdrojového kódu

Pokud chcete prozkoumat a upravit zdrojový kód klientské aplikace, můžete si ho stáhnout z [úložiště GitHub Windows-iotcore-Samples](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients).

## <a name="device-template-details"></a>Podrobnosti o šabloně zařízení

Aplikace vytvořená v ukázkové šabloně aplikace **Devkits** zahrnuje šablonu zařízení **Windows IoT Core** s následujícími charakteristikami:

### <a name="telemetry-measurements"></a>Měření telemetrie

| Název pole     | Jednotky  | Minimální | Maximum | Desetinná místa |
| -------------- | ------ | ------- | ------- | -------------- |
| vlhkost       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| tlak       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Nastavení

Číselná nastavení

| Zobrazované jméno | Název pole | Jednotky | Desetinná místa | Minimální | Maximum | Počáteční |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Rychlost větráku    | fanSpeed   | /MIN   | 0              | 0       | 1 000    | 0       |

### <a name="properties"></a>Vlastnosti

| Typ            | Zobrazované jméno | Název pole | Data type |
| --------------- | ------------ | ---------- | --------- |
| Vlastnost zařízení | Číslo Die   | dieNumber  | číslo    |
| Text            | Umístění     | location   | Nevztahuje se       |

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili připojit zařízení Windows IoT Core k vaší aplikaci Azure IoT Central, je doporučený další krok, kde se dozvíte, jak [nastavit vlastní šablonu zařízení](howto-set-up-template.md) pro vlastní zařízení IoT.
