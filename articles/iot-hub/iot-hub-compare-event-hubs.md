---
title: Porovnání Azure IoT Hub a Azure Event Hubs | Dokumentace Microsoftu
description: Porovnání služeb IoT Hub a Event Hubs Azure, zvýraznění funkční rozdíly a případy použití. Porovnání zahrnuje podporovaných protokolů, zařízení, monitorování, správy a nahrávání souborů.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 830052341c4f0e3488c8e63da59cbef1f72e158a
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2018
ms.locfileid: "42058329"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Připojení k Azure IoT zařízení: služby IoT Hub a Event Hubs

Azure poskytuje služby, vyvinutá speciálně pro různých typů připojení a komunikace připojení dat k elektrické energie cloudu vám usnadní. Azure IoT Hub a Azure Event Hubs jsou cloudové služby, které můžete ingestovat velké objemy dat a zpracování nebo uložení dat k obchodním přehledům. Tyto dvě služby jsou podobné v tom, že obě podporují ingestování dat s nízkou latencí a vysokou spolehlivostí, ale jsou určené pro různé účely. Konkrétně k vyřešení jedinečné požadavky na připojení zařízení IoT ve velkém měřítku, do cloudu Azure, zatímco služba Event Hubs je navržená pro streamování velkých objemů dat byla vyvinuta služby IoT Hub. To je důvod, proč společnost Microsoft doporučuje používat Azure IoT Hub připojit zařízení IoT pro Azure

Azure IoT Hub je cloudové bráně, které propojuje zařízení IoT ke shromáždění dat k získání obchodních přehledů a automatizace. Kromě toho služby IoT Hub obsahuje funkce, které obohacují vztah mezi zařízeními a back-endových systémů. Obousměrná komunikace, které funkce znamená, že při přijímat data ze zařízení můžete také posílat příkazy a zásady zpět do zařízení, třeba k aktualizaci vlastností nebo vyvolání akce správy zařízení.  Toto připojení typu cloud zařízení také poskytuje důležité možnosti doručování inteligentních cloudových funkcí do hraničních zařízení pomocí služby Azure IoT Edge. Jedinečná identita úrovni zařízení poskytované služby IoT Hub pomáhá lépe zabezpečené řešení IoT z možných útoků. 

[Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) jsou velké objemy dat, streamování služby Azure. Je navržená pro streamování scénáře s vysokou propustností data kde mohou zákazníci odesílat miliardy požadavků denně. Event Hubs využívá modelu oddělených příjemců je pravidlo pro horizontální navýšení kapacity datového proudu a je integrována do velkých objemů dat a analytické služby Azure, včetně Databricks, Stream Analytics, ADLS a HDInsight. Pomocí funkcí, jako jsou Event Hubs Capture a automatické rozšiřování tato služba je účelem je podporovat své aplikace pro velké objemy dat a řešení. Kromě toho služby IoT Hub využívá Event Hubs pro cestu tok telemetrických dat, tak řešení IoT také těží z obrovské sílu služby Event Hubs.

Souhrnně řečeno, zatímco obě řešení jsou určeny pro příjem dat ve velkém měřítku, pouze služby IoT Hub poskytuje bohaté IoT specifické možnosti, které jsou určeny pro vás k maximalizaci obchodní hodnoty jazyka připojení vašich zařízení IoT do cloudu Azure.  Pokud začínáte svoji cestu k IoT, spouští se službou IoT Hub pro podporu scénářů příjmu dat zajistí, že máte okamžitý přístup k funkcím IoT plně funkční po obchodní a technické požadavky, které je vyžadují.

Následující tabulka obsahuje podrobnosti o dvě úrovně služby IoT Hub porovnání do služby Event Hubs při hodnocení je pro funkce IoT. Další informace o úrovních standard a basic služby IoT Hub najdete v tématu [návodu k výběru správné úrovně služby IoT Hub](iot-hub-scaling.md).

| Funkce IoT | Úroveň standard služby IoT Hub | Úroveň basic služby IoT Hub | Event Hubs |
| --- | --- | --- | --- |
| Zasílání zpráv typu zařízení cloud | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] |
| Protokoly: AMQP HTTPS, AMQP, přes webSockets | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] |
| Protokoly: MQTT, MQTT přes webSockets | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] |  |
| Identita na jedno zařízení | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] |  |
| Načtení souborů ze zařízení | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] |  |
| Device Provisioning Service | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] |  |
| Zasílání zpráv z cloudu na zařízení | ![Zaškrtnout][checkmark] |  |  |
| Dvojče zařízení a správy zařízení | ![Zaškrtnout][checkmark] |  |  |
| IoT Edge | ![Zaškrtnout][checkmark] |  |  |

I v případě, že jediným případem použití je příjem dat typu zařízení cloud, důrazně doporučujeme pomocí služby IoT Hub, protože poskytuje službu, která je navržená pro připojení zařízení IoT. 

### <a name="next-steps"></a>Další postup

Podrobněji prozkoumat možnosti služby IoT Hub, najdete v článku [Příručka vývojáře pro IoT Hub](iot-hub-devguide.md).

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png
