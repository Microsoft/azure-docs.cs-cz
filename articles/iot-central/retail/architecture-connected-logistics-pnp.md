---
title: Logistika propojené IoT architektury | Microsoft Docs
description: Architektura šablony logistické aplikace připojené k IoT pro IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: f48ebe9ca6477bc2151c9ca3fed76be152cf648a
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957500"
---
# <a name="architecture-of-iot-central-connected-logistics-application-template"></a>Architektura šablony logistické aplikace IoT Central připojené

Partneři & zákazník může využít šablonu aplikace & následující pokyny pro vývoj koncových řešení, která se dokončí koncovým **připojením**.

> [!div class="mx-imgBorder"]
> řídicí panel ![připojená logistika](./media/concept-connected-logistics-architecture/connected-logistics-architecture.png)

1. Sada značek IoT odesílajících data telemetrie na zařízení brány
2. Zařízení brány odesílající telemetrii a agregované přehledy pro IoT Central
3. Data jsou směrována do požadované služby Azure pro manipulaci
4. Služby Azure, jako je ASA nebo Azure Functions, se dají využít k přeformátování datových proudů a posílání do požadovaných účtů úložiště. 
5. Různé obchodní pracovní postupy můžou být napájené pomocí podnikových aplikací koncových uživatelů.

## <a name="details"></a>Podrobnosti
Následující část popisuje jednotlivé části ingestování telemetrie architektury konceptuální architektury z značek IoT & Branch.

## <a name="iot-tags"></a>Značky IoT
Značky IoT poskytují možnosti fyzických, okolních a environmentálních senzorů, jako je teplota, vlhkost, náraz, náklon & světlo. Značky IoT se obvykle připojují k zařízení brány prostřednictvím ZigBee (802.15.4). Značky jsou levné senzory; Proto mohou být zahozeny na konci typické logistické cesty, aby se zabránilo problémům s reverzním logistikou.

## <a name="gateway"></a>brána
Brány mohou fungovat také jako značky IoT s možnostmi okolního průzkumu. Brána umožňuje navázat Azure IoT Cloud konektivitu (MQTT) přes mobilní kanály Wi-Fi.  Režimy Bluetooth, NFC a 802.15.4 bezdrátového senzoru sítě (WSN) se používají pro bezkontaktní komunikaci se značkami IoT. Brány poskytují koncovým zabezpečeným cloudovým připojením, párování značek IoT, agregaci dat senzorů, uchovávání dat a možnost konfigurace prahových hodnot alarmu.

## <a name="device-management-with-iot-central"></a>Správa zařízení pomocí IoT Central 
Azure IoT Central je vývojová platforma pro řešení, která zjednodušuje připojení zařízení IoT, konfiguraci a správu. Platforma významně snižuje zatížení a náklady na správu zařízení IoT, operace a související vývoj. Zákazníci, kteří & partneři, mohou vytvořit koncová podniková řešení pro zajištění smyčky digitální zpětné vazby v logistikě.

## <a name="business-insights--actions-via-data-egress"></a>& Akcí v obchodní Insights prostřednictvím výstupních dat 
Platforma IoT Central poskytuje bohatou možnost rozšiřitelnosti prostřednictvím exportu a rozhraní API pro průběžné exporty dat (CDE). Obchodní přehledy založené na zpracování dat telemetrie nebo nezpracovaná telemetrie se obvykle exportují do upřednostňované obchodní aplikace. Toho je možné dosáhnout pomocí Webhooku, Service Bus, centra událostí nebo úložiště objektů BLOB k sestavování, výuce a nasazení modelů strojového učení & další obohacení přehledů.

## <a name="next-steps"></a>Další kroky
* Naučte se nasadit [připojenou šablonu logistického řešení](./tutorial-iot-central-connected-logistics-pnp.md) .
* Další informace o [IoT Central maloobchodních šablon](./overview-iot-central-retail-pnp.md)
* Další informace o IoT Central najdete v tématu [IoT Central Overview](../core/overview-iot-central-pnp.md) .
