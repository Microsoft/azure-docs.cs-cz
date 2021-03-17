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
ms.openlocfilehash: c2d5310d1a664aa2e22d4241d8066e41d9c82bd1
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796716"
---
# <a name="azure-iot-central-architecture"></a>Architektura služby Azure IoT Central

Tento článek poskytuje přehled architektury Microsoft Azure IoT Central.

![Architektura nejvyšší úrovně](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Zařízení

Zařízení vyměňují data v aplikaci Azure IoT Central. Zařízení může:

- Odesílejte měření, jako je telemetrie.
- Synchronizujte nastavení s vaší aplikací.

V Azure IoT Central se data, která si zařízení může vyměňovat s vaší aplikací, zadávají v šabloně zařízení. Další informace o šablonách zařízení najdete v tématu [Správa metadat](#metadata-management).

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

## <a name="analytics"></a>Analýzy

Služba analýzy zodpovídá za generování vlastních dat sestav, která aplikace zobrazuje. Operátor může [přizpůsobit analýzy](howto-create-analytics.md) zobrazené v aplikaci. Analytická služba je postavená na [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) a zpracovává data měření odesílaná z vašich zařízení.

## <a name="rules-and-actions"></a>Pravidla a akce

[Pravidla a akce](tutorial-create-telemetry-rules.md) společně spolupracují na automatizaci úloh v rámci aplikace. Tvůrce může definovat pravidla na základě telemetrie zařízení, například teploty překračující stanovenou prahovou hodnotu. Azure IoT Central využívá procesor datových proudů k určení, kdy jsou podmínky pravidla splněné. Když je splněna podmínka pravidla, aktivuje akci definovanou tvůrcem. Akce může například odeslat e-mail s informacemi o tom, že teplota v zařízení je příliš vysoká.

## <a name="metadata-management"></a>Správa metadat

V aplikaci IoT Central Azure definují šablony zařízení chování a možnosti typů zařízení. Například šablona zařízení chladničky určuje telemetrii, kterou do vaší aplikace posílá chladnička.

![Architektura šablon](media/concepts-architecture/template-architecture.png)

V IoT Central [Šablona zařízení](concepts-device-templates.md) obsahuje:

- **Model zařízení** , který určuje možnosti zařízení, jako je například telemetrie, kterou posílá, vlastnosti, které definují stav zařízení, a příkazy, na které zařízení reaguje. Možnosti zařízení jsou uspořádány do jednoho nebo více rozhraní.
- **Vlastnosti cloudu** určují vlastnosti IoT Central úložišť pro zařízení. Tyto vlastnosti jsou uloženy pouze v IoT Central a nejsou nikdy odesílány do zařízení.
- **Zobrazení** určují řídicí panely a formuláře, které tvůrce vytvoří, aby mohl obsluhu monitorovat a spravovat zařízení.
- **Přizpůsobení** umožní tvůrci přepsat některé definice v modelu zařízení, aby byly lépe relevantní pro IoT Central aplikace.

Aplikace může mít jedno nebo několik simulovaných a reálných zařízení založených na každé šabloně zařízení.

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

## <a name="ui-shell"></a>Prostředí uživatelského rozhraní

Prostředí uživatelského rozhraní je moderní aplikace založená na prohlížeči HTML5, reaguje na ni.
Správce může přizpůsobovat uživatelské rozhraní aplikace pomocí vlastních motivů a úpravou odkazů na odkazy, které odkazují na vaše vlastní prostředky pro usnadnění. Další informace o přizpůsobení uživatelského rozhraní najdete v článku [přizpůsobení uživatelského rozhraní Azure IoT Central](howto-customize-ui.md) .

Operátor může vytvářet přizpůsobené řídicí panely aplikací. Můžete mít několik řídicích panelů, které zobrazují různá data a mezi nimi přepínat.

## <a name="next-steps"></a>Další kroky

Teď, když jste se dozvěděli o architektuře Azure IoT Central, je doporučeným dalším krokem informace o [připojení zařízení](concepts-get-connected.md) ve službě Azure IoT Central.