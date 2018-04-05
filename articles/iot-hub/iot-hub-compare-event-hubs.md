---
title: Porovnání Azure IoT Hub do služby Azure Event Hubs | Microsoft Docs
description: Porovnání služby IoT Hub a Event Hubs Azure zvýraznění určité rozdíly a případy použití. Porovnání obsahuje podporované protokoly, správu zařízení, monitorování, a nahrávání souborů.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 303a2bde0a1e0b25ca6eb145e7b0cd6c91fff351
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Porovnání Azure IoT Hub a Azure Event Hubs

Azure IoT Hub a Azure Event Hubs jsou cloudové služby, které mohou ingestování velké objemy dat a proces nebo ukládání dat podnikových statistik. Obě služby jsou podobné v tom, že oba podporují zpracování dat událostí a telemetrie s nízkou latencí a vysokou spolehlivostí. Ale pouze IoT Hub vyvinula s specifické možnosti, které jsou potřeba k podpoře v odpovídajícím měřítku pro internet věcí scénáře. 

Azure IoT Hub je Cloudová brána, která připojí zařízení a shromažďuje data pro automatizaci a podnikových statistik. Usnadňuje Streamovat data do cloudu a správě svých zařízení ve velkém měřítku. Důležité rozdílem mezi IoT Hub a dalších služeb přijímání dat je, že Centrum IoT obsahuje funkce, které zlepšit komunikaci oddělení vztah mezi zařízeními a back-end systémy. Možnosti obousměrnou komunikaci znamenají, že při přijímat data ze zařízení, můžete odeslat také, že zprávy zpět do zařízení aktualizace vlastnosti nebo vyvolání akce. Identity zařízení úrovni pomáhá zabezpečit váš systém. Distribuovat výpočetní logiku přesune cloudové služby na hraniční zařízení.

[Azure Event Hubs] [ Azure Event Hubs] je služba přijímání událostí, která umožňuje zpracovávat a ukládat velké objemy dat a telemetrie. Event Hubs je určená pro přijímání událostí v masivním měřítku, i v rámci scénáře mimo datové centrum a intra-datacenter, ale neposkytuje bohaté možnosti specifické pro IoT, které jsou k dispozici službou IoT Hub. Z tohoto důvodu není doporučeno Event Hubs pro vaše řešení IoT. 

Následující tabulka obsahuje podrobnosti o jak porovnat dvěma vrstvami IoT Hub do centra událostí při hodnocení je pro funkce IoT. Další informace o úrovních standard a basic služby IoT Hub, najdete v části [jak vybrat správné úrovně služby IoT Hub][lnk-scaling].

| Funkce IoT | IoT Hub úrovně standard | Základní úroveň služby IoT Hub | Event Hubs |
| --- | --- | --- | --- |
| Zasílání zpráv typu zařízení cloud | ![Zaškrtnout][1] | ![Zaškrtnout][1] | ![Zaškrtnout][1] |
| Protokoly: AMQP protokolu HTTPS, AMQP, přes objekty websockets | ![Zaškrtnout][1] | ![Zaškrtnout][1] | ![Zaškrtnout][1] |
| Protokoly: MQTT, MQTT přes objekty websockets | ![Zaškrtnout][1] | ![Zaškrtnout][1] |  |
| Identita na zařízení. | ![Zaškrtnout][1] | ![Zaškrtnout][1] |  |
| Načtení souborů ze zařízení | ![Zaškrtnout][1] | ![Zaškrtnout][1] |  |
| Device Provisioning Service | ![Zaškrtnout][1] | ![Zaškrtnout][1] |  |
| Zasílání zpráv typu cloud zařízení | ![Zaškrtnout][1] |  |  |
| Správa zařízení a dvojče zařízení | ![Zaškrtnout][1] |  |  |
| IoT Edge | ![Zaškrtnout][1] |  |  |

I když pouze případ použití je přijímání dat zařízení cloud, důrazně doporučujeme pomocí služby IoT Hub, která poskytuje službu, která je určená pro připojení zařízení IoT. 

### <a name="next-steps"></a>Další postup

Chcete-li prozkoumat další možnosti IoT Hub, přečtěte si téma [Příručka vývojáře pro službu IoT Hub][lnk-devguide]


[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md

<!--Image references-->
[1]: ./media/iot-hub-compare-event-hubs/ic195031.png