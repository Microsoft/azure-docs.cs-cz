---
title: Připojit zařízení s Windows IoT Core do aplikace Azure IoT Central | Dokumentace Microsoftu
description: Jako vývojář zařízení zjistěte, jak připojit zařízení MXChip IoT DevKit do aplikace Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: af6d66d2e3eae80477a151323578b930dcd7727a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60886607"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Připojit zařízení s Windows IoT Core do aplikace Azure IoT Central

Tento článek popisuje, jak jako vývojář zařízení pro zařízení s Windows IoT Core připojit k aplikaci Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku budete potřebovat následující:

- Azure IoT Central aplikace vytvořené z **ukázka Devkits** šablony aplikace. Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).

- Zařízení s operačním systémem Windows 10 IoT Core. Další informace najdete v tématu [nastavení zařízení s Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

- Vývojovém počítači s [Node.js](https://nodejs.org/) verze 8.0.0 nebo novější. Můžete spustit `node --version` na příkazovém řádku k ověření verze. Node.js je k dispozici pro širokou škálu operačních systémů.

## <a name="the-sample-devkits-application"></a>Devkits ukázkové aplikace

Aplikace vytvořené z **ukázka Devkits** zahrnuje šablony aplikace **Windows IoT Core** šablona zařízení s následujícími charakteristikami:

- Měření telemetrická data pro zařízení: **Vlhkosti**, **teploty**, a **tlak**.
- Nastavení pro ovládací prvek **ventilátor rychlost**.
- Vlastnosti zařízení **kostka číslo** a vlastnosti cloudu **umístění**.

Úplné podrobnosti o konfiguraci zařízení šablony najdete v tématu [Podrobnosti šablony zařízení Windows IoT Core](#device-template-details).

## <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT Central, použijte **Device Explorer** stránku z reálného zařízení přidáte **Windows 10 IoT Core** šablona zařízení. Poznamenejte si zařízení, podrobnosti o připojení (**ID oboru**, **ID zařízení**, a **primární klíč**). Další informace najdete v tématu [získat informace o připojení](howto-generate-connection-string.md#get-connection-information).

## <a name="prepare-the-device"></a>Připravte zařízení

U zařízení pro připojení k IoT Central potřebuje připojovací řetězec.

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

Pro kód zařízení pro přístup k připojovací řetězec uložit do souboru s názvem **connection.string.iothub** ve složce `C:\Data\Users\DefaultAccount\Documents\` na zařízení s Windows 10 IoT Core.

Pro kopírování **connection.string.iothub** soubor ze stolního počítače k `C:\Data\Users\DefaultAccount\Documents\` složky na vašem zařízení, můžete použít [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Pomocí webového prohlížeče přejděte na Windows Device Portal na vašem zařízení.
1. Chcete-li procházet soubory na vašem zařízení, zvolte **aplikace > Průzkumníka souborů**.
1. Přejděte do **uživatele Folders\Documents**. Nahrajte **connection.string.iothub** souboru:

    ![Uložit připojovací řetězec](media/howto-connect-windowsiotcore/device-portal.png)

## <a name="deploy-and-run"></a>Nasazení a spuštění

K nasazení a spuštění ukázkové aplikace ve vašem zařízení, můžete použít [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

1. Pomocí webového prohlížeče přejděte na Windows Device Portal na vašem zařízení.
1. Nasadit a spustit **Azure IoT Hub Client** aplikaci, zvolte **aplikace > Rychlé spuštění ukázky**. Klikněte na tlačítko **Azure IoT Hub Client**.
1. Klikněte na tlačítko **nasadit a spustit**.

    ![Nasazení a spuštění](media/howto-connect-windowsiotcore/quick-run.png)

Za pár minut můžete zobrazit telemetrická data z vašeho zařízení ve vaší aplikaci IoT Central.

[Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) obsahuje nástroje, které vám umožní potíží se zařízením:

- **Aplikace správce** stránky umožňuje řídit aplikace běžící na vašem zařízení.
- Pokud nemáte k dispozici monitorování připojení k zařízení, můžete použít **nastavení zařízení** stránky k zachycení snímků obrazovky ze zařízení. Příklad:

    ![Snímek obrazovky aplikace](media/howto-connect-windowsiotcore/iot-hub-foreground-client.png)

## <a name="download-the-source-code"></a>Stáhněte si zdrojový kód

Pokud chcete prozkoumat a upravit zdrojový kód pro klientskou aplikaci, můžete ji stáhnout [úložiště GitHub pro Windows-iotcore-samples](https://github.com/Microsoft/Windows-iotcore-samples/blob/master/Samples/Azure/IoTHubClients).

## <a name="device-template-details"></a>Podrobnosti o zařízení šablony

Aplikace vytvořené z **ukázka Devkits** zahrnuje šablony aplikace **Windows IoT Core** šablona zařízení s následujícími charakteristikami:

### <a name="telemetry-measurements"></a>Měření telemetrie

| Název pole     | Jednotky  | Minimální | Maximum | Desetinná místa |
| -------------- | ------ | ------- | ------- | -------------- |
| vlhkost       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| tlak       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Nastavení

Číselné nastavení

| Zobrazované jméno | Název pole | Jednotky | Desetinná místa | Minimální | Maximum | Počáteční |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Ventilátor rychlost    | fanSpeed   | OT. / MIN   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>Vlastnosti

| Type            | Zobrazované jméno | Název pole | Typ dat |
| --------------- | ------------ | ---------- | --------- |
| Vlastnosti zařízení | Kostka čísla   | dieNumber  | číslo    |
| Text            | Location     | location   | neuvedeno       |

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak se připojit k aplikaci Azure IoT Central Raspberry Pi, navrhované dalším krokem je další způsob [nastavit šablonu vlastního zařízení](howto-set-up-template.md) pro zařízení IoT.