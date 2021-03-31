---
title: Brána protokolu Azure IoT | Microsoft Docs
description: Jak používat bránu protokolu Azure IoT k rozšiřování funkcí IoT Hub a podpory protokolů, aby se zařízení mohla připojit k vašemu rozbočovači pomocí protokolů, které IoT Hub nativně nepodporují.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/11/2017
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 315d76715973b79a971f3ca15bcc5186d20135a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87325028"
---
# <a name="support-additional-protocols-for-iot-hub"></a>Podpora dalších protokolů pro IoT Hub

Azure IoT Hub nativně podporuje komunikaci přes protokoly MQTT, AMQP a HTTPS. V některých případech nemusí být zařízení nebo brány polí schopné použít jeden z těchto standardních protokolů a vyžadují úpravu protokolu. V takových případech můžete použít vlastní bránu. Vlastní brána umožňuje úpravu protokolu pro koncové body IoT Hub přemostěním provozu do a z IoT Hub. [Bránu protokolu Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) můžete použít jako vlastní bránu k povolení přizpůsobení protokolu pro IoT Hub.

## <a name="azure-iot-protocol-gateway"></a>Brána protokolu Azure IoT

Brána protokolu Azure IoT je architektura pro úpravu protokolu, která je navržená pro komunikaci obousměrného zařízení s IoT Hub. Brána protokolu je průchozí komponenta, která přijímá připojení zařízení přes konkrétní protokol. Přemostění provozu do IoT Hub přes AMQP 1,0.

Bránu protokolu můžete v Azure nasadit vysoce škálovatelným způsobem pomocí Service Fabric Azure, rolí pracovního procesu Azure Cloud Services nebo Windows Virtual Machines. Kromě toho je možné bránu protokolu nasadit v místních prostředích, jako jsou například brány polí.

Brána protokolu Azure IoT zahrnuje adaptér protokolu MQTT, který v případě potřeby umožňuje přizpůsobit chování protokolu MQTT. Vzhledem k tomu, že IoT Hub poskytuje integrovanou podporu protokolu MQTT v 3.1.1, měli byste zvážit použití adaptéru protokolu MQTT, pokud jsou potřeba vlastní nastavení protokolu nebo specifické požadavky pro další funkce.

Adaptér MQTT také ukazuje programovací model pro vytváření adaptérů protokolu pro jiné protokoly. Kromě toho programovací model brány protokolu Azure IoT umožňuje připojit vlastní součásti pro specializované zpracování, jako je vlastní ověřování, transformace zpráv, komprese/dekomprese nebo šifrování a dešifrování provozu mezi zařízeními a IoT Hub.

V případě flexibility jsou v rámci open source softwarového projektu brány a implementace protokolu Azure IoT a MQTT k dispozici. Můžete použít Open source projekt k přidání podpory pro různé protokoly a verze protokolu nebo přizpůsobení implementace pro váš scénář. 

## <a name="next-steps"></a>Další kroky

Další informace o bráně protokolu Azure IoT a o tom, jak ji použít a nasadit jako součást řešení IoT, najdete v těchto tématech:

* [Úložiště brány protokolu Azure IoT na GitHubu](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)

* [Příručka pro vývojáře k bráně protokolu Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Další informace o plánování nasazení IoT Hub najdete v tématech:

* [Porovnat s Event Hubs](iot-hub-compare-event-hubs.md)

* [Škálování, vysoká dostupnost a zotavení po havárii](iot-hub-scaling.md)

* [IoT Hub příručka pro vývojáře](iot-hub-devguide.md)