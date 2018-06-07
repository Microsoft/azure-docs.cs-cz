---
title: Azure IoT Hub vysoké dostupnosti a zotavení po havárii | Microsoft Docs
description: Popisuje funkce Azure a IoT Hub, které pomáhají při vytváření vysoce dostupné řešení Azure IoT se po havárii možnosti obnovení.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/13/2017
ms.author: elioda
ms.openlocfilehash: 428209defa554599c01789e6f2a8b62f155b0f2f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633702"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>IoT Hub vysoké dostupnosti a zotavení po havárii
Jako služby Azure IoT Hub poskytuje vysokou dostupnost (HA) pomocí redundance na úrovni oblasti Azure bez další zátěže vyžadují řešení. Platforma Microsoft Azure také obsahuje funkce, které pomáhají při vytváření řešení pomocí možnosti zotavení po havárii nebo mezi oblastmi dostupnosti. Pokud chcete zajistit globální, mezi oblastmi vysoká dostupnost pro zařízení nebo uživatelů, využít výhod těchto funkcí Azure zotavení po Havárii. Článek [Azure obchodní kontinuity technické pokyny](../resiliency/resiliency-technical-guidance.md) popisuje integrované funkce v Azure pro provozní kontinuitu a zotavení po Havárii. [Zotavení po havárii a vysoká dostupnost pro aplikace Azure] [ Disaster recovery and high availability for Azure applications] dokument obsahuje pokyny k architektura s strategie pro aplikace Azure a dosáhnout HA a zotavení po Havárii.

## <a name="azure-iot-hub-dr"></a>Azure IoT Hub DR
Kromě HA uvnitř oblasti IoT Hub implementuje mechanismy převzetí služeb při selhání pro zotavení po havárii, které vyžadují od uživatele žádný zásah. IoT Hub DR samoobslužné inicializaci a má cíli času obnovení (RTO), 2-26 hodin a tyto cíle bodu obnovení (rpo):

| Funkce | PLÁNOVANÝ BOD OBNOVENÍ |
| --- | --- |
| Dostupnost služeb pro operace registru a komunikace |Případné ztrátě CName |
| Data identit v registru identit |0 – 5 minut ztrátě dat. |
| Zprávy typu zařízení-cloud |Dojde ke ztrátě všech nepřečtených zpráv |
| Operace sledování zpráv |Dojde ke ztrátě všech nepřečtených zpráv |
| Zprávy typu cloud zařízení |0 – 5 minut ztrátě dat. |
| Frontu zpětné vazby cloud zařízení |Dojde ke ztrátě všech nepřečtených zpráv |
| Data twin zařízení |0 – 5 minut ztrátě dat. |
| Úlohy nadřazené a zařízení |0 – 5 minut ztrátě dat. |

## <a name="regional-failover-with-iot-hub"></a>Místní převzetí služeb při selhání s centrem IoT
Dokončení zpracování topologií nasazení v řešeních pro IoT je mimo rámec tohoto článku. Popisuje článek *regionální převzetí služeb při selhání* modelu nasazení za účelem vysoké dostupnosti a zotavení po havárii.

V modelu regionální převzetí služeb při selhání řešení back end spustí především na jednom místě datového centra. Sekundární IoT hub a back-end jsou nasazeny v jiném umístění datového centra. Pokud službu IoT hub v primární datové centrum vyskytne výpadku nebo pokud dojde k přerušení připojení k síti ze zařízení na primární datové centrum, zařízení používat koncového bodu služby sekundární. Implementace model převzetí služeb při selhání mezi oblastmi místo zachování v jedné oblasti, lze vylepšit dostupnost řešení. 

Na vysoké úrovni implementovat model místní převzetí služeb při selhání službou IoT Hub, budete potřebovat následující:

* **Sekundární IoT hub a zařízení směrování logiku**: Pokud v primární oblasti dojde k narušení služby, zařízení musí začínat připojení k vaší sekundární oblasti. Vzhledem k rozpoznání stavu povaze většina služeb související se situací, je běžné správců řešení, které spustí proces převzetí služeb při selhání mezi oblast. Nejlepší způsob, jak komunikaci nový koncový bod do zařízení, a zachováním kontroly procesu, je na jejich pravidelně kontrolovat *concierge* služby pro aktuální aktivní koncový bod. Službu concierge může být webové aplikace, která je replikována a uchovávat dosažitelný pomocí technik DNS přesměrování (například pomocí [Azure Traffic Manager][Azure Traffic Manager]).
* **Replikace registru identit**: možné používat, musí obsahovat sekundární IoT hub všechny identity zařízení, které se můžou připojit k řešení. Řešení musí zachovat geograficky replikované zálohy identit zařízení a jejich nahrávání do služby IoT hub sekundární před přepnutím aktivní koncový bod pro zařízení. Funkce exportu identity zařízení IoT Hub je užitečné v tomto kontextu. Další informace najdete v tématu [Příručka vývojáře pro službu IoT Hub - registru identit][IoT Hub developer guide - identity registry].
* **Slučování logiku**: když primární oblasti opět k dispozici, všechny musí být migrovány stav a data, která byla vytvořena v sekundární lokalitě zpět na primární oblasti. Tento stav a data většinou se týkají identit zařízení a metadata aplikace, které je potřeba sloučit s primární IoT hub a jiných úložišť specifické pro aplikaci v primární oblasti. Pro zjednodušení tento krok, měli byste použít idempotent operace. Operace Idempotent minimalizovat vedlejší účinky z daného distribučního konzistentní událostí a z duplikáty nebo doručení na více systémů pořadí událostí. Kromě toho logiku aplikace by měl být pro tolerovat potenciální nekonzistence nebo "mírně" zastaralé stavu. Tato situace může nastat v důsledku další čas potřebný pro systému "retušovat" podle plánovaných bodů obnovení (RPO).

## <a name="next-steps"></a>Další postup
Další informace o službě Azure IoT Hub na následujících odkazech:

* [Začínáme s centra IoT (kurz)][lnk-get-started]
* [Co je Azure IoT Hub?][What is Azure IoT Hub?]

[Disaster recovery and high availability for Azure applications]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Azure Traffic Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[IoT Hub developer guide - identity registry]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[What is Azure IoT Hub?]: iot-hub-what-is-iot-hub.md
