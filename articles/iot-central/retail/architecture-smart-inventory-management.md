---
title: Architektura IoT Smart Inventory Management | Dokumenty společnosti Microsoft
description: Architektura šablony IoT Smart Inventory Management pro IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 9d52b2fa9289da709449a5f1edc527239800dfa1
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000641"
---
# <a name="architecture-of-iot-central-smart-inventory-management-application-template"></a>Architektura šablony aplikace pro inteligentní řízení inventáře IoT Central

Partneři a zákazníci mohou pomocí šablony aplikace a následujících pokynů vyvinout řešení **pro správu inteligentních zásob** od konce.

> [!div class="mx-imgBorder"]
> ![inteligentní řízení zásob](./media/concept-smart-inventory-mgmt-architecture/smart-inventory-management-architecture.png)

1. Sada ioT senzorů odesílajících telemetrická data do zařízení brány
2. Zařízení brány odesílající telemetrii a agregované přehledy do IoT Central
3. Data jsou směrována na požadovanou službu Azure pro manipulaci
4. Služby Azure, jako je ASA nebo Azure Functions, se můžou použít k přeformátování datových proudů a odesílání na požadované účty úložiště. 
5. Zpracovaná data jsou uložena v horkém úložišti pro akce téměř v reálném čase nebo v chladírenském úložišti pro další vylepšení přehledu, která jsou založena na analýze ML nebo dávky. 
6. Logic Apps lze použít k napájení různých obchodních pracovních postupů v obchodních aplikacích koncových uživatelů

## <a name="details"></a>Podrobnosti
Následující část popisuje každou část konceptuální architektury Telemetrie požití z radiofrekvenční identifikace (RFID), Bluetooth nízkoenergetické (BLE) tagy

## <a name="rfid-tags"></a>RFID tagy
RFID tagy přenášejí data o položce rádiovými vlnami. RFID tagy obvykle nemají baterii, pokud není zadána. Tagy přijímají energii z rádiových vln generovaných čtečkou a přenášejí signál zpět k rfid čtečce.

## <a name="ble-tags"></a>Značky BLE
Energetický maják vysílá pakety dat v pravidelných intervalech. Beacon data jsou detekovány BLE čtenáři nebo nainstalovanými službami na smartphonech a pak je přenášejí do cloudu.

## <a name="rfid--ble-readers"></a>RFID & BLE čtečky
RFID čtečka převádí rádiové vlny na použitelnější formu dat. Informace shromážděné ze značek jsou pak uloženy na místním hraničním serveru nebo odeslány do cloudu pomocí JSON-RPC 2.0 přes MQTT.
Ble čtečka také známá jako přístupové body (AP) jsou podobné rfid čtečce. Používá se k detekci blízkých signálů Bluetooth a přenosu jeho zprávy do místní Azure IoT Edge nebo cloud pomocí JSON-RPC 2.0 přes MQTT.
Mnoho čtenářů je schopno číst Signály RFID a majáku a poskytovat další schopnosti senzoru související s teplotou, vlhkostí, akcelerometrem a gyroskopem.

## <a name="azure-iot-edge-gateway"></a>Brána Azure IoT Edge
Server Azure IoT Edge poskytuje místo pro předběžné zpracování dat místně před jejich odesláním do cloudu. Můžeme také nasadit cloudové úlohy umělá inteligence, Azure a služby třetích stran, obchodní logiku pomocí standardních kontejnerů.

## <a name="device-management-with-iot-central"></a>Správa zařízení s IoT Central 
Azure IoT Central je platforma pro vývoj řešení, která zjednodušuje připojení, konfiguraci a správu zařízení IoT. Platforma výrazně snižuje zátěž a náklady na správu zařízení IoT, provoz a související vývoj. Zákazníci & partneři mohou vytvořit komplexní podniková řešení, aby dosáhli smyčky digitální zpětné vazby ve správě zásob.

## <a name="business-insights--actions-using-data-egress"></a>Obchodní přehledy & akce pomocí odchozího přenosu dat 
Platforma IoT Central poskytuje bohaté možnosti rozšiřitelnosti prostřednictvím průběžného exportu dat (CDE) a api. Obchodní přehledy založené na zpracování telemetrických dat nebo nezpracovaná telemetrie se obvykle exportují do upřednostňované obchodní aplikace. Toho lze dosáhnout pomocí webhooku, service bus, centra událostí nebo úložiště objektů blob k vytváření, trénování a nasazování modelů strojového učení & další informace obohatit.

## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak nasadit [šablonu pro inteligentní správu inventáře](./tutorial-iot-central-smart-inventory-management.md)
* Další informace o [maloobchodních šablonách IoT Central](./overview-iot-central-retail.md)
* Další informace o IoT Central najdete v [přehledu IoT Central](../core/overview-iot-central.md)
