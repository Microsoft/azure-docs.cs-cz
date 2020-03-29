---
title: Porovnání azure iot hubu s Azure Event Hubs | Dokumenty společnosti Microsoft
description: Porovnání služeb Azure služby IoT Hub a Event Hubs zvýraznění funkční rozdíly a případy použití. Porovnání zahrnuje podporované protokoly, správu zařízení, monitorování a nahrávání souborů.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 7a589ba80b61ea5ef9ea1c941e9a0218a1653c99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60735507"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Připojení zařízení IoT k Azure: IoT Hub a centra událostí

Azure poskytuje služby speciálně vyvinuté pro různé typy připojení a komunikace, které vám pomůžou připojit data k výkonu cloudu. Azure IoT Hub i Azure Event Hubs jsou cloudové služby, které můžou ingestovat velké množství dat a zpracovávat nebo ukládat tato data pro obchodní přehledy. Tyto dvě služby jsou podobné v tom, že oba podporují ingestování dat s nízkou latencí a vysokou spolehlivostí, ale jsou určeny pro různé účely. IoT Hub byl vyvinut tak, aby řešil jedinečné požadavky na připojení zařízení IoT ke cloudu Azure, zatímco Centra událostí byla navržena pro streamování velkých objemů dat. Microsoft doporučuje používat Azure IoT Hub pro připojení zařízení IoT k Azure

Azure IoT Hub je cloudová brána, která propojuje zařízení IoT za účelem shromažďování dat a řízení obchodních přehledů a automatizace. Kromě toho IoT Hub obsahuje funkce, které obohacují vztah mezi vašimi zařízeními a back-endovými systémy. Funkce obousměrné komunikace znamenají, že zatímco přijímáte data ze zařízení, můžete také odesílat příkazy a zásady zpět do zařízení. Pomocí zasílání zpráv z cloudu na zařízení můžete například aktualizovat vlastnosti nebo vyvolat akce správy zařízení. Komunikace mezi cloudy také umožňuje odesílat cloudové informace do hraničních zařízení pomocí Azure IoT Edge. Jedinečná identita na úrovni zařízení poskytovaná službou IoT Hub pomáhá lépe zabezpečit vaše řešení IoT před potenciálními útoky. 

[Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) je služba streamování velkých objemů dat v Azure. Je navržená pro situace streamování dat s vysokou propustností, kde můžou zákazníci posílat miliardy požadavků denně. Event Hubs využívá model příjemců rozdělený do oddílů, který umožňuje horizontálně navýšit kapacitu streamu a je integrovaný do služeb Azure pro velké objemy dat a analýzy, mimo jiné Databricks, Stream Analytics, ADLS a HDInsight. Díky funkcím, jako je digitalizace centra událostí a automatické nafouknutí, je tato služba navržena tak, aby podporovala vaše aplikace a řešení pro velká data. IoT Hub navíc používá event huby pro svou cestu toku telemetrie, takže vaše řešení IoT také těží z obrovské honosné výkonu event hubů.

Chcete-li shrnout, obě řešení jsou určeny pro přilnavost dat v masivním měřítku. Jenom IoT Hub poskytuje bohaté funkce specifické pro IoT, které jsou navržené tak, aby maximalizovaly obchodní hodnotu připojení vašich zařízení IoT ke cloudu Azure.  Pokud vaše cesta ioT je teprve na začátku, počínaje IoT Hub pro podporu vašich scénářů ingestování dat zajistí, že budete mít okamžitý přístup k plně vybaveným funkcím IoT, jakmile vaše obchodní a technické potřeby vyžadují.

Následující tabulka obsahuje podrobnosti o tom, jak dvě úrovně IoT Hub porovnání s Event Hubs při jejich vyhodnocování pro funkce IoT. Další informace o standardních a základních úrovních ioT hubu najdete v tématu [Jak vybrat správnou úroveň IoT Hub](iot-hub-scaling.md).

| Schopnost IoT | Úroveň standardu IoT Hub | Základní úroveň IoT Hubu | Event Hubs |
| --- | --- | --- | --- |
| Zasílání zpráv mezi zařízeními a cloudy | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] |
| Protokoly: HTTPS, AMQP, AMQP přes webSockets | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] |
| Protokoly: MQTT, MQTT přes webSockets | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] |  |
| Identita pro zařízení | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] |  |
| Nahrávání souborů ze zařízení | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] |  |
| Služba zřizování zařízení | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] |  |
| Zasílání zpráv mezi cloudy | ![Zaškrtnout][checkmark] |  |  |
| Dvojče zařízení a správa zařízení | ![Zaškrtnout][checkmark] |  |  |
| Datové proudy zařízení (náhled) | ![Zaškrtnout][checkmark] |  |  |
| IoT Edge | ![Zaškrtnout][checkmark] |  |  |

I v případě, že jediný případ použití je ingestování dat zařízení cloud, důrazně doporučujeme používat Službu IoT Hub, protože poskytuje službu, která je určena pro připojení zařízení IoT. 

### <a name="next-steps"></a>Další kroky

Další informace o možnostech služby IoT Hub najdete v [průvodci vývojáři služby IoT Hub](iot-hub-devguide.md).

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png
