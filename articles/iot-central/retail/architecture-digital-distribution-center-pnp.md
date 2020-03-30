---
title: Architektura IoT Central Digital Distribution Center | Dokumenty společnosti Microsoft
description: Architektura šablony aplikace Digital Distribution Center pro IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 359aaf7bbde5501716f3d8c4229b2b90918fd5de
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77020977"
---
# <a name="architecture-of-iot-central-digital-distribution-center-application-template"></a>Architektura aplikační šablony digitálního distribučního centra IoT Central



Partneři a zákazníci mohou pomocí šablony aplikace & následujícípokyny k vývoji řešení **digitálních distribučních center** od konce ke konci.

> [!div class="mx-imgBorder"]
> ![digitální distribuční centrum](./media/concept-ddc-architecture/digital-distribution-center-architecture.png)

1. Sada ioT senzorů odesílajících telemetrická data do zařízení brány
2. Zařízení brány odesílající telemetrii a agregované přehledy do IoT Central
3. Data jsou směrována na požadovanou službu Azure pro manipulaci
4. Služby Azure, jako je ASA nebo Azure Functions, se můžou použít k přeformátování datových proudů a odesílání na požadované účty úložiště. 
5. Zpracovaná data jsou uložena v horkém úložišti pro akce téměř v reálném čase nebo v chladírenském úložišti pro další vylepšení přehledu, která jsou založena na analýze ML nebo dávky. 
6. Logic Apps lze použít k napájení různých obchodních pracovních postupů v obchodních aplikacích koncových uživatelů

## <a name="details"></a>Podrobnosti
Následující část popisuje každou část konceptuální architektury

## <a name="video-cameras"></a>Videokamery 
Videokamery jsou primární senzory v tomto digitálně propojeném ekosystému v podnikovém měřítku. Pokroky ve strojovém učení a umělé inteligenci, které umožňují přeměnu videa na strukturovaná data a jeho zpracování na hraniční úrovni před odesláním do cloudu. Pomocí IP kamer můžeme zachytit obrázky, komprimovat je na kameře a pak odeslat komprimovaná data přes okraj výpočetních prostředků pro kanál analýzy videa nebo použít kamery GigE k zachycení snímků na senzoru a následné odeslání těchto obrázků přímo do Azure IoT Edge , který se pak komprimuje před zpracováním v kanálu analýzy videa. 

## <a name="azure-iot-edge-gateway"></a>Hraniční brána Azure IoT
Úlohy "kamery jako senzory" a hraniční úlohy jsou spravovány místně pomocí Azure IoT Edge a datový proud kamery se zpracovává pomocí analytického kanálu. Kanál zpracování analýzy videa v Azure IoT Edge přináší mnoho výhod, včetně snížení doby odezvy, nízké spotřeby šířky pásma, což má za následek nízkou latenci pro rychlé zpracování dat. Do cloudu se k dalšímu postupu nebo vyšetřování odešlou jenom nejdůležitější metadata, přehledy nebo akce. 

## <a name="device-management-with-iot-central"></a>Správa zařízení s IoT Central 
Azure IoT Central je platforma pro vývoj řešení, která zjednodušuje připojení, konfiguraci a správu brány Azure IoT Edge & zařízení IoT. Platforma výrazně snižuje zátěž a náklady na správu zařízení IoT, provoz a související vývoj. Zákazníci & partneři mohou vytvářet komplexní podniková řešení, aby dosáhli smyčky digitální zpětné vazby v distribučních centrech.

## <a name="business-insights-and-actions-using-data-egress"></a>Obchodní přehledy a akce využívající odchozí přenos dat 
Platforma IoT Central poskytuje bohaté možnosti rozšiřitelnosti prostřednictvím průběžného exportu dat (CDE) a api. Obchodní přehledy, které jsou založeny na zpracování telemetrických dat nebo nezpracovaná telemetrie jsou obvykle exportovány do upřednostňované obchodní aplikace. Toho lze dosáhnout prostřednictvím webhooku, Service Bus, centra událostí nebo úložiště objektů blob k vytváření, trénování a nasazování modelů strojového učení a dalšího obohacení přehledů.

## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak nasadit [šablonu digitálního distribučního centra](./tutorial-iot-central-digital-distribution-center-pnp.md)
* Další informace o [maloobchodních šablonách IoT Central](./overview-iot-central-retail-pnp.md)
* Další informace o IoT Central najdete v [přehledu IoT Central](../core/overview-iot-central.md)
