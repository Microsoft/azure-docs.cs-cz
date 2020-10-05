---
title: Správa správy inteligentních zásob v architektuře IoT | Microsoft Docs
description: Architektura šablony správy inteligentního inventáře IoT pro IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: 9e0b4a3c22c1e6008bdce7fecc1d122fe317dd58
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "81686210"
---
# <a name="architecture-of-iot-central-smart-inventory-management-application-template"></a>Architektura šablony aplikace IoT Central správy inteligentního inventáře

Partneři a zákazníci můžou použít šablonu aplikace a následující pokyny k vývoji kompletních řešení pro **správu inteligentních inventářů** .

> [!div class="mx-imgBorder"]
> ![Správa inteligentních inventářů](./media/concept-smart-inventory-mgmt-architecture/smart-inventory-management-architecture.png)

1. Sada senzorů IoT odesílajících data telemetrie na zařízení brány
2. Zařízení brány odesílající telemetrii a agregované přehledy pro IoT Central
3. Data jsou směrována do požadované služby Azure pro manipulaci
4. Služby Azure, jako je ASA nebo Azure Functions, se dají použít k přeformátování datových proudů a posílání do požadovaných účtů úložiště. 
5. Zpracovaná data se ukládají do horkého úložiště pro akce téměř v reálném čase nebo z chladírenského úložiště pro další vylepšení Insights založená na ML nebo analýze dávek. 
6. Logic Apps můžete použít k napájení různých podnikových pracovních postupů v podnikových aplikacích koncových uživatelů.

## <a name="details"></a>Podrobnosti
Následující část popisuje jednotlivé části ingestování telemetrie architektury konceptuální architektury ze služby radiofrekvenční identifikace (RFID), značky s nízkými energií (v/v).

## <a name="rfid-tags"></a>Štítky RFID
Štítky RFID odesílají data o položce prostřednictvím rádiových vln. Pokud není zadaný, značky RFID obvykle nemají baterii. Značky získají energii od rádiových vln vygenerovaných čtecím modulem a odesílají signál zpátky ke čtečce RFID.

## <a name="ble-tags"></a>Tagy pro tabulku
Energetická signalizace vysílá pakety dat v pravidelných intervalech. Data o majáku zjišťují čtenáři nebo nainstalují služby na smartphonech a pak je přenáší do cloudu.

## <a name="rfid--ble-readers"></a>& čtenáři v nástroji pro čtení obsahu RFID
Čtečka RFID převede rádiové vlny na užitečnější formu dat. Informace shromažďované ze značek se pak ukládají na místní hraniční Server nebo se odesílají do cloudu pomocí JSON-RPC 2,0 přes MQTT.
Čtečka v aplikaci návštěvník se také označuje jako přístupový bod (přístupový bod), která je podobná čtečce RFID. Slouží k detekci okolních signálů Bluetooth a k přenosu své zprávy do místní Azure IoT Edge nebo cloudu s využitím JSON – RPC 2,0 přes MQTT.
Mnoho čtenářů dokáže číst signály RFID a majáku a poskytovat další senzory související s teplotou, vlhkostí, akcelerometrem a vybavený gyroskopem.

## <a name="azure-iot-edge-gateway"></a>Brána Azure IoT Edge
Azure IoT Edge Server poskytuje místo pro místní zpracování těchto dat před jejich odesláním do cloudu. Můžete také nasadit cloudové úlohy umělal Intelligence, Azure a služeb třetích stran s obchodní logikou pomocí standardních kontejnerů.

## <a name="device-management-with-iot-central"></a>Správa zařízení pomocí IoT Central 
Azure IoT Central je vývojová platforma pro řešení, která zjednodušuje připojení zařízení IoT, konfiguraci a správu. Platforma významně snižuje zatížení a náklady na správu zařízení IoT, operace a související vývoj. Zákazníci, kteří & partneři, mohou vytvořit koncová podniková řešení pro zajištění smyčky digitální zpětné vazby v rámci správy inventáře.

## <a name="business-insights--actions-using-data-egress"></a>& akcí pro Business Insights s využitím odchozího přenosu dat 
Platforma IoT Central poskytuje bohatou možnost rozšiřitelnosti prostřednictvím exportu a rozhraní API pro průběžné exporty dat (CDE). Obchodní přehledy založené na zpracování dat telemetrie nebo nezpracovaná telemetrie se obvykle exportují do upřednostňované obchodní aplikace. Dá se dosáhnout pomocí Webhooku, Service Bus, centra událostí nebo úložiště objektů BLOB k sestavování, výuce a nasazení modelů strojového učení & dalších obohacení přehledů.

## <a name="next-steps"></a>Další kroky
* Naučte se nasadit [šablonu správy inteligentních inventářů](./tutorial-iot-central-smart-inventory-management.md) .
* Další informace o [IoT Central maloobchodních šablon](./overview-iot-central-retail.md)
* Další informace o IoT Central najdete v tématu [IoT Central Overview](../core/overview-iot-central.md) .
