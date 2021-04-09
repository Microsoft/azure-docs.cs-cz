---
title: Logistika propojené IoT architektury | Microsoft Docs
description: Architektura šablony logistické aplikace připojené k IoT pro IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: conceptual
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: d1e17dce80c313bf726451c36ec06dd393549600
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832415"
---
# <a name="architecture-of-iot-central-connected-logistics-application-template"></a>Architektura šablony logistické aplikace IoT Central připojené



Partneři & zákazník může použít šablonu aplikace & následující pokyny k vývoji koncových řešení, která se dokončí koncovým **připojením**.

> [!div class="mx-imgBorder"]
> ![řídicí panel připojené logistiky](./media/concept-connected-logistics-architecture/connected-logistics-architecture.png)

1. Sada značek IoT odesílajících data telemetrie na zařízení brány
2. Zařízení brány odesílající telemetrii a agregované přehledy pro IoT Central
3. Data jsou směrována do požadované služby Azure pro manipulaci
4. Služby Azure, jako je ASA nebo Azure Functions, se dají použít k přeformátování datových proudů a posílání do požadovaných účtů úložiště.
5. Různé obchodní pracovní postupy můžou být napájené pomocí podnikových aplikací koncových uživatelů.

## <a name="details"></a>Podrobnosti
Následující část popisuje jednotlivé části ingestování telemetrie architektury konceptuální architektury z značek IoT & Branch.

## <a name="iot-tags"></a>Značky IoT
Značky IoT poskytují možnosti fyzických, okolních a environmentálních senzorů, jako je teplota, vlhkost, náraz, náklon &světlo. Značky IoT se obvykle připojují k zařízení brány prostřednictvím ZigBee (802.15.4). Značky jsou levnější senzory; To znamená, že je můžete zahodit na konci typické logistické cesty, abyste se vyhnuli problémům s reverzní logistikou.

## <a name="gateway"></a>brána
Brány mohou fungovat také jako značky IoT s možnostmi okolního průzkumu. Brána umožňuje navázat Azure IoT Cloud konektivitu (MQTT) pomocí mobilních Wi-Fi kanálů.  Režimy Bluetooth, NFC a 802.15.4 bezdrátového senzoru sítě (WSN) se používají pro bezkontaktní komunikaci se značkami IoT. Brány poskytují koncovým zabezpečeným cloudovým připojením, párování značek IoT, agregaci dat senzorů, uchovávání dat a možnost konfigurace prahových hodnot alarmu.

## <a name="device-management-with-iot-central"></a>Správa zařízení pomocí IoT Central 
Azure IoT Central je vývojová platforma pro řešení, která zjednodušuje připojení zařízení IoT, konfiguraci a správu. Platforma významně snižuje zatížení a náklady na správu zařízení IoT, operace a související vývoj. Zákazníci, kteří & partneři, mohou vytvořit koncová podniková řešení pro zajištění smyčky digitální zpětné vazby v logistikě.

## <a name="business-insights-and-actions-using-data-egress"></a>Obchodní přehledy a akce využívající výstup dat 
Platforma IoT Central poskytuje bohatou možnost rozšiřitelnosti prostřednictvím exportu a rozhraní API pro průběžné exporty dat (CDE). Obchodní přehledy založené na zpracování dat telemetrie nebo nezpracovaná telemetrie se obvykle exportují do upřednostňované obchodní aplikace. Dá se dosáhnout pomocí Webhooku, Service Bus, centra událostí nebo úložiště objektů BLOB k sestavování, výuce a nasazení modelů strojového učení & dalších obohacení přehledů.

## <a name="next-steps"></a>Další kroky
* Naučte se nasadit [připojenou šablonu logistického řešení](./tutorial-iot-central-connected-logistics.md) .
* Další informace o [IoT Central maloobchodních šablon](./overview-iot-central-retail.md)
* Další informace o IoT Central najdete v tématu [IoT Central Overview](../core/overview-iot-central.md) .
