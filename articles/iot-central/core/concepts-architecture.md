---
title: Koncepty architektury v Azure IoT Central | Microsoft Docs
description: Tento článek představuje klíčové koncepty týkající se architektury Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: bcda4ca252101ed1505f71a1b5f9fe9a0d8d16b9
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728387"
---
# <a name="azure-iot-central-architecture"></a>Architektura služby Azure IoT Central

Tento článek poskytuje přehled klíčových konceptů v architektuře Azure IoT Central.

## <a name="devices"></a>Zařízení

Zařízení vyměňují data v aplikaci Azure IoT Central. Zařízení může:

- Odesílejte měření, jako je telemetrie.
- Synchronizujte nastavení s vaší aplikací.

V Azure IoT Central se data, která si zařízení může vyměňovat s vaší aplikací, zadávají v šabloně zařízení. Další informace o šablonách zařízení najdete v tématu [šablony zařízení](concepts-device-templates.md).

Další informace o tom, jak se zařízení připojují k aplikaci Azure IoT Central, najdete v tématu [připojení zařízení](concepts-get-connected.md).

## <a name="azure-iot-edge-devices"></a>Zařízení Azure IoT Edge

I zařízení vytvořená pomocí [sad SDK služby Azure IoT](https://github.com/Azure/azure-iot-sdks)můžete také připojit [zařízení Azure IoT Edge](../../iot-edge/about-iot-edge.md) k aplikaci IoT Central. IoT Edge umožňuje spustit cloudovou logiku a vlastní logiku přímo na zařízeních IoT spravovaných pomocí IoT Central. Modul runtime IoT Edge umožňuje:

- Instalace a aktualizace úloh na zařízení.
- Udržujte na zařízení standardy zabezpečení IoT Edge.
- Ujistěte se, že moduly IoT Edge jsou vždycky spuštěné.
- Oznamte stav modulu v cloudu pro vzdálené monitorování.
- Spravujte komunikaci mezi moduly pro příjem a IoT Edge zařízení, mezi moduly v zařízení IoT Edge a mezi IoT Edgem zařízením a cloudem.

![IoT Central Azure s Azure IoT Edge](./media/concepts-architecture/iotedge.png)

IoT Central povoluje pro IoT Edge zařízení následující funkce:

- Šablony zařízení, které popisují možnosti IoT Edgeho zařízení, například:
  - Možnost nahrávání manifestu nasazení, která pomáhá spravovat manifest pro loďstva zařízení.
  - Moduly, které se spouštějí na zařízení IoT Edge.
  - Telemetrii každý modul odesílá.
  - Vlastnosti každého modulu hlásí.
  - Příkazy, na které každý modul reaguje.
  - Vztahy mezi zařízením IoT Edge brány a zařízením pro příjem dat.
  - Vlastnosti cloudu, které nejsou uložené na zařízení IoT Edge.
  - Vlastní nastavení, řídicí panely a formuláře, které jsou součástí vaší aplikace IoT Central.

  Další informace najdete v článku [připojení zařízení Azure IoT Edge k aplikaci Azure IoT Central](./concepts-iot-edge.md) .

- Možnost zřídit ve velkém měřítku IoT Edge zařízení pomocí služby Azure IoT Device Provisioning
- Pravidla a akce.
- Vlastní řídicí panely a analýzy.
- Průběžný export dat telemetrie ze zařízení IoT Edge.

### <a name="iot-edge-device-types"></a>IoT Edge typy zařízení

IoT Central klasifikuje IoT Edge typy zařízení následujícím způsobem:

- Zařízení typu list. Zařízení IoT Edge může mít zařízení pro příjem dat, ale tato zařízení nejsou zřízená v IoT Central.
- Zařízení brány se zařízeními pro příjem dat. Zařízení brány i zařízení pro příjem dat jsou zřízena v IoT Central

![IoT Central s přehledem IoT Edge](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>Vzory IoT Edge

IoT Central podporuje následující vzory IoT Edge zařízení:

#### <a name="iot-edge-as-leaf-device"></a>IoT Edge jako zařízení na list

![IoT Edge jako zařízení na list](./media/concepts-architecture/edgeasleafdevice.png)

Zařízení IoT Edge se zřídí v IoT Central a všechna zařízení s příchozím výstupem a jejich telemetrie se reprezentují jako přicházející ze zařízení IoT Edge. Podřízená zařízení připojená k IoT Edge zařízení nejsou zřízená v IoT Central.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>Zařízení IoT Edge brány připojené k zařízením pro příjem dat s identitou

![IoT Edge s identitou pro osobní zařízení](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

Zařízení IoT Edge se zřizuje v IoT Central společně se zařízeními pro příjem dat, která jsou připojená ke IoT Edge zařízení. Podpora modulu runtime pro zřizování podřízených zařízení přes bránu není momentálně podporovaná.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>Zařízení IoT Edge brány připojené k zařízením pro příjem dat pomocí identity poskytované IoT Edge bránou

![IoT Edge se zařízením pro příjem dat bez identity](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

Zařízení IoT Edge se zřizuje v IoT Central společně se zařízeními pro příjem dat, která jsou připojená ke IoT Edge zařízení. Běhová podpora brány, která poskytuje identitu pro podřízená zařízení a zřizování zařízení pro příjem dat, se v tuto chvíli nepodporuje. Pokud přenesete vlastní modul překladu identity, IoT Central může tento model podporovat.

## <a name="cloud-gateway"></a>Cloudová brána

Azure IoT Central používá Azure IoT Hub jako cloudovou bránu, která umožňuje připojení zařízení. IoT Hub povoluje:

- Přijímání dat v cloudu se škálováním.
- Správa zařízení.
- Zabezpečte připojení zařízení.

Další informace o IoT Hub najdete v tématu [Azure IoT Hub](../../iot-hub/index.yml).

Další informace o připojení zařízení v Azure IoT Central najdete v tématu [připojení zařízení](concepts-get-connected.md).

## <a name="data-stores"></a>Úložiště dat

Azure IoT Central ukládá data aplikací v cloudu. Uložená data aplikací zahrnují:

- Šablony zařízení.
- Identity zařízení.
- Metadata zařízení.
- Data uživatelů a rolí.

Azure IoT Central využívá úložiště časových řad k měření dat odesílaných z vašich zařízení. Data časové řady ze zařízení, která služba Analytics používá.

## <a name="data-export"></a>Export dat

V aplikaci IoT Central Azure můžete [data průběžně exportovat](howto-export-data.md) do vlastních instancí Azure Event Hubs a Azure Service Bus. Data můžete také pravidelně exportovat do svého účtu služby Azure Blob Storage. IoT Central může exportovat měření, zařízení a šablony zařízení.

## <a name="batch-device-updates"></a>Aktualizace zařízení Batch

V aplikaci IoT Central Azure můžete [vytvářet a spouštět úlohy](howto-run-a-job.md) pro správu připojených zařízení. Tyto úlohy umožňují hromadné aktualizace vlastností a nastavení zařízení nebo spouštění příkazů. Můžete například vytvořit úlohu pro zvýšení rychlosti ventilátoru pro více chladírenských prodejních počítačů.

## <a name="role-based-access-control-rbac"></a>Řízení přístupu na základě role (RBAC)

Každá aplikace IoT Central má vlastní vestavěný systém RBAC. [Správce může definovat pravidla přístupu](howto-manage-users-roles.md) pro aplikaci Azure IoT Central pomocí jedné z předdefinovaných rolí nebo vytvořením vlastní role. Role určují, ke kterým oblastem aplikace má uživatel přístup a jaké akce můžou provádět.

## <a name="security"></a>Zabezpečení

Mezi funkce zabezpečení v rámci Azure IoT Central patří:

- Data se šifrují při přenosu a v klidovém stavu.
- Ověřování se poskytuje buď pomocí Azure Active Directory, nebo pomocí účtu Microsoft. Podporuje se dvojúrovňové ověřování.
- Úplná izolace tenanta.
- Zabezpečení na úrovni zařízení.

## <a name="next-steps"></a>Další kroky

Teď, když jste se dozvěděli o architektuře Azure IoT Central, je doporučeným dalším krokem informace o [připojení zařízení](concepts-get-connected.md) ve službě Azure IoT Central.