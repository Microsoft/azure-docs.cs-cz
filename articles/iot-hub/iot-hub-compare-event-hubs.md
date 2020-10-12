---
title: Porovnat Azure IoT Hub s Azure Event Hubs | Microsoft Docs
description: Porovnání IoT Hub a Event Hubs služeb Azure zvýrazňuje funkční rozdíly a případy použití. Porovnání zahrnuje podporované protokoly, správu zařízení, monitorování a nahrávání souborů.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: System Architecture'
ms.openlocfilehash: 1b51e2338dabd34ec69d311a9eb1a5c4a644f628
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87322750"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Připojení zařízení IoT k Azure: IoT Hub a Event Hubs

Azure poskytuje služby speciálně vyvinuté pro nejrůznější typy připojení a komunikace, které vám pomůžou s připojením vašich dat ke sílu cloudu. Azure IoT Hub i Azure Event Hubs jsou cloudové služby, které mohou ingestovat velké objemy dat a zpracovávat nebo ukládat tato data pro obchodní přehledy. Tyto dvě služby jsou podobné v tom, že obě podporují přijímání dat s nízkou latencí a vysokou spolehlivostí, ale jsou navržené pro různé účely. IoT Hub byla vyvinuta pro řešení jedinečných požadavků na připojení zařízení IoT ke cloudu Azure, zatímco Event Hubs byla navržena pro streamování velkých objemů dat. Microsoft doporučuje použít Azure IoT Hub k připojení zařízení IoT k Azure.

Azure IoT Hub je cloudová brána, která připojuje zařízení IoT ke shromažďování dat a zajištění služby Business Insights a automatizace. Kromě toho IoT Hub zahrnuje funkce, které rozšiřují vztah mezi vašimi zařízeními a back-end systémy. Možnosti obousměrné komunikace znamenají, že když přijímáte data ze zařízení, můžete také příkazy a zásady poslat zpátky do zařízení. Pomocí zasílání zpráv z cloudu na zařízení můžete například aktualizovat vlastnosti nebo vyvolat akce správy zařízení. Komunikace z cloudu na zařízení také umožňuje posílání cloudových informací do hraničních zařízení pomocí Azure IoT Edge. Jedinečná identita na úrovni zařízení, kterou poskytuje IoT Hub, pomáhá lépe zabezpečit vaše řešení IoT před potenciálními útoky. 

[Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) je služba pro streamování velkých objemů dat v Azure. Je navržená pro situace streamování dat s vysokou propustností, kde můžou zákazníci posílat miliardy požadavků denně. Event Hubs využívá model příjemců rozdělený do oddílů, který umožňuje horizontálně navýšit kapacitu streamu a je integrovaný do služeb Azure pro velké objemy dat a analýzy, mimo jiné Databricks, Stream Analytics, ADLS a HDInsight. Tato služba je navržená tak, aby podporovala vaše aplikace a řešení pro velké objemy dat a nabízí funkce, jako je Event Hubs zachytávání a automatické neploché. Kromě toho IoT Hub používá Event Hubs pro svou cestu toku telemetrie, takže vaše řešení IoT také přináší výhody obrovské síly Event Hubs.

Pro shrnutí je obě řešení navržena pro příjem dat v obrovském měřítku. Jenom IoT Hub poskytují bohatě náročné funkce specifické pro IoT, které jsou navržené tak, aby maximalizovaly obchodní hodnotu propojení vašich zařízení IoT s cloudem Azure.  Pokud se právě začíná vaše cesta k IoT, počínaje IoT Hub k podpoře scénářů příjmu dat se zajistí, že budete mít okamžitý přístup k plnohodnotným funkcím IoT až po jejich podnikání a technických potřebách.

Následující tabulka poskytuje podrobnosti o tom, jak se dvě úrovně IoT Hub porovnávají s Event Hubs při jejich hodnocení pro funkce IoT. Další informace o úrovních Standard a Basic IoT Hub najdete v tématu [Jak zvolit správnou IoT Hub vrstvu](iot-hub-scaling.md).

| Schopnost IoT | Úroveň Standard IoT Hub | IoT Hub úrovně Basic | Event Hubs |
| --- | --- | --- | --- |
| Zasílání zpráv ze zařízení do cloudu | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] |
| Protokoly: HTTPS, AMQP, AMQP přes WebSockets | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] |
| Protokoly: MQTT, MQTT přes WebSockets | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] |  |
| Identita vázaná na zařízení | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] |  |
| Nahrávání souborů ze zařízení | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] |  |
| Device Provisioning Service | ![Zaškrtnout][checkmark] | ![Zaškrtnout][checkmark] |  |
| Zasílání zpráv z cloudu na zařízení | ![Zaškrtnout][checkmark] |  |  |
| Zařízení se zdvojeným zařízením a správou zařízení | ![Zaškrtnout][checkmark] |  |  |
| Datové proudy zařízení (Preview) | ![Zaškrtnout][checkmark] |  |  |
| IoT Edge | ![Zaškrtnout][checkmark] |  |  |

I když je jediným případem použití přijímání dat typu zařízení-Cloud, důrazně doporučujeme použít IoT Hub, protože poskytuje službu, která je navržena pro připojení zařízení IoT. 

### <a name="next-steps"></a>Další kroky

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si téma [Příručka pro vývojáře IoT Hub](iot-hub-devguide.md).

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png
