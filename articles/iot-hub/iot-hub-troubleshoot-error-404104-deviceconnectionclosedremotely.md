---
title: Řešení potíží s chybou služby Azure IoT Hub 404104 DeviceConnectionClosedly
description: Pochopit, jak opravit chybu 404104 DeviceConnectionClosedly
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: c8cb91aa0c7ce1610320d4107db282d3c34407ba
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758724"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

Tento článek popisuje příčiny a řešení pro **404104 DeviceConnectionClosedRemotely** chyby.

## <a name="symptoms"></a>Příznaky

### <a name="symptom-1"></a>Příznak 1

Zařízení odpojit v pravidelných intervalech (každých 65 minut, například) a zobrazí **404104 DeviceConnectionClosedRemotely** v protokolech diagnostiky služby IoT Hub. Někdy se také zobrazí **401003 IoTHubUnauthorized** a úspěšné události připojení zařízení méně než minutu později.

### <a name="symptom-2"></a>Příznak 2

Zařízení odpojit náhodně a zobrazí **404104 DeviceConnectionClosedRemotely** v protokolech diagnostiky služby IoT Hub.

### <a name="symptom-3"></a>Příznak 3

Mnoho zařízení odpojit najednou, uvidíte pokles v [metriku připojených zařízení](iot-hub-metrics.md)a existuje více **404104 DeviceConnectionClosedRemotely** a [500xxx Vnitřní chyby](iot-hub-troubleshoot-error-500xxx-internal-errors.md) v diagnostických protokolech než obvykle.

## <a name="causes"></a>Příčiny

### <a name="cause-1"></a>Příčina 1

Platnost [tokenu SAS používaného k připojení k službě IoT Hub](iot-hub-devguide-security.md#security-tokens) vypršela, což způsobí, že služby IoT Hub odpojí zařízení. Připojení je obnoveno při aktualizaci tokenu zařízením. Například [token SAS vyprší každou hodinu ve výchozím nastavení pro C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication), což může vést k pravidelné odpojení.

Další informace naleznete v [tématu 401003 IoTHubNeautorizovaná příčina](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1).

### <a name="cause-2"></a>Příčina 2

Některé možnosti zahrnují:

- Zařízení ztratilo základní síťové připojení delší, než je [udržování mqtt](iot-hub-mqtt-support.md#default-keep-alive-timeout), což má za následek časový limit vzdálené holčicí činnosti. Nastavení udržování mqtt může být různé pro zařízení.

- Zařízení odeslala reset ování na úrovni Protokolu TCP/IP, `MQTT DISCONNECT`ale neodeslala úroveň aplikace . V podstatě zařízení náhle uzavřelo připojení spodní ho soketu. Někdy tento problém je způsoben chyby ve starších verzích sady Azure IoT SDK.

- Aplikace na straně zařízení havarovala.

### <a name="cause-3"></a>Příčina 3

IoT Hub může docházet k přechodným potížím. Viz [Příčinou chyb interního serveru služby IoT Hub](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause).

## <a name="solutions"></a>Řešení

### <a name="solution-1"></a>Řešení 1

Viz [401003 IoTHubNeautorizované řešení 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)

### <a name="solution-2"></a>Řešení 2

- [Testováním připojení](tutorial-connectivity.md)se ujistěte, že má zařízení dobré připojení k centru IoT Hub . Pokud je síť nespolehlivá nebo přerušovaná, nedoporučujeme zvyšovat hodnotu keep-alive, protože by to mohlo vést k detekci (například prostřednictvím výstrah Azure Monitoru) trvá déle. 

- Použijte nejnovější verze sad [IoT SDK](iot-hub-devguide-sdks.md).

### <a name="solution-3"></a>Řešení 3

Viz [řešení chyb interního serveru služby IoT Hub](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution).

## <a name="next-steps"></a>Další kroky

Doporučujeme používat sady SDK zařízení Azure IoT ke spolehlivé správě připojení. Další informace najdete v [tématu Správa připojení a spolehlivého zasílání zpráv pomocí sad SDK zařízení Azure IoT Hub.](iot-hub-reliability-features-in-sdks.md)