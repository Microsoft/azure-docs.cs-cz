---
title: Brána protokolu Azure IoT | Dokumenty společnosti Microsoft
description: Jak používat bránu protokolu Azure IoT k rozšíření funkcí služby IoT Hub a podpory protokolů, aby se zařízení mohla připojit k vašemu rozbočovači pomocí protokolů, které služba IoT Hub nepodporuje nativně.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/11/2017
ms.openlocfilehash: 9dbb7905c2a0fed65ede610577e0fa11a1deef92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60345392"
---
# <a name="support-additional-protocols-for-iot-hub"></a>Podpora dalších protokolů pro službu IoT Hub

Azure IoT Hub nativně podporuje komunikaci přes protokoly MQTT, AMQP a HTTPS. V některých případech nemusí být zařízení nebo brány v terénu schopny používat jeden z těchto standardních protokolů a vyžadovat přizpůsobení protokolu. V takových případech můžete použít vlastní bránu. Vlastní brána umožňuje přizpůsobení protokolu pro koncové body služby IoT Hub přemostěním provozu do a z IoT Hub. Bránu [protokolu Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) můžete použít jako vlastní bránu k povolení přizpůsobení protokolu pro Službu IoT Hub.

## <a name="azure-iot-protocol-gateway"></a>Brána protokolu Azure IoT

Brána protokolu Azure IoT je rámec pro přizpůsobení protokolu, který je určený pro vysokorychlostní obousměrnou komunikaci zařízení s IoT Hub. Brána protokolu je předávací součást, která přijímá připojení zařízení přes určitý protokol. Přepne provoz do ioT hubu přes AMQP 1.0.

Bránu protokolu v Azure můžete nasadit vysoce škálovatelným způsobem pomocí Azure Service Fabric, role pracovních míst Azure Cloud Services nebo virtuálnípočítače Windows. Kromě toho lze bránu protokolu nasadit v místních prostředích, jako jsou brány polí.

Brána protokolu Azure IoT obsahuje adaptér protokolu MQTT, který umožňuje v případě potřeby přizpůsobit chování protokolu MQTT. Vzhledem k tomu, že služba IoT Hub poskytuje integrovanou podporu pro protokol MQTT v3.1.1, měli byste zvážit použití adaptéru protokolu MQTT pouze v případě, že jsou vyžadována přizpůsobení protokolu nebo specifické požadavky na další funkce.

Adaptér MQTT také demonstruje programovací model pro vytváření adaptérů protokolu pro jiné protokoly. Kromě toho programovací model brány protokolu Azure IoT umožňuje připojit vlastní součásti pro specializované zpracování, jako je vlastní ověřování, transformace zpráv, komprese/dekomprese nebo šifrování/dešifrování provozu mezi zařízeními a IoT Hub.

Pro flexibilitu jsou brána protokolu Azure IoT a implementace MQTT k dispozici v open source softwarovém projektu. Open source projekt můžete použít k přidání podpory pro různé protokoly a verze protokolů nebo přizpůsobit implementaci pro váš scénář. 

## <a name="next-steps"></a>Další kroky

Další informace o bráně protokolu Azure IoT a o tom, jak ji používat a nasazovat jako součást řešení IoT, najdete v tématu:

* [Úložiště brány protokolu Azure IoT na GitHubu](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)

* [Průvodce vývojářem brány protokolu Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Další informace o plánování nasazení služby IoT Hub najdete v tématu:

* [Porovnat s centry událostí](iot-hub-compare-event-hubs.md)

* [Škálování, vysoká dostupnost a zotavení po havárii](iot-hub-scaling.md)

* [Průvodce vývojáři ioT Hubu](iot-hub-devguide.md)