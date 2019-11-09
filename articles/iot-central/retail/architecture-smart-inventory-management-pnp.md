---
title: Správa správy inteligentních zásob v architektuře IoT | Microsoft Docs
description: Architektura šablony správy inteligentního inventáře IoT pro IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 6450169ae2b2d74006eedc66f35338494257594a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889090"
---
# <a name="architecture-of-iot-central-smart-inventory-management-application-template"></a>Architektura šablony aplikace IoT Central správy inteligentního inventáře

Partneři & zákazník může využít šablonu aplikace & následující pokyny pro vývoj kompletních řešení pro **správu inteligentních inventářů** .

> [!div class="mx-imgBorder"]
> ](./media/concept-smart-inventory-mgmt-architecture/smart-inventory-management-architecture.png) ![správy inteligentních inventářů

1. Sada senzorů IoT odesílajících data telemetrie na zařízení brány
2. Zařízení brány odesílající telemetrii a agregované přehledy pro IoT Central
3. Data jsou směrována do požadované služby Azure pro manipulaci
4. Služby Azure, jako je ASA nebo Azure Functions, se dají využít k přeformátování datových proudů a posílání do požadovaných účtů úložiště. 
5. Zpracovaná data se ukládají do horkého úložiště pro akce téměř v reálném čase nebo z chladírenského úložiště pro další vylepšení v oblasti přehledů založené na ML nebo analýze dávek. 
6. Logic Apps můžete použít k napájení různých podnikových pracovních postupů v podnikových aplikacích koncových uživatelů.

## <a name="details"></a>Podrobnosti
Následující část popisuje jednotlivé části ingestování telemetrie architektury konceptuální architektury ze služby radiofrekvenční identifikace (RFID), značky s nízkými energií (v/v).

## <a name="rfid-tags"></a>Štítky RFID
Štítky RFID odesílají data o položce prostřednictvím rádiových vln. Pokud není zadaný, značky RFID obvykle nemají baterii. Značky získají energii od rádiových vln vygenerovaných čtecím modulem a odesílají signál zpátky ke čtečce RFID.

## <a name="ble-tags"></a>Tagy pro tabulku
Energetická signalizace vysílá pakety dat v pravidelných intervalech. Data o majáku zjišťují čtenáři nebo nainstalují služby na smartphonech a pak je přenáší do cloudu.

## <a name="rfid--ble-readers"></a>& Čtenáři v nástroji pro čtení obsahu RFID
Čtečka RFID převede rádiové vlny na užitečnější formu dat. Informace shromažďované ze značek se pak ukládají na místní hraniční Server nebo se odesílají do cloudu prostřednictvím JSON-RPC 2,0 přes MQTT.
Čtečka v aplikaci návštěvník se také označuje jako přístupový bod (přístupový bod), která je podobná čtečce RFID. Používají se k detekci okolních signálů Bluetooth a k přenosu své zprávy do místní Azure IoT Edge nebo cloudu prostřednictvím JSON-RPC 2,0 přes MQTT.
Mnoho čtenářů podporuje čtení signálů pro signály RFID &ch signálů a poskytuje další schopnosti snímače týkající se teplota překročila, vlhkosti a akcelerometru & Gyro.

## <a name="azure-iot-edge-gateway"></a>Brána Azure IoT Edge
Azure IoT Edge Server poskytuje místo pro místní zpracování těchto dat před jejich odesláním do cloudu. Můžete také nasadit cloudové úlohy umělal Intelligence, Azure a služeb třetích stran, obchodní logiku prostřednictvím standardních kontejnerů.

## <a name="device-management-with-iot-central"></a>Správa zařízení pomocí IoT Central 
Azure IoT Central je vývojová platforma pro řešení, která zjednodušuje připojení zařízení IoT, konfiguraci a správu. Platforma významně snižuje zatížení a náklady na správu zařízení IoT, operace a související vývoj. Zákazníci, kteří & partneři, mohou vytvořit koncová podniková řešení pro zajištění smyčky digitální zpětné vazby v rámci správy inventáře.

## <a name="business-insights--actions-via-data-egress"></a>& Akcí v obchodní Insights prostřednictvím výstupních dat 
Platforma IoT Central poskytuje bohatou možnost rozšiřitelnosti prostřednictvím exportu a rozhraní API pro průběžné exporty dat (CDE). Obchodní přehledy založené na zpracování dat telemetrie nebo nezpracovaná telemetrie se obvykle exportují do upřednostňované obchodní aplikace. Toho je možné dosáhnout pomocí Webhooku, Service Bus, centra událostí nebo úložiště objektů BLOB k sestavování, výuce a nasazení modelů strojového učení & další obohacení přehledů.

## <a name="next-steps"></a>Další kroky
* Naučte se nasadit [šablonu správy inteligentních inventářů](./tutorial-iot-central-smart-inventory-management-pnp.md) .
* Další informace o [IoT Central maloobchodních šablon](./overview-iot-central-retail-pnp.md)
* Další informace o IoT Central najdete v tématu [IoT Central Overview](../preview/overview-iot-central.md) .
