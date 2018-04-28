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
ms.openlocfilehash: b86132b42aef981e6218b27e271e6db645d14071
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Připojení k Azure zařízení IoT: IoT Hub a Event Hubs

Azure poskytuje služby konkrétně vyvinuté u různých typů připojení a komunikace pro připojení vašich dat exponentem cloudu. Azure IoT Hub a Azure Event Hubs jsou cloudové služby, které mohou ingestování velké objemy dat a proces nebo ukládání dat podnikových statistik. Obě služby jsou podobné v tom, že oba podporují přijímání dat s nízkou latencí a vysokou spolehlivostí, ale jsou navrženy pro jiné účely. IoT Hub byla vyvinuta určený speciálně pro připojení zařízení IoT, v odpovídajícím měřítku, do cloudu Azure, zatímco Event Hubs je navržené pro velké objemy dat streamování jedinečné požadavky. Proto společnost Microsoft doporučuje používat Azure IoT Hub pro připojení k Azure zařízení IoT

Azure IoT Hub je Cloudová brána, která se připojuje zařízení IoT ke shromažďování dat na jednotku podnikových statistik a automatizace. Kromě toho IoT Hub obsahuje funkce, které zlepšit komunikaci oddělení vztah mezi zařízeními a back-end systémy. Obousměrná komunikace, které možnosti znamenají, že při přijímat data ze zařízení, můžete také odeslat příkazy a zásady zpět do zařízení, například aktualizovat vlastnosti nebo vyvolání akce správy zařízení.  Tyto možnosti připojení cloud zařízení také zajišťuje důležité schopnosti produktu doručování cloudu intelligence hraniční zařízení s Azure IoT hranou. Jedinečnou identitu úrovni zařízení poskytované služby IoT Hub pomáhá lepšího zabezpečení řešení IoT z možných útoků. 

[Azure Event Hubs] [ Azure Event Hubs] jsou velké objemy dat streamování služby Azure. Je určený pro scénáře vysoké propustnosti data streamování, kde Zákazníci mohou odesílat až miliardy požadavků za den. Služba Event Hubs používá model oddílů pro horizontální škálování vašeho datového proudu a je integrována do velkých objemů dat a analytické služby Azure, včetně Databricks, Stream Analytics, ADLS a HDInsight. S funkcí, jako je zaznamenat centra událostí a automaticky zvýšilo nastavení je tato služba navržený pro podporu velkých objemů dat aplikací a řešení. Kromě toho IoT Hub využívá Event Hubs pro jeho cesty toku telemetrie tak řešení IoT také výhody od strávíte výkonu služby Event Hubs.

To Shrneme, zatímco obě řešení jsou určené pro přijímání dat v masivním měřítku, pouze IoT Hub poskytuje bohaté IoT specifické možnosti, které jsou navržené tak, abyste maximalizovat obchodní hodnota připojení zařízení IoT do cloudu Azure.  Pokud zahajuje právě vám dobře slouží IoT, počínaje IoT Hub pro podporu scénářů přijímání dat se zajistil mít okamžitý přístup k funkcím plné IoT, jakmile je vašim potřebám obchodních a technických vyžadují.

Následující tabulka obsahuje podrobnosti o jak porovnat dvěma vrstvami IoT Hub do centra událostí při hodnocení je pro funkce IoT. Další informace o úrovních standard a basic služby IoT Hub, najdete v části [jak vybrat správné úrovně služby IoT Hub][lnk-scaling].

| Funkce IoT | IoT Hub úrovně standard | Základní úroveň služby IoT Hub | Event Hubs |
| --- | --- | --- | --- |
| Zasílání zpráv typu zařízení cloud | ![Zaškrtnout][1] | ![Zaškrtnout][1] | ![Zaškrtnout][1] |
| Protokoly: AMQP protokolu HTTPS, AMQP, přes objekty webSockets | ![Zaškrtnout][1] | ![Zaškrtnout][1] | ![Zaškrtnout][1] |
| Protokoly: MQTT, MQTT přes objekty webSockets | ![Zaškrtnout][1] | ![Zaškrtnout][1] |  |
| Identita na jedno zařízení | ![Zaškrtnout][1] | ![Zaškrtnout][1] |  |
| Načtení souborů ze zařízení | ![Zaškrtnout][1] | ![Zaškrtnout][1] |  |
| Device Provisioning Service | ![Zaškrtnout][1] | ![Zaškrtnout][1] |  |
| Zasílání zpráv z cloudu na zařízení | ![Zaškrtnout][1] |  |  |
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
