---
title: Architektura IoT Propojená logistika | Dokumenty společnosti Microsoft
description: Architektura šablony aplikace IoT Connected Logistics pro IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: 8925b98269b67bfb8a96cb057982ee4e396f17ed
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686224"
---
# <a name="architecture-of-iot-central-connected-logistics-application-template"></a>Architektura propojené logistické aplikační šablony IoT Central



Partneři & zákazník můžou použít šablonu aplikace & podle pokynů k vývoji **komplexních propojených logistických řešení**.

> [!div class="mx-imgBorder"]
> ![připojená logistická řídicí panel](./media/concept-connected-logistics-architecture/connected-logistics-architecture.png)

1. Sada značek IoT odesílání telemetrických dat do zařízení brány
2. Zařízení brány odesílající telemetrii a agregované přehledy do IoT Central
3. Data jsou směrována na požadovanou službu Azure pro manipulaci
4. Služby Azure, jako je ASA nebo Azure Functions, se můžou použít k přeformátování datových proudů a odesílání na požadované účty úložiště. 
5. Různé obchodní pracovní postupy mohou být poháněny obchodními aplikacemi koncových uživatelů

## <a name="details"></a>Podrobnosti
Následující část popisuje každou část konceptuální architektury Telemetrie ingestování z IoT tagy & brány

## <a name="iot-tags"></a>Značky IoT
Značky IoT poskytují fyzické, okolní a environmentální senzorové funkce, jako je teplota, vlhkost, náraz, náklon &světlo. Značky IoT se obvykle připojují k zařízení brány prostřednictvím Zigbee (802.15.4). Značky jsou levnější senzory; tak mohou být vyřazeny na konci typické logistické cesty, aby se zabránilo výzvám s reverzní logistikou.

## <a name="gateway"></a>brána
Brány mohou také fungovat jako značky IoT s jejich možnostmi snímání okolí. Brána umožňuje upstream Azure IoT cloud připojení (MQTT) pomocí mobilních kanálů Wi-Fi.  Režimy Bluetooth, NFC a 802.15.4 Wireless Sensor Network (WSN) se používají pro následnou komunikaci se značkami IoT. Brány poskytují komplexní zabezpečené připojení ke cloudu, párování značek IoT, agregaci dat senzorů, uchovávání dat a možnost konfigurace prahových hodnot alarmů.

## <a name="device-management-with-iot-central"></a>Správa zařízení s IoT Central 
Azure IoT Central je platforma pro vývoj řešení, která zjednodušuje připojení, konfiguraci a správu zařízení IoT. Platforma výrazně snižuje zátěž a náklady na správu zařízení IoT, provoz a související vývoj. Zákazníci & partneři mohou vytvářet komplexní podniková řešení, aby dosáhli smyčky digitální zpětné vazby v logistice.

## <a name="business-insights-and-actions-using-data-egress"></a>Obchodní přehledy a akce pomocí odchozího přenosu dat 
Platforma IoT Central poskytuje bohaté možnosti rozšiřitelnosti prostřednictvím průběžného exportu dat (CDE) a api. Obchodní přehledy založené na zpracování telemetrických dat nebo nezpracovaná telemetrie se obvykle exportují do upřednostňované obchodní aplikace. Toho lze dosáhnout pomocí webhooku, service bus, centra událostí nebo úložiště objektů blob k vytváření, trénování a nasazování modelů strojového učení & další informace obohatit.

## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak nasadit [šablonu řešení pro propojenou logistiku](./tutorial-iot-central-connected-logistics.md)
* Další informace o [maloobchodních šablonách IoT Central](./overview-iot-central-retail.md)
* Další informace o IoT Central najdete v [přehledu IoT Central](../core/overview-iot-central.md)
