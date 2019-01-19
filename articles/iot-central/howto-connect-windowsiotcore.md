---
title: Připojit zařízení s Windows IoT Core do aplikace Azure IoT Central | Dokumentace Microsoftu
description: Jako vývojář zařízení zjistěte, jak připojit zařízení MXChip IoT DevKit do aplikace Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ff45ecc00d1c5c810015c8007d194b882f32f3da
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2019
ms.locfileid: "54412564"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Připojit zařízení s Windows IoT Core do aplikace Azure IoT Central

Tento článek popisuje, jak jako vývojář zařízení pro zařízení s Windows IoT Core připojit k aplikaci Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Před zahájením

K dokončení kroků v tomto článku budete potřebovat následující:

1. Azure IoT Central aplikace vytvořené z **ukázka Devkits** šablony aplikace. Další informace najdete v [rychlém startu k vytvoření aplikace](quick-deploy-iot-central.md).
2. Zařízení s operačním systémem Windows 10 IoT Core. V tomto návodu budeme používat Raspberry Pi.


## <a name="sample-devkits-application"></a>**Ukázkový Devkits** aplikace

Aplikace vytvořené z **ukázka Devkits** zahrnuje šablony aplikace **Windows IoT Core** šablona zařízení s následujícími charakteristikami: 

- Telemetrická data, která obsahuje měření pro zařízení **vlhkosti**, **teploty** a **tlak**. 
- Nastavení zobrazení **ventilátor rychlost**.
- Vlastnosti obsahující vlastnosti zařízení **kostka číslo** a **umístění** cloudové vlastnosti.


Najdete všechny podrobnosti o konfiguraci zařízení šablony [Podrobnosti šablony zařízení Windows IoT Core](howto-connect-windowsiotcore.md#windows-iot-core-device-template-details)

## <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT Central přidat z reálného zařízení **Windows IoT Core** šablona zařízení a zkontrolujte poznamenejte si připojovací řetězec zařízení. Další informace najdete v tématu [skutečné zařízení přidat do aplikace Azure IoT Central](tutorial-add-device.md).

### <a name="prepare-the-windows-iot-core-device"></a>Připravit zařízení Windows IoT Core

Nastavení zařízení s Windows IoT Core postupujte podle podrobného průvodce na [nastavit zařízení s Windows IoT Core](https://github.com/Azure/iot-central-firmware/tree/master/WindowsIoT#setup-a-physical-device).

### <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT Central přidat z reálného zařízení **Windows IoT Core** šablona zařízení a zaznamenání podrobností o připojení zařízení (**primární klíč ID oboru, ID zařízení**). Další informace najdete v tématu [skutečné zařízení přidat do aplikace Azure IoT Central](tutorial-add-device.md).

 > [!NOTE]
   > Azure IoT Central převedl na používání služby Azure IoT Hub Device Provisioning (DPS) pro všechna připojení zařízení, postupujte podle těchto instrustions k [získat připojovací řetězec zařízení](concepts-connectivity.md#getting-device-connection-string) a pokračujte se zbývající část tohoto kurzu.

## <a name="prepare-the-windows-10-iot-core-device"></a>Připravit zařízení Windows 10 IoT Core

### <a name="what-youll-need"></a>Co budete potřebovat

Pokud chcete nastavit skutečné zařízení Windows 10 IoT Core, musíte nejprve mít zařízení se systémem Windows 10 IoT Core. Zjistěte, jak nastavit zařízení s Windows 10 IoT Core [tady](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup).

Budete také potřebovat klientské aplikace, který může komunikovat s Azure IoT Central. Můžete sestavit vlastní aplikaci pomocí sady Azure SDK a nasaďte ji do zařízení pomocí sady Visual Studio, nebo si můžete stáhnout [předem připravené ukázky](https://developer.microsoft.com/windows/iot/samples) a jednoduše nasadit a spustit ho v zařízení. 

### <a name="deploying-the-sample-client-application"></a>Nasazení ukázkové aplikace klienta

Pokud chcete nasadit aplikaci klienta z předchozího kroku do zařízení Windows 10 IoT aby bylo možné připravit:

**Ujistěte se, že je připojovací řetězec je uložen na zařízení pro klientskou aplikaci používat**
* Na ploše uložte do textového souboru s názvem connection.string.iothub připojovací řetězec.
* Zkopírujte tento textový soubor do složku dokumentů zařízení: `[device-IP-address]\C$\Data\Users\DefaultAccount\Documents\connection.string.iothub`

Jakmile, který jste provedli, budete muset otevřít [Windows Device Portal](https://docs.microsoft.com/windows/iot-core/manage-your-device/deviceportal) tak, že zadáte http://[device-IP-address]:8080 do libovolného prohlížeče.

Z zde a, jak ukazuje if níže budete chtít:
1. Rozbalte uzel "Aplikace" na levé straně.
2. Klikněte na tlačítko "rychlého spuštění ukázky".
3. Klikněte na tlačítko "Klient služby Azure IoT Hub".
4. Klikněte na tlačítko "Nasadit a spustit".

![GIF klienta služby Azure IoT Hub na Windows Device Portal](./media/howto-connect-windowsiotcore/iothubapp.gif)

V případě úspěchu aplikace spustí na zařízení a vypadat nějak takto:

![Snímek obrazovky s Azure IoT Hub klientské aplikace](./media/howto-connect-windowsiotcore/IoTHubForegroundClientScreenshot.png)

V Azure IoT Central uvidíte, jak kód spuštěný na Raspberry Pi komunikuje s aplikací:

* Na **měření** stránky pro skutečné zařízení, zobrazí se telemetrie.
* Na **vlastnosti** stránky, můžete zobrazit hodnotu ohlášené vlastnosti kostka číslo.
* Na **nastavení** stránky, můžete změnit různá nastavení na Raspberry Pi, jako je například napětí a podporuje a rychlost.

## <a name="download-the-source-code"></a>Stáhněte si zdrojový kód

Pokud chcete prozkoumat a upravit zdrojový kód pro klientskou aplikaci, si můžete stáhnout z Githubu [tady](https://github.com/Microsoft/Windows-iotcore-samples/tree/develop/Samples/Azure/IoTHubClients). Pokud budete chtít upravit kód, postupujte podle těchto pokynů v souboru readme [tady](https://github.com/Microsoft/Windows-iotcore-samples) pro desktopové operační systém.

> [!NOTE]
> Pokud **git** není nainstalovaný ve vašem vývojovém prostředí, můžete ji stáhnout [ https://git-scm.com/download ](https://git-scm.com/download).

## <a name="windows-iot-core-device-template-details"></a>Podrobnosti šablony zařízení Windows IoT Core

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

| Typ            | Zobrazované jméno | Název pole | Typ dat |
| --------------- | ------------ | ---------- | --------- |
| Vlastnosti zařízení | Kostka čísla   | dieNumber  | číslo    |
| Text            | Umístění     | location   | neuvedeno       |
