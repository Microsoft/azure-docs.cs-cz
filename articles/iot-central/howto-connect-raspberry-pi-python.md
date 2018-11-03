---
title: Connnect Raspberry Pi až po aplikace Azure IoT Central (Python) | Dokumentace Microsoftu
description: Jako vývojář zařízení jak se připojit na Raspberry Pi do aplikace Azure IoT Central pomocí Pythonu.
author: dominicbetts
ms.author: dobett
ms.date: 01/23/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: c478c2c4af12ecaa02e6700ec7faffc5b9862a00
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958181"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Připojte Raspberry Pi do aplikace Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Tento článek popisuje, jak jako vývojář zařízení připojit Raspberry Pi do aplikace Microsoft Azure IoT Central, pomocí programovacího jazyka Python.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku, budete potřebovat následující komponenty:

* Azure IoT Central aplikace vytvořené z **ukázka Devkits** šablony aplikace. Další informace najdete v tématu [vytvořit aplikaci rychlý Start](quick-deploy-iot-central.md).
* Raspberry Pi zařízení se systémem Raspbian operačního systému. Potřebujete monitoru, klávesnice a myši připojené k Raspberry Pi přístup k prostředí grafického uživatelského rozhraní. Raspberry Pi musí být schopen [připojení k Internetu](https://www.raspberrypi.org/learning/software-guide/wifi/).
* Volitelně můžete [smysl Hat](https://www.raspberrypi.org/products/sense-hat/) panel doplněk pro Raspberry Pi. Tento panel shromažďuje telemetrická data z různých snímačů a poslat do aplikace Azure IoT Central. Pokud nemáte k dispozici **smysl Hat** panelu, můžete místo toho použít emulátor (k dispozici jako součást image Raspberry Pi).

## <a name="sample-devkits-application"></a>**Ukázkový Devkits** aplikace

Aplikace vytvořené z **ukázka Devkits** zahrnuje šablony aplikace **Raspberry Pi** šablona zařízení s následujícími charakteristikami: 

- Telemetrická data, která zahrnuje následující měření, které zařízení bude shromažďovat:
    - Vlhkost
    - Teplota
    - Tlak
    - Magnetometer (X, Y, Z)
    - Akcelerometr (X, Y, Z)
    - Volný setrvačník (X, Y, Z)
- Nastavení
    - Snímač napětí
    - Aktuální
    - Ventilátor rychlost
    - Přepnout reakcí na Incidenty.
- Vlastnosti
    - Kostka vlastnost počtu zařízení
    - Vlastnost Location cloudu

Podrobnosti o konfiguraci zařízení šablony najdete v tématu [Podrobnosti šablony Raspberry PI zařízení](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details)
    

## <a name="add-a-real-device"></a>Přidání skutečného zařízení

V aplikaci Azure IoT Central přidat z reálného zařízení **Raspberry Pi** zařízení šablony a udržovat přehled o podrobnosti o připojení zařízení (**primární klíč ID oboru, ID zařízení**). Další informace najdete v tématu [skutečné zařízení přidat do aplikace Azure IoT Central](tutorial-add-device.md).


### <a name="configure-the-raspberry-pi"></a>Konfigurace Raspberry Pi

Následující kroky popisují, jak stáhnout a nakonfigurovat ukázkové aplikace v Pythonu z Githubu. Tato ukázková aplikace:

* Odesílá telemetrická data a hodnoty vlastností do Azure IoT Central.
* Reaguje na změny provedené v Azure IoT Central nastavení.

Ke konfiguraci zařízení, [postupujte podle podrobných pokynů na Githubu.](http://aka.ms/iotcentral-docs-Raspi-releases)


> [!NOTE]
> Další informace týkající se ukázky Raspberry Pi Pythonu najdete v tématu [README](http://aka.ms/iotcentral-docs-Raspi-releases) soubor na Githubu.


1. Po nakonfigurování zařízení by měla vaše zařízení zahájila odesílání dat do Azure IoT Central okamžik.
1. V aplikaci Azure IoT Central uvidíte, jak kód spuštěný na Raspberry Pi komunikuje s aplikací:

    * Na **měření** stránky pro skutečné zařízení, můžete zobrazit telemetrická data odesílaná z Raspberry Pi. Pokud používáte **smysl HAT emulátor**, můžete změnit hodnoty telemetrie v grafickém Uživatelském rozhraní na Raspberry Pi.
    * Na **vlastnosti** stránky, zobrazí se hodnota hlášení **kostka číslo** vlastnost.
    * Na **nastavení** stránky, můžete změnit různá nastavení na Raspberry Pi, jako je například napětí a podporuje a rychlost. Když je Raspberry Pi potvrdí změny, nastavení zobrazí jako **synchronizované** v Azure IoT Central.


## <a name="raspberry-pi-device-template-details"></a>Podrobnosti šablony Raspberry PI zařízení

Aplikace vytvořené z **ukázka Devkits** zahrnuje šablony aplikace **Raspberry Pi** šablona zařízení s následujícími charakteristikami:

### <a name="telemetry-measurements"></a>Měření telemetrie

| Název pole     | Jednotky  | Minimální | Maximum | Desetinná místa |
| -------------- | ------ | ------- | ------- | -------------- |
| vlhkost       | %      | 0       | 100     | 0              |
| temp           | ° C     | -40     | 120     | 0              |
| tlak       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Nastavení

Číselné nastavení

| Zobrazované jméno | Název pole | Jednotky | Desetinná místa | Minimální | Maximum | Počáteční |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Snímač napětí      | setVoltage | Intenzita | 0              | 0       | 240     | 0       |
| Aktuální      | setCurrent | A  | 0              | 0       | 100     | 0       |
| Ventilátor rychlost    | fanSpeed   | OT. / MIN   | 0              | 0       | 1000    | 0       |

Přepínací tlačítko Nastavení

| Zobrazované jméno | Název pole | Na text | Vypnout text | Počáteční |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | OFF      | Vypnuto     |

### <a name="properties"></a>Vlastnosti

| Typ            | Zobrazované jméno | Název pole | Typ dat |
| --------------- | ------------ | ---------- | --------- |
| Vlastnosti zařízení | Kostka čísla   | dieNumber  | číslo    |
| Text            | Umístění     | location   | neuvedeno       |

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak připojit Raspberry Pi do aplikace Azure IoT Central, tady jsou další navrhované kroky:

* [Připojit obecný klientská aplikace Node.js do Azure IoT Central](howto-connect-nodejs.md)
