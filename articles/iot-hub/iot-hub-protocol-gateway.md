---
title: Brány protokolu Azure IoT | Microsoft Docs
description: Jak používat brány protokolu Azure IoT rozšířit IoT Hub, možnosti a podpora protokolu povolit zařízení pro připojení do vašeho centra pomocí protokolů nejsou podporovány službou IoT Hub nativně.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: elioda
ms.openlocfilehash: 2c90ee899d0002d41ca21ed4a4927470ee53b2e1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635300"
---
# <a name="support-additional-protocols-for-iot-hub"></a>Podpora dalších protokoly pro IoT Hub
Azure IoT Hub nativně podporuje komunikaci přes protokoly MQTT, AMQP a HTTPS. V některých případech nemusí být zařízení nebo brány pole moct použít jednu z těchto standardních protokolů a vyžadovat protokol přizpůsobení. V takových případech můžete použít vlastní bránu. Vlastní brána umožňuje přizpůsobení a protokol pro koncové body centra IoT podle přemostění provoz do a ze služby IoT Hub. Můžete použít [brány protokolu Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) jako vlastní bránu povolit protokol přizpůsobení pro IoT Hub.

## <a name="azure-iot-protocol-gateway"></a>Brány protokolu Azure IoT
Brány protokolu Azure IoT je rozhraní pro protokol přizpůsobení, která je určená pro špičkové obousměrnou komunikaci zařízení službou IoT Hub. Brána protokolu je průchozí komponenty, která přijímá připojení zařízení přes určitý protokol. Přenosy do služby IoT Hub je přemosťuje prostřednictvím protokolu AMQP 1.0. 

Vysoce škálovatelné způsobem můžete nasadit brány protokolu v Azure pomocí Azure Service Fabric, rolí pracovního procesu Azure Cloud Services nebo virtuální počítače s Windows. Kromě toho brány protokolu se dá nasadit v prostředích místní, například pole brány.

Brány protokolu Azure IoT zahrnuje adaptér MQTT protokol, který umožňuje přizpůsobit chování protokolu MQTT v případě potřeby. Vzhledem k tomu, že služba IoT Hub zajišťuje integrovanou podporu pro protokol MQTT v3.1.1, měli byste pouze zvážit použití adaptér protokolu MQTT Pokud vlastní nastavení protokolu nebo specifické požadavky pro další funkce, je potřeba.

Adaptér MQTT také ukazuje programovací model pro vytváření adaptéry protokolu pro jiné protokoly. Kromě toho programovací model brány protokolu Azure IoT můžete zařadit vlastní komponenty pro speciální zpracování například vlastní ověřování, zpráva transformace, kompresi nebo dekompresi nebo šifrování a dešifrování přenosů mezi zařízeními a IoT Hub.

Pružnosti brány protokolu Azure IoT a MQTT implementace jsou uvedeny v projektu open-source softwaru. Můžete použít open-source projekt přidání podpory pro různé protokoly a verze protokolu nebo implementaci přizpůsobení pro váš scénář. 

## <a name="next-steps"></a>Další postup
Další informace o brány protokolu Azure IoT a jak používat a nasaďte ho jako součást řešení IoT najdete v tématu:

* [Úložiště brány protokolu Azure IoT na Githubu](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Příručka vývojáře brány protokolu sady Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Další informace o plánování nasazení služby IoT Hub naleznete v tématu:

* [Porovnání s služby Event Hubs][lnk-compare]
* [Škálování, vysokou dostupnost a zotavení po havárii][lnk-scaling]
* [Příručka vývojáře pro službu IoT Hub][lnk-devguide]

[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
