---
title: Odesílání zpráv na server MQTT pomocí klientské knihovny Azure MQTT
description: Zjistěte, jak pomocí knihovny klienta MQTT odesílat zprávy zprostředkovateli MQTT. Také se dozvíte, jak nakonfigurovat mXChip IoT DevKit jako klient MQTT.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.openlocfilehash: 14feb02fbac800cd37da882a1872beb8269d9938
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954242"
---
# <a name="send-messages-to-an-mqtt-server"></a>Odesílání zpráv na server MQTT

Systémy Internetu věcí (IoT) se často zabývají přerušovaným, nekvalitním nebo pomalým připojením k internetu. MQTT je protokol připojení mezi stroji (M2M), který byl vyvinut s ohledem na tyto výzvy. 

Klientská knihovna MQTT, která je zde použita, je součástí projektu [Eclipse Paho,](https://www.eclipse.org/paho/) který poskytuje api pro použití MQTT přes více dopravních prostředků.

## <a name="what-you-learn"></a>Co se naučíte

V tomto projektu se dozvíte:
- Jak používat knihovnu klienta MQTT k odesílání zpráv zprostředkovateli MQTT.
- Jak nakonfigurovat MXChip Iot DevKit jako klienta MQTT.

## <a name="what-you-need"></a>Co potřebujete

Dokončete [příručku Začínáme](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) s tímto účelem:

* Připojení devKitu k Wi-Fi
* Příprava vývojového prostředí

## <a name="open-the-project-folder"></a>Otevření složky projektu

1. Pokud je devKit již připojit k počítači, odpojte jej.

2. Spusťte VS Code.

3. Připojte devKit k počítači.

## <a name="open-the-mqttclient-sample"></a>Otevření ukázky klienta MQTT

Rozbalte sekci **PŘÍKLADY ARDUINO** na levé straně, přejděte k **příkladům pro MXCHIP AZ3166 > MQTT**a vyberte **MQTTClient**. Otevře se nové okno VS Code se složkou projektu.

> [!NOTE]
> Příklad můžete otevřít také z palety příkazů. Pomocí `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) otevřete paletu příkazů, zadejte **Arduino**a pak najděte a vyberte **Arduino: Příklady**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Sestavte a nahrajte skicu Arduino do DevKitu

Typ `Ctrl+P` (macOS: `Cmd+P`) `task device-upload`ke spuštění . Po dokončení nahrávání devKit restartuje a spustí skicu.

![nahrávání zařízení](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Může se zobrazit chybová zpráva Chyba: AZ3166: Neznámý balíček. K této chybě dochází, pokud index balíčku desky není správně aktualizován. Chcete-li tuto chybu vyřešit, naleznete v [části vývoje v nejčastějších dotazech k IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testování projektu

V kódu VS otevřete a nastavte sériový monitor následujícím postupem:

1. Kliknutím `COM[X]` na slovo na stavovém řádku `STMicroelectronics`nastavte ![správný port COM pomocí : set-com-port](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Kliknutím na ikonu elektrické zástrčky na stavovém řádku otevřete sériový monitor: ![sériový monitor](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Na stavovém řádku klikněte na číslo, které `115200`představuje ![přenosovou rychlost, a nastavte ji na : set-baud-rate](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

Sériový monitor zobrazí všechny zprávy odeslané ukázkovou skicou. Skica spojuje DevKit s Wi-Fi. Jakmile je připojení Wi-Fi úspěšné, skica odešle zprávu zprostředkovateli MQTT. Poté ukázka opakovaně odešle dvě zprávy "iot.eclipse.org" pomocí QoS 0 a QoS 1.

![sériový výstup](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na problémy, podívejte se na [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo se připojte pomocí následujících kanálů:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Viz také

* [Připojení IoT DevKit AZ3166 k Azure IoT Hub v cloudu](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Shake, Shake pro pípání](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Další kroky

Nyní, když jste se naučili, jak nakonfigurovat MXChip Iot DevKit jako klienta MQTT a použít knihovnu klienta MQTT k odesílání zpráv zprostředkovateli MQTT, zde jsou navrhované další kroky:

* [Přehled akcelerátoru řešení vzdáleného monitorování Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Připojení zařízení MXChip IoT DevKit k aplikaci Azure IoT Central](/azure/iot-central/core/howto-connect-devkit)
