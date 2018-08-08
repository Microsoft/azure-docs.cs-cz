---
title: Odeslání zprávy serveru MQTT s využitím klientské knihovny Azure MQTT | Dokumentace Microsoftu
description: Použít DevKit jako klient k odeslání zprávy serveru MQTT
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.openlocfilehash: fc74613e00adc459f7a7b0a16c6f773fe4bf601d
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39593651"
---
# <a name="send-messages-to-an-mqtt-server"></a>Odeslání zprávy serveru MQTT

Internet věcí (IoT) systémy často řešit Přerušovaná, nízké kvality nebo pomalé připojení k Internetu. MQTT je protokol připojení machine-to-machine (modelů M2M), které se tyto výzvy v úvahu vyvinula. 

Knihovna klienta MQTT se tady použít. je součástí [Eclipse Paho](http://www.eclipse.org/paho/) projektu, který poskytuje rozhraní API pomocí protokolu MQTT přes více způsob přenosu.

## <a name="what-you-learn"></a>Co se naučíte

V tomto projektu se dozvíte:
- Jak používat protokol MQTT klientské knihovny pro odesílání zpráv do Service broker MQTT.
- Jak nakonfigurovat MXChip Iot DevKit jako klienta MQTT.

## <a name="what-you-need"></a>Co potřebujete

Dokončit [– Příručka Začínáme](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) na:

* Mají vaše DevKit připojení k Wi-Fi
* Příprava vývojového prostředí

## <a name="open-the-project-folder"></a>Otevřít složku projektu

1. Pokud už je DevKit připojte k počítači, odpojte ho.

2. Spusťte VS Code.

3. Připojení DevKit k vašemu počítači.

## <a name="open-the-mqttclient-sample"></a>Otevřete ukázku MQTTClient

Rozbalte na levé straně **ARDUINO příklady** vyhledejte **příklady MXCHIP AZ3166 > MQTT**a vyberte **MQTTClient**. Otevře se nové okno VS Code se v něm složku projektu.

> [!NOTE]
> Příklad lze také otevřít z palety příkazů. Použití `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) otevřete paletu příkazů, zadejte **Arduino**a poté vyhledejte a vyberte **Arduino: Příklady**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Vytváření a odesílání Arduino sketch DevKit

Typ `Ctrl+P` (macOS: `Cmd+P`) ke spuštění `task device-upload`. Po dokončení nahrávání DevKit restartuje a spustí náčrt.

![nahrávání zařízení](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Může se zobrazit "Chyba: AZ3166: Neznámý balíček" chybová zpráva. Tato chyba nastane, pokud index panelu balíček není správně aktualizovat. Chcete-li tuto chybu vyřešit, přečtěte si [části vývoj nejčastější dotazy týkající se IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testování projektu

V nástroji VS Code pomocí následujícího postupu otevřete a nastavte monitorování sériového portu:

1. Klikněte na tlačítko `COM[X]` word na stavovém řádku na nastavte správný port COM s `STMicroelectronics`: ![sady port com](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Klikněte na ikonu power moduly na stavovém řádku otevřete sériového portu monitorování: ![sériové monitorování](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Na stavovém řádku, klikněte na číslo představující přenosovou rychlostí a nastavte ho na `115200`: ![set přenosová rychlost](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

Sériový Monitor má zobrazit všechny zprávy odeslané sketch vzorku. Náčrt DevKit připojí k Wi-Fi. Po úspěšném připojení Wi-Fi náčrt odešle zprávu do zprostředkovatele protokolu MQTT. Poté odešle vzorku opakovaně dvě "iot.eclipse.org" zprávy pomocí technologie QoS 0 a QoS 1 v uvedeném pořadí.

![výstup sériové](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na potíže, přečtěte si [nejčastější dotazy týkající se IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo se připojte pomocí následujících kanálů:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Další informace najdete v tématech

* [IoT DevKit AZ3166 se připojit ke službě Azure IoT Hub v cloudu](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Zatřeste, zatřeste tweetu](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak nakonfigurovat MXChip Iot DevKit jako klienta MQTT a použít protokol MQTT klientskou knihovnu pro odesílání zpráv do Service broker MQTT, tady jsou další navrhované kroky:

* [Azure IoT vzdálené monitorování přehled akcelerátorů řešení](https://docs.microsoft.com/azure/iot-suite/)
* [Připojení MXChip IoT DevKit zařízení do aplikace Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
