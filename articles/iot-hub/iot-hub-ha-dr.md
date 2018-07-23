---
title: Azure IoT Hub vysokou dostupnost a zotavení po havárii | Dokumentace Microsoftu
description: Popisuje funkce Azure a IoT Hub, které pomáhají při vytváření vysoce dostupných řešení Azure IoT s po havárii funkcím pro obnovení.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/13/2017
ms.author: elioda
ms.openlocfilehash: 992c42511f7bc9e9af71ff552ee91bc2472ebcf8
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185699"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>IoT Hub vysokou dostupnost a zotavení po havárii
Jako službu Azure IoT Hub poskytuje vysokou dostupnost (HA) pomocí redundance na úrovni oblasti Azure, bez jakékoli další práce požadováno řešením. Platforma Microsoft Azure také zahrnuje funkce, které vám pomůžou vytvářet řešení s možností zotavení po havárii nebo dostupnosti mezi oblastmi. Pokud chcete zadat globální, mezi různými oblastmi vysoká dostupnost pro zařízení nebo uživatele, využijte tyto funkce zotavení po Havárii Azure. Tento článek [Azure obchodní kontinuity podnikových procesů technickou pomoc](../resiliency/resiliency-technical-guidance.md) popisuje předdefinované funkce v Azure pro provozní kontinuitu a zotavení po Havárii. [Zotavení po havárii a vysoká dostupnost pro aplikace Azure] [ Disaster recovery and high availability for Azure applications] dokument obsahuje pokyny k architektuře strategie pro aplikace v Azure zajistit vysokou dostupnost a zotavení po Havárii.

## <a name="azure-iot-hub-dr"></a>Azure IoT Hub zotavení po Havárii
Kromě HA uvnitř oblasti služby IoT Hub implementuje mechanismus převzetí služeb při selhání pro zotavení po havárii, které vyžadují zásah od uživatele. IoT Hub zotavení po Havárii je držitelem zahájeno a plánovaná doba obnovení (RTO) 2-26 hodin a následujících bodů obnovení (rpo):

| Funkce | CÍL BODU OBNOVENÍ |
| --- | --- |
| Dostupnost služeb pro operace registru a komunikace |Může dojít ke ztrátě CName |
| Data identit v registru identit |0 – 5 minut ztráty dat |
| Zprávy typu zařízení-cloud |Všechny nepřečtené zprávy jsou ztraceny |
| Monitorování zprávy operací |Všechny nepřečtené zprávy jsou ztraceny |
| Zprávy typu cloud zařízení |0 – 5 minut ztráty dat |
| Frontu zpětné vazby typu cloud zařízení |Všechny nepřečtené zprávy jsou ztraceny |
| Data dvojčete zařízení |0 – 5 minut ztráty dat |
| Úlohy nadřazený a zařízení |0 – 5 minut ztráty dat |

## <a name="regional-failover-with-iot-hub"></a>Regionální převzetí služeb při selhání pomocí služby IoT Hub
Dokončení zpracování topologií nasazení v řešeních IoT sahá nad rámec tohoto článku. Tento článek popisuje článek *regionální převzetí služeb při selhání* model nasazení za účelem vysoké dostupnosti a zotavení po havárii.

V modelu regionální převzetí služeb při selhání řešení back end spuštění primárně na jednom místě datacentra. Sekundární centra IoT a back-endu se nasazují do jiného umístění datového centra. Pokud v primárním datovém centru služby IoT hub odkážete výpadku nebo dojde k přerušení připojení k síti ze zařízení na primární datové centrum, zařízení použijte koncový bod služby sekundární. Může zlepšit dostupnost řešení díky implementaci modelu převzetí služeb při selhání mezi oblastmi místo zůstávají v rámci jedné oblasti. 

Na vysoké úrovni k implementaci modelu regionální převzetí služeb při selhání službou IoT Hub, budete potřebovat následující:

* **Sekundární služby IoT hub a zařízení směrování logiky**: Pokud narušení služby v primární oblasti, zařízení musí začínat připojení k sekundární oblasti. Vzhledem k s ohledem na stav povaze většina služeb zahrnutých, je běžné, že správců řešení, které spustí proces převzetí služeb při selhání mezi oblastmi. Je nejlepší způsob, jak komunikovat se zachováním potřebné kontroly nad procesu, nový koncový bod do zařízení, aby pravidelně kontrolovat *concierge* aktuální aktivní koncový bod služby. Služba Concierge, která může být webové aplikace, která je replikována a udržovat dostupný pomocí technik DNS přesměrování (například pomocí [Azure Traffic Manager][Azure Traffic Manager]).
* **Replikace registru identit**: má být použitelná, sekundární služby IoT hub musí obsahovat všechny identity zařízení, které se můžete připojit k řešení. Řešení musí zachovat geograficky replikovaných záloh identit zařízení a jejich nahrávání do služby IoT hub sekundární před přepnutím aktivní koncový bod pro zařízení. Funkce exportu identity zařízení služby IoT Hub je užitečná v tomto kontextu. Další informace najdete v tématu [Příručka pro vývojáře IoT Hub – registr identit][IoT Hub developer guide - identity registry].
* **Slučování logiky**: když primární oblast opět k dispozici, všechny stav a data, které byly vytvořeny v sekundární lokalitě se musí migrovat zpět do primární oblasti. Tento stav a data většinou se týkají identit zařízení a metadata aplikace, které je potřeba sloučit s primární služby IoT hub a jiných úložišť specifické pro aplikaci v primární oblasti. Pro zjednodušení tohoto kroku, měli byste použít idempotentní operace. Idempotentní operace minimalizovala vedlejších účinků z konečné konzistentní distribuci událostí a duplicitní hodnoty nebo mimo pořadí doručení událostí. Kromě toho logika aplikace by se měly navrhovat tolerovat potenciálním nekonzistencím nebo "mírně" zastaralý stav. Tato situace může nastat z důvodu další čas potřebný pro systém "opravy" podle plánovaných bodů obnovení (RPO).

## <a name="next-steps"></a>Další postup
Další informace o službě Azure IoT Hub na následujících odkazech:

* [Začínáme s IoT hub (kurz)][lnk-get-started]
* [Co je Azure IoT Hub?][What is Azure IoT Hub?]

[Disaster recovery and high availability for Azure applications]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Azure Traffic Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[IoT Hub developer guide - identity registry]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: quickstart-send-telemetry-dotnet.md
[What is Azure IoT Hub?]: about-iot-hub.md
