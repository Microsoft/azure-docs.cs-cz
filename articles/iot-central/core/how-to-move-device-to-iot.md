---
title: Postup přesunutí zařízení do Azure IoT Central z IoT Hub
description: Postup přesunutí zařízení do Azure IoT Central z IoT Hub
author: TheRealJasonAndrew
ms.author: v-anjaso
ms.date: 12/20/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 7898f842529b81b80febff444c97b199fbebba3c
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98146455"
---
# <a name="how-to-transfer-a-device-to-azure-iot-central-from-iot-hub"></a>Jak přenést zařízení do Azure IoT Central z IoT Hub

*Tento článek se týká operátorů a vývojářů zařízení.*  

Tento článek popisuje, jak přenést zařízení do aplikace Azure IoT Central z IoT Hub. 

Zařízení se nejdřív připojí ke koncovému bodu DPS, aby načetlo informace, které potřebuje pro připojení k vaší aplikaci. Interně používá vaše aplikace IoT Central službu IoT Hub ke zpracování připojení zařízení.  

Zařízení je možné připojit ke službě IoT Hub přímo pomocí připojovacího řetězce nebo pomocí DPS. [Služba Azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) je trasa pro IoT Central.

## <a name="to-move-the-device"></a>Přesunutí zařízení

Pokud chcete zařízení připojit k IoT Central ze služby IOT hub, musí být zařízení aktualizované:

* [ID oboru](../../iot-dps/concepts-service.md) aplikace IoT Central.
* Klíč odvozený buď z klíče [SAS skupiny](concepts-get-connected.md) , nebo z [certifikátu X. 509](../../iot-hub/iot-hub-x509ca-overview.md)

Aby bylo možné pracovat s IoT Central, musí existovat šablona zařízení, která modeluje vlastnosti/telemetrie/příkazy, které zařízení implementuje. Další informace najdete v tématu věnovaném [připojení k IoT Central](concepts-get-connected.md) a [co jsou šablony zařízení?](concepts-device-templates.md)

## <a name="next-steps"></a>Další kroky

Pokud jste vývojářem zařízení, některé z navrhovaných dalších kroků:

- Přečtěte si ukázkový kód, který ukazuje, jak používat tokeny SAS v [kurzu: vytvoření a připojení klientské aplikace do aplikace Azure IoT Central.](tutorial-connect-device.md)
- Naučte se, jak [připojit zařízení pomocí certifikátů X. 509 pomocí sady SDK pro Node.js zařízení pro IoT Central aplikaci](how-to-connect-devices-x509.md) .
- Naučte se [monitorovat připojení zařízení pomocí Azure CLI](./howto-monitor-devices-azure-cli.md) .
- Zjistěte, jak [v aplikaci IoT Central Azure definovat nový typ zařízení IoT](./howto-set-up-template.md) .
- Přečtěte si o [Azure IoT Edge zařízeních a Azure IoT Central](./concepts-iot-edge.md)