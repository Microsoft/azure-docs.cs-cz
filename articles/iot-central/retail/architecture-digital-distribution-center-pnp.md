---
title: Architektura IoT Central digital Distribution Center | Microsoft Docs
description: Architektura šablony aplikace digitálního distribučního centra pro IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 2ab79cccaafeb1b48100285a5fd495340b545278
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615340"
---
# <a name="architecture-of-iot-central-digital-distribution-center-application-template"></a>Architektura šablony aplikace IoT Central digital Distribution Center

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Partneři & zákazník může využít šablonu aplikace & následující pokyny pro vývoj kompletních řešení pro **distribuci digitálních distribučních center** .

> [!div class="mx-imgBorder"]
> ![](./media/concept-ddc-architecture/digital-distribution-center-architecture.png) digital Distribution Center

1. Sada senzorů IoT odesílajících data telemetrie na zařízení brány
2. Zařízení brány odesílající telemetrii a agregované přehledy pro IoT Central
3. Data jsou směrována do požadované služby Azure pro manipulaci
4. Služby Azure, jako je ASA nebo Azure Functions, se dají využít k přeformátování datových proudů a posílání do požadovaných účtů úložiště. 
5. Zpracovaná data se ukládají do horkého úložiště pro akce téměř v reálném čase nebo z chladírenského úložiště pro další vylepšení v oblasti přehledů založené na ML nebo analýze dávek. 
6. Logic Apps můžete použít k napájení různých podnikových pracovních postupů v podnikových aplikacích koncových uživatelů.

## <a name="details"></a>Podrobnosti
Následující část popisuje jednotlivé části koncepční architektury.

## <a name="video-cameras"></a>Videokamery 
Videokamery jsou primární senzory v tomto nepřipojeném ekosystému s místním škálováním na podnikové úrovni. V strojovém učení a v umělých inteligentních kurzech je možné zasílat do strukturovaných dat a před odesláním do cloudu je zpracovat na hraničních zařízeních. Pomocí fotoaparátů IP můžeme zachytit image, zkomprimovat je na kameře a pak pro kanál video Analytics poslat komprimovaná data přes Edge a pak je pomocí GigE videokamery zachytit na senzor a pak tyto image poslat přímo do Azure IoT Edge , které pak komprimuje před zpracováním v kanálu video Analytics. 

## <a name="azure-iot-edge-gateway"></a>Brána Azure IoT Edge
Pracovní zatížení "kamery jako senzory" a hraniční úlohy jsou spravovány místně pomocí Azure IoT Edge a datový proud kamery se zpracovává pomocí analytického kanálu. Kanál pro zpracování analýzy videí v Azure IoT Edge přináší řadu výhod, včetně snížené doby odezvy, spotřeby s nízkou šířkou pásma, což má za následek nízkou latenci pro rychlé zpracování dat. Do cloudu se posílají jenom nejdůležitější metadata, přehledy nebo akce pro další akce nebo šetření. 

## <a name="device-management-with-iot-central"></a>Správa zařízení pomocí IoT Central 
Azure IoT Central je vývojová platforma pro řešení, která zjednodušuje & zařízení IoT Azure IoT Edge připojení, konfiguraci a správu brány. Platforma významně snižuje zatížení a náklady na správu zařízení IoT, operace a související vývoj. Zákazníci, kteří & partneři, mohou vytvořit koncová podniková řešení pro zajištění smyčky digitální zpětné vazby v distribučních centrech.

## <a name="business-insights--actions-via-data-egress"></a>& Akcí v obchodní Insights prostřednictvím výstupních dat 
Platforma IoT Central poskytuje bohatou možnost rozšiřitelnosti prostřednictvím exportu a rozhraní API pro průběžné exporty dat (CDE). Obchodní přehledy založené na zpracování dat telemetrie nebo nezpracovaná telemetrie se obvykle exportují do upřednostňované obchodní aplikace. Toho je možné dosáhnout pomocí Webhooku, Service Bus, centra událostí nebo úložiště objektů BLOB k sestavování, výuce a nasazení modelů strojového učení & další obohacení přehledů.

## <a name="next-steps"></a>Další kroky
* Postup nasazení šablony nástroje [digital Distribution Center](./tutorial-iot-central-digital-distribution-center-pnp.md)
* Další informace o [IoT Central maloobchodních šablon](./overview-iot-central-retail-pnp.md)
* Další informace o IoT Central najdete v tématu [IoT Central Overview](../core/overview-iot-central-pnp.md) .
