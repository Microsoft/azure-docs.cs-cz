---
title: Posílání zpráv na server MQTT pomocí klientské knihovny Azure MQTT
description: Naučte se používat klientskou knihovnu MQTT k posílání zpráv do zprostředkovatele MQTT. Také se dozvíte, jak nakonfigurovat mXChip IoT DevKit jako klienta MQTT.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.custom: mqtt
ms.openlocfilehash: a3a8c065c226d1cc94d79b44d16f288fab205a56
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337853"
---
# <a name="send-messages-to-an-mqtt-server"></a>Odesílání zpráv na server MQTT

Systémy Internet věcí (IoT) často řeší občasné, špatnou kvalitu nebo pomalé připojení k Internetu. MQTT je protokol připojení typu počítač-počítač (M2M), který byl vyvinut s těmito výzvami. 

Zde použitá knihovna klienta MQTT je součástí projektu [zatmění PAHO](https://www.eclipse.org/paho/) , který poskytuje rozhraní API pro použití MQTT přes více prostředků přenosu.

## <a name="what-you-learn"></a>Co se naučíte

V tomto projektu se naučíte:
- Jak používat klientskou knihovnu MQTT k posílání zpráv do zprostředkovatele MQTT.
- Jak nakonfigurovat MXChip IoT DevKit jako klienta MQTT.

## <a name="what-you-need"></a>Co budete potřebovat

Dokončete [průvodce Začínáme](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) :

* Připojení DevKit k Wi-Fi
* Příprava vývojového prostředí

## <a name="open-the-project-folder"></a>Otevření složky projektu

1. Pokud se DevKit už připojí k vašemu počítači, odpojte ho.

2. Spusťte VS Code.

3. Připojte DevKit k počítači.

## <a name="open-the-mqttclient-sample"></a>Otevřete ukázku MQTTClient

Rozbalte části **Příklady Arduino** na levé straně, přejděte k **příkladům pro MXCHIP AZ3166 > MQTT**a vyberte **MQTTClient**. Otevře se nové okno VS Code se složkou projektu.

> [!NOTE]
> Můžete také otevřít příklad z palety příkazů. Použijte `Ctrl+Shift+P` (MacOS: `Cmd+Shift+P` ) k otevření palety příkazů, zadejte **Arduino**a pak vyhledejte a vyberte **Arduino: příklady**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Sestavení a nahrání Arduino náčrtu do DevKit

Typ `Ctrl+P` (MacOS: `Cmd+P` ), který se má spustit `task device-upload` . Až se nahrávání dokončí, DevKit se restartuje a spustí nákres.

![zařízení – nahrání](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Může se zobrazit chybová zpráva "Chyba: AZ3166: neznámý balíček". K této chybě dochází, pokud se index balíčku panelu není správně aktualizovat. Pokud chcete tuto chybu vyřešit, přečtěte si [část věnované vývoji v tématu Nejčastější dotazy ke službě IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testování projektu

V VS Code postupujte podle následujícího postupu a otevřete a nastavte monitorování sériového portu:

1. Kliknutím na `COM[X]` slovo na stavovém řádku nastavte správný port COM `STMicroelectronics` : ![ set-COM-port](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Kliknutím na ikonu plug-in na stavovém řádku otevřete monitorování sériového portu: ![ sériové – monitorování](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Na stavovém řádku klikněte na číslo, které představuje přenosovou rychlost, a nastavte ji na `115200` : ![ nastavení přenosové rychlosti.](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

Nástroj sériového zobrazení zobrazí všechny zprávy odesílané vzorovým nákresem. Náčrt připojuje DevKit k Wi-Fi. Po úspěšném připojení k síti Wi-Fi pošle skica zprávu zprostředkovateli MQTT. Poté ukázka opakovaně odesílá dvě zprávy "iot.eclipse.org" pomocí technologie QoS 0 a QoS 1 v uvedeném pořadí.

![sériové – výstup](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problémy a zpětná vazba

Pokud narazíte na problémy, přečtěte si [Nejčastější dotazy ke službě IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) nebo se připojte pomocí následujících kanálů:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Viz také:

* [Připojení IoT DevKit AZ3166 k Azure IoT Hub v cloudu](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Třepačka, Třesce](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak nakonfigurovat MXChip IoT DevKit jako klienta MQTT a použít klientskou knihovnu MQTT k posílání zpráv do zprostředkovatele MQTT, je zde doporučený další krok: [Přehled akcelerátoru řešení vzdáleného monitorování Azure IoT](https://docs.microsoft.com/azure/iot-suite/) .
