---
title: Připojení zařízení jádro IoT Windows do aplikace Azure IoT centrální | Microsoft Docs
description: Jako vývojář zařízení zjistěte, jak se připojit k aplikaci Azure IoT centrální zařízení s MXChip IoT DevKit.
services: iot-central
author: miriamb
ms.author: mriamb
ms.date: 04/09/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: peterpr
ms.openlocfilehash: c38231f97eeb1c4511702bf3e788f72918cab045
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Jádro IoT Windows zařízení připojit k Azure IoT centrální aplikace

Tento článek popisuje, jak jako vývojář zařízení pro připojení k aplikaci Microsoft Azure IoT centrální zařízení IoT jádro systému Windows.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku budete potřebovat následující:

1. Azure IoT centrální aplikace vytvořené pomocí **Devkits ukázka** šablony aplikace. Další informace najdete v tématu [vytvořit aplikaci Azure IoT centrální](howto-create-application.md).
2. Zařízení se systémem Windows 10 IoT Core operačního systému. V tomto návodu použijeme malin platformy

Aplikace vytvořené z **ukázka Devkits** obsahuje šablony aplikace **jádro IoT Windows** šablona zařízení s následujícími charakteristikami:

### <a name="telemetry-measurements"></a>Měření telemetrie

| Název pole     | Jednotky  | Minimální | Maximum | Desetinných míst |
| -------------- | ------ | ------- | ------- | -------------- |
| vlhkosti       | %      | 0       | 100     | 0              |
| dočasné           | ° C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Nastavení

Číselné nastavení

| Zobrazované jméno | Název pole | Jednotky | Desetinných míst | Minimální | Maximum | Počáteční |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Ventilátor rychlosti    | fanSpeed   | OT. / MIN   | 0              | 0       | 1000    | 0       |


### <a name="properties"></a>Vlastnosti

| Typ            | Zobrazované jméno | Název pole | Typ dat |
| --------------- | ------------ | ---------- | --------- |
| Vlastnosti zařízení | Kostka číslo   | dieNumber  | číslo    |
| Text            | Umístění     | location   | neuvedeno       |

## <a name="add-a-real-device"></a>Přidání skutečné zařízení

V aplikaci Azure IoT centrální přidat skutečné zařízení z **jádro IoT Windows** šablona zařízení a je zaznamenána připojovací řetězec zařízení. Další informace najdete v tématu [přidat skutečné zařízení Azure IoT centrální aplikace](tutorial-add-device.md).

### <a name="prepare-the-windows-iot-core-device"></a>Připravte zařízení IoT jádro systému Windows

Nastavit jádro IoT Windows zařízení postupujte podle průvodce krok za krokem v [nastavení zařízení IoT jádro systému Windows] (https://github.com/Microsoft/microsoft-iot-central-firmware/tree/master/WindowsIoT#setup-a-physical-device).

### <a name="add-a-real-device"></a>Přidání skutečné zařízení

V aplikaci Azure IoT centrální přidat skutečné zařízení z **jádro IoT Windows** šablona zařízení a je zaznamenána připojovací řetězec zařízení. Další informace najdete v tématu [přidat skutečné zařízení Azure IoT centrální aplikace](tutorial-add-device.md).

## <a name="prepare-the-windows-10-iot-core-device"></a>Připravte zařízení jádro IoT Windows 10

### <a name="what-youll-need"></a>Co budete potřebovat

Pokud chcete nastavit fyzické zařízení jádro IoT Windows 10, musíte napřed zařízení se systémem Windows 10 IoT Core. Zjistěte, jak nastavit zařízení s Windows 10 IoT Core [zde](https://developer.microsoft.com/en-us/windows/iot/getstarted/prototype/setupdevice).

Budete také potřebovat klientskou aplikaci, která může komunikovat se službou Azure IoT centrální. Můžete buď vytvořit své vlastní aplikaci pomocí sady Azure SDK a nasadíte ho do zařízení pomocí sady Visual Studio, nebo si můžete stáhnout [předdefinovaných ukázka](https://developer.microsoft.com/en-us/windows/iot/samples) a jednoduše nasaďte a spusťte ho na zařízení. 

### <a name="deploying-the-sample-client-application"></a>Nasazení ukázkové aplikace klienta

K nasazení aplikace klienta z předchozího kroku do zařízení s Windows 10 IoT Chcete-li připravit:

**Ujistěte se, že připojovací řetězec je uložený na zařízení pro klientskou aplikaci používat**
* Na ploše uložte do textového souboru s názvem connection.string.iothub připojovací řetězec.
* Textový soubor zkopírujte do složky dokumentů zařízení: `[device-IP-address]\C$\Data\Users\DefaultAccount\Documents\connection.string.iothub`

Jakmile provedete tento krok, budete muset otevřít [portál zařízení Windows](https://docs.microsoft.com/en-us/windows/iot-core/manage-your-device/deviceportal) zadáním http://[device-IP-address]:8080 do libovolného prohlížeče.

Z tam a, jak ukazuje Pokud níže budete chtít:
1. Rozbalte uzel "Aplikací" na levé straně.
2. Klikněte na tlačítko "rychlé spuštění ukázky".
3. Klikněte na tlačítko "Klient Azure IoT Hub".
4. Klikněte na tlačítko "Nasadit a spustit".

![GIF klienta Azure IoT Hub v portálu pro zařízení Windows](./media/howto-connect-windowsiotcore/iothubapp.gif)

Po úspěšné, bude aplikace spustí na zařízení a vypadat takto:

![Snímek obrazovky Azure IoT Hub klientské aplikace](./media/howto-connect-windowsiotcore/IoTHubForegroundClientScreenshot.png)

V Azure IoT centrální můžete zjistit, jak kód spuštěný na platformy malin komunikuje s aplikací:

* Na **měření** stránky pro skutečné zařízení, můžete zobrazit telemetrii.
* Na **vlastnosti** stránky, se zobrazí hodnota hlášené vlastnosti kostka číslo.
* Na **nastavení** stránky, můžete změnit různá nastavení na platformy malin například napětí a ventilátor rychlosti.

## <a name="download-the-source-code"></a>Stáhnout zdrojový kód

Pokud chcete prozkoumat a upravit zdrojový kód pro klientskou aplikaci, můžete ho stáhnout z webu GitHub [zde](https://github.com/Microsoft/Windows-iotcore-samples/tree/develop/Samples/Azure/IoTHubClients). Pokud máte v úmyslu změnit kód, postupujte podle těchto pokynů v souboru readme [sem](https://github.com/Microsoft/Windows-iotcore-samples) plochy operačního systému.

> [!NOTE]
> Pokud **git** není nainstalován ve vašem vývojovém prostředí si můžete stáhnout z [ https://git-scm.com/download ](https://git-scm.com/download).
