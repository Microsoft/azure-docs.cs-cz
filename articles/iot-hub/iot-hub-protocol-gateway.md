---
title: Brána protokolu Azure IoT | Dokumentace Microsoftu
description: Jak používat službu Azure IoT gateway protokol rozšířit možnosti a podporu protokolu chcete zařízením povolit, pro připojení k centru pomocí protokolů nejsou podporovány ve službě IoT Hub nativně služby IoT Hub.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/11/2017
ms.openlocfilehash: 91d61ffecc0e5345d02db88af73aee4586f7099e
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "57009847"
---
# <a name="support-additional-protocols-for-iot-hub"></a>Podpora dalších protokolů pro službu IoT Hub
Azure IoT Hub nativně podporuje komunikaci přes protokoly MQTT, AMQP a protokolu HTTPS. V některých případech zařízení nebo bran v terénu nebudou moct použít jednu z těchto standardních protokolů a vyžadují úpravu protokolu. V takovém případě můžete použít vlastní bránu. Vlastní brána umožňuje přizpůsobení a protokol pro koncové body IoT Hubu pomocí přemostění provoz do a ze služby IoT Hub. Můžete použít [brány protokolu Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) jako vlastní bránu k povolení protokolu přizpůsobení pro službu IoT Hub.

## <a name="azure-iot-protocol-gateway"></a>Brána protokolu Azure IoT
Brána protokolu Azure IoT je architektura určená k přizpůsobení protokolu, které je navržené pro zajištění vysoce škálovatelné, obousměrné komunikace zařízení služby IoT Hub. Brána protokolu je průchozí součást, která přijímá připojení zařízení přes určitý protokol. Provoz do služby IoT Hub je přemosťuje prostřednictvím protokolu AMQP 1.0. 

Brána protokolu v Azure můžete nasadit ve vysoce škálovatelným způsobem pomocí Azure Service Fabric, rolí pracovního procesu Azure Cloud Services nebo Windows Virtual Machines. Kromě toho je možné nasadit brána protokolu v místních prostředích, jako je například bran v terénu.

Brána protokolu Azure IoT zahrnuje adaptér protokol MQTT, který umožňuje přizpůsobit chování protokolu MQTT v případě potřeby. Od služby IoT Hub poskytuje integrovanou podporu pro protokol MQTT protokoly v3.1.1, pouze zvažte použití adaptér protokolu MQTT, pokud se vyžaduje protokol přizpůsobení nebo specifické požadavky na další funkce.

Adaptér MQTT také ukazuje programovací model pro vytváření protokol adaptéry pro jiné protokoly. Kromě toho programovací model brány protokolu Azure IoT můžete zařadit vlastní komponenty pro speciální zpracování, jako jsou například vlastní ověřování, zpráva transformace, kompresi a dekompresi nebo šifrování a dešifrování přenosů mezi zařízení a služby IoT Hub.

Zajišťuje tak flexibilitu brána protokolu Azure IoT a MQTT implementace jsou poskytovány v projektu open source softwaru. Můžete použít open source projekt a přidat podporu pro různé protokoly a verze protokolu nebo přizpůsobení implementace pro váš scénář. 

## <a name="next-steps"></a>Další postup
Další informace o brány protokolu Azure IoT a tom, jak používat a nasaďte ho jako součást řešení IoT, najdete v tématech:

* [Úložiště brány protokolu Azure IoT na Githubu](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Příručka pro vývojáře brány protokolu sady Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Další informace o plánování nasazení služby IoT Hub, najdete v tématech:

* [Porovnání s Event Hubs][lnk-compare]
* [Škálování, vysokou dostupnost a zotavení po havárii][lnk-scaling]
* [Příručka vývojáře pro IoT Hub][lnk-devguide]

[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
