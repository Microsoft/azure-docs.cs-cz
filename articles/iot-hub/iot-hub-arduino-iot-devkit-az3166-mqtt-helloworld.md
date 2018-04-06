---
title: Odeslání zprávy na server MQTT pomocí klientské knihovny Azure MQTT | Microsoft Docs.
description: Odeslání zprávy na MQTT server pomocí DevKit jako klient.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2018
ms.author: liydu
ms.openlocfilehash: 121bd8a21e74a836fcca74af97fc20cae297d9a2
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="send-messages-to-an-mqtt-server"></a>Odeslání zprávy na MQTT server

Internet věcí (IoT) systémy často řešit přerušované, nízký kvality nebo pomalé připojení k Internetu. MQTT je protokol připojení počítač počítače (M2M), která byla vyvinuta s přihlédnutím takové problémům. 

Použít tady MQTT klientské knihovny je součástí [Eclipse Panamerická](http://www.eclipse.org/paho/) projektu, který poskytuje rozhraní API pomocí MQTT přes více způsob přenosu.

## <a name="what-you-learn"></a>Co se naučíte

V tomto projektu dozvíte:
- Jak používat k odesílání zpráv do broker MQTT MQTT klientské knihovny.
- Postup konfigurace vaší Iot DevKit MXChip jako klient MQTT.

## <a name="what-you-need"></a>Co potřebujete

Dokončit [Příručka Začínáme](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) na:

* Mít vaše DevKit připojení Wi-Fi
* Příprava vývojového prostředí

## <a name="open-the-project-folder"></a>Otevřete složku projektu

1. Pokud je již připojen, odpojte DevKit z vašeho počítače.

2. Spustí kód VS.

3. Připojení DevKit k vašemu počítači.
    VS Code automaticky zjišťuje vaší DevKit a otevře na následujících stránkách:
    * Úvodní stránka DevKit.
    * Příklady Arduino: Praktických vzorků, které vám pomůžou začít s vaší DevKit.

    ![mini-solution-vscode](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/vscode-start.png)

## <a name="open-the-mqttclient-sample"></a>Otevřete ukázku MQTTClient

Rozbalte levé straně **ARDUINO příklady** vyhledejte **příklady MXCHIP AZ3166 > MQTT**a vyberte **MQTTClient**. Otevře se nové okno VS Code s složce projektu v ní.

![Příklady složky](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/examples.png)

> [!NOTE]
> Pokud jste dojít zavřete podokno, můžete ho znovu otevřít. Použití `Ctrl+Shift+P` (systému macOS: `Cmd+Shift+P`) Chcete-li spustit příkaz palety, zadejte **Arduino**a potom najděte a vyberte **Arduino: Příklady**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Vytvoření a odeslání nákresu Arduino do DevKit

Typ `Ctrl+P` (systému macOS: `Cmd+P`) ke spuštění `task device-upload`. Po dokončení nahrávání se DevKit restartuje a spustí nákresu.

![nahrávání zařízení](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Může se zobrazit "Chyba: AZ3166: Neznámý balíček" chybová zpráva. Tato chyba nastane, když není správně aktualizovat index balíčků panelu. Tuto chybu vyřešit, najdete v tématu to [– nejčastější dotazy](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testování projektu

V produktu VS Code pomocí následujícího postupu otevřete a nastavit sériové monitorování:

1. Klikněte na tlačítko `COM[X]` word na stavovém řádku nastavit pravý port COM s `STMicroelectronics`: ![sadu port com](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Klikněte na ikonu power moduly na stavovém řádku otevřete sériové monitorování: ![sériové monitorování](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Na stavovém řádku, klikněte na číslo, představující přenosová rychlost a nastavte ji na `115200`: ![set přenosová rychlost](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

Sériové monitorování zobrazí všechny zprávy odeslané podle nákresu ukázka. Nákresu připojí DevKit připojení Wi-Fi. Po úspěšné připojení k Wi-Fi nákresu odešle zprávu do MQTT zprostředkovatele. Poté odešle ukázku opakovaně dvě zprávy "iot.eclipse.org" pomocí technologie QoS 0 a QoS 1, v uvedeném pořadí.

![sériové output](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problémy a zpětné vazby

Pokud narazíte na problémy, podívejte se na [nejčastější dotazy k](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo se připojte pomocí následující kanály:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Další informace najdete v tématech

* [IoT DevKit AZ3166 se připojit ke službě Azure IoT Hub v cloudu]({{"/docs/getting-started/" | absolute_url }})
* [Zatřesením, zatřesením pro Tweet]({{"/docs/projects/shake-shake/" | absolute_url }})

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili konfigurace vaší Iot DevKit MXChip jako klient MQTT a používat k odesílání zpráv do broker MQTT MQTT klientské knihovny, tady jsou navrhované další kroky:

* [Přehled služby Azure IoT Suite](https://docs.microsoft.com/azure/iot-suite/)
* [Připojte zařízení MXChip IoT DevKit do aplikace Microsoft IoT centrální](https://docs.microsoft.com/en-us/microsoft-iot-central/howto-connect-devkit)
