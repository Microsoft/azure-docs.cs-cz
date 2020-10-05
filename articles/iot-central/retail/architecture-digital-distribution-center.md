---
title: Architektura IoT Central digital Distribution Center | Microsoft Docs
description: Architektura šablony aplikace digitálního distribučního centra pro IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: b73d065b43aff5f9793e642a102ff8bb2a7be036
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "81000445"
---
# <a name="architecture-of-iot-central-digital-distribution-center-application-template"></a>Architektura šablony aplikace IoT Central digital Distribution Center



Partneři a zákazníci můžou použít šablonu aplikace & následující pokyny pro vývoj kompletních řešení pro **distribuci digitálních distribučních center** .

> [!div class="mx-imgBorder"]
> ![centrum digitální distribuce](./media/concept-ddc-architecture/digital-distribution-center-architecture.png)

1. Sada senzorů IoT odesílajících data telemetrie na zařízení brány
2. Zařízení brány odesílající telemetrii a agregované přehledy pro IoT Central
3. Data jsou směrována do požadované služby Azure pro manipulaci
4. Služby Azure, jako je ASA nebo Azure Functions, se dají použít k přeformátování datových proudů a posílání do požadovaných účtů úložiště. 
5. Zpracovaná data se ukládají do horkého úložiště pro akce téměř v reálném čase nebo z chladírenského úložiště pro další vylepšení Insights založená na ML nebo analýze dávek. 
6. Logic Apps můžete použít k napájení různých podnikových pracovních postupů v podnikových aplikacích koncových uživatelů.

## <a name="details"></a>Podrobnosti
Následující část popisuje jednotlivé části koncepční architektury.

## <a name="video-cameras"></a>Videokamery 
Videokamery jsou primární senzory v tomto nepřipojeném ekosystému s místním škálováním na podnikové úrovni. V strojovém učení a v umělých inteligentních kurzech je možné zasílat do strukturovaných dat a před odesláním do cloudu je zpracovat na hraničních zařízeních. Pomocí fotoaparátů IP můžeme zachytit image, komprimovat je do kamery a pak pro kanál analýzy a přenos komprimovaných dat přes Edge a pomocí GigE videokamery zachytit image na senzoru a pak tyto image poslat přímo do Azure IoT Edge, která se pak před zpracováním v kanálu analýzy videí komprimuje. 

## <a name="azure-iot-edge-gateway"></a>Brána Azure IoT Edge
Pracovní zatížení "kamery jako senzory" a hraniční úlohy jsou spravovány místně pomocí Azure IoT Edge a datový proud kamery se zpracovává pomocí analytického kanálu. Kanál pro zpracování analýzy videí v Azure IoT Edge přináší spoustu výhod, včetně snížené doby odezvy, spotřeby s nízkou šířkou pásma, což má za následek nízkou latenci pro rychlé zpracování dat. Do cloudu se posílají jenom nejdůležitější metadata, přehledy nebo akce pro další akce nebo šetření. 

## <a name="device-management-with-iot-central"></a>Správa zařízení pomocí IoT Central 
Azure IoT Central je vývojová platforma pro řešení, která zjednodušuje & zařízení IoT Azure IoT Edge připojení, konfiguraci a správu brány. Platforma významně snižuje zatížení a náklady na správu zařízení IoT, operace a související vývoj. Zákazníci, kteří & partneři, mohou vytvořit koncová podniková řešení pro zajištění smyčky digitální zpětné vazby v distribučních centrech.

## <a name="business-insights-and-actions-using-data-egress"></a>Obchodní přehledy a akce využívající výstup dat 
Platforma IoT Central poskytuje bohatou možnost rozšiřitelnosti prostřednictvím exportu a rozhraní API pro průběžné exporty dat (CDE). Obchodní přehledy založené na zpracování dat telemetrie nebo nezpracovaná telemetrie se obvykle exportují do upřednostňované obchodní aplikace. Dá se dosáhnout pomocí Webhooku, Service Bus, centra událostí nebo úložiště objektů BLOB pro sestavování, výuku a nasazení modelů strojového učení a další obohacení přehledů.

## <a name="next-steps"></a>Další kroky
* Postup nasazení šablony nástroje [digital Distribution Center](./tutorial-iot-central-digital-distribution-center.md)
* Další informace o [IoT Central maloobchodních šablon](./overview-iot-central-retail.md)
* Další informace o IoT Central najdete v tématu [IoT Central Overview](../core/overview-iot-central.md) .
