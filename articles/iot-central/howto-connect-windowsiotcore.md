---
title: Připojení zařízení s Windows IoT Core k aplikaci Azure IoT Central | Microsoft Docs
description: Jako vývojář zařízení se naučíte připojit zařízení MXChip IoT DevKit k vaší aplikaci Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: dca0146cc16ea63e0621eff8f508f15e0046b63b
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877391"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Připojení zařízení s Windows IoT Core k aplikaci Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Tento článek popisuje, jak jako vývojář zařízení připojit zařízení s Windows IoT Core k vaší aplikaci Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Před zahájením

K dokončení kroků v tomto článku budete potřebovat následující:

- Aplikace IoT Central v Azure vytvořená z **ukázkové** šablony aplikace Devkits Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).

- Zařízení s operačním systémem Windows 10 IoT Core. Další informace najdete v tématu [nastavení zařízení s Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

- Vývojový počítač s nainstalovanou aplikací [Node. js](https://nodejs.org/) verze 8.0.0 nebo novější. Můžete spustit `node --version` na příkazovém řádku a ověřit svou verzi. Node.js je k dispozici pro širokou škálu operačních systémů.

## <a name="the-sample-devkits-application"></a>Ukázková aplikace Devkits

Aplikace vytvořená v ukázkové šabloně aplikace **Devkits** zahrnuje šablonu zařízení **Windows IoT Core** s následujícími charakteristikami:

- Měření telemetrie pro zařízení: **Vlhkost**, **teplota**a **tlak**.
- Nastavení pro řízení **rychlosti ventilátoru**.
- **Číslo** a **umístění**vlastnosti v zařízení, které je v cloudu.

Úplné podrobnosti o konfiguraci šablony zařízení najdete v podrobnostech o [šabloně zařízení Windows IoT Core](#device-template-details).

## <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT Central můžete pomocí stránky **Device Explorer** přidat reálné zařízení ze šablony zařízení s **Windows 10 IoT Core** . Poznamenejte si podrobnosti o připojení zařízení (**ID oboru**, **ID zařízení**a **primární klíč**). Další informace najdete v tématu [získání informací o připojení](howto-generate-connection-string.md#get-connection-information).

## <a name="prepare-the-device"></a>Příprava zařízení

Aby se zařízení mohlo připojit k IoT Central, potřebuje připojovací řetězec.

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

Aby kód zařízení měl přístup k připojovacímu řetězci, uložte ho do souboru s názvem **Connection. String. iothub** ve složce `C:\Data\Users\DefaultAccount\Documents\` na zařízení s Windows 10 IoT Core.

K zkopírování souboru **Connection. String. iothub** z počítače do `C:\Data\Users\DefaultAccount\Documents\` složky v zařízení můžete použít [portál zařízení Windows](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal):

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
| názvem           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Nastavení

Číselná nastavení

| Display name | Název pole | Jednotky | Desetinná místa | Minimální | Maximum | Počáteční |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Rychlost ventilátoru    | fanSpeed   | /MIN   | 0              | 0       | 1000    | 0       |

### <a name="properties"></a>Vlastnosti

| type            | Display name | Název pole | Datový typ |
| --------------- | ------------ | ---------- | --------- |
| Vlastnost zařízení | Číslo Die   | dieNumber  | číslo    |
| Text            | Location     | location   | Není k dispozici       |

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili připojit zařízení Windows IoT Core k vaší aplikaci Azure IoT Central, je doporučený další krok, kde se dozvíte, jak [nastavit vlastní šablonu zařízení](howto-set-up-template.md) pro vlastní zařízení IoT.
