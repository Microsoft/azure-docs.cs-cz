---
title: Architektonické koncepty v Azure IoT Central | Dokumenty společnosti Microsoft
description: Tento článek představuje klíčové koncepty týkající se architektury Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 11/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 12ad231d81b6c134ebb8d4902b3f95c978e9622d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271639"
---
# <a name="azure-iot-central-architecture"></a>Architektura služby Azure IoT Central



Tento článek obsahuje přehled architektury Microsoft Azure IoT Central.

![Architektura nejvyšší úrovně](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Zařízení

Zařízení si vyměňují data s vaší aplikací Azure IoT Central. Zařízení může:

- Odešlete měření, jako je telemetrie.
- Synchronizujte nastavení s aplikací.

Ve službě Azure IoT Central jsou data, která si zařízení můžou vyměňovat s vaší aplikací, určena v šabloně zařízení. Další informace o šablonách zařízení naleznete v [tématu Správa metadat](#metadata-management).

Další informace o tom, jak se zařízení připojují k vaší aplikaci Azure IoT Central, najdete v [tématu Připojení k zařízení](concepts-get-connected.md).

## <a name="azure-iot-edge-devices"></a>Zařízení Azure IoT Edge

Kromě zařízení vytvořených pomocí sad [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks)můžete zařízení [Azure IoT Edge](../../iot-edge/about-iot-edge.md) připojit také k aplikaci IoT Central. IoT Edge umožňuje spouštět cloudové inteligence a vlastní logiku přímo na zařízeních IoT spravovaných IoT Central. Runtime IoT Edge umožňuje:

- Nainstalujte a aktualizujte úlohy v zařízení.
- Udržujte na zařízení standardy zabezpečení IoT Edge.
- Ujistěte se, že moduly IoT Edge jsou vždy spuštěné.
- Oznamte stav modulu do cloudu pro vzdálené monitorování.
- Spravujte komunikaci mezi zařízeními s navazujícími listy a zařízením IoT Edge, mezi moduly na zařízení IoT Edge a mezi zařízením IoT Edge a cloudem.

![Azure IoT Central s Azure IoT Edge](./media/concepts-architecture/iotedge.png)

IoT Central umožňuje následující funkce pro zařízení IoT Edge:

- Šablony zařízení popisující možnosti zařízení IoT Edge, například:
  - Možnost nahrávání manifestu nasazení, která vám pomůže spravovat manifest pro flotilu zařízení.
  - Moduly, které běží na zařízení IoT Edge.
  - Telemetrie, kterou každý modul odesílá.
  - Vlastnosti, které každý modul hlásí.
  - Příkazy, na které každý modul reaguje.
  - Vztahy mezi modelem schopností zařízení brány IoT Edge a modelem schopností zařízení pro příjem dat.
  - Vlastnosti cloudu, které nejsou uložené na zařízení IoT Edge.
  - Vlastní nastavení, řídicí panely a formuláře, které jsou součástí aplikace IoT Central.

  Další informace najdete v [článku připojení zařízení Azure IoT Edge k článku aplikace Azure IoT Central.](./concepts-iot-edge.md)

- Možnost zřizování zařízení IoT Edge ve velkém měřítku pomocí služby zřizování zařízení Azure IoT
- Pravidla a akce.
- Vlastní řídicí panely a analýzy.
- Nepřetržitý export dat telemetrie ze zařízení IoT Edge.

### <a name="iot-edge-device-types"></a>Typy zařízení IoT Edge

IoT Central klasifikuje typy zařízení IoT Edge takto:

- Leaf zařízení. Zařízení IoT Edge může mít zařízení s pořízenými listy, ale tato zařízení nejsou zřízená v IoT Central.
- Zařízení brány s podřazenými zařízeními. Zařízení brány i zařízení pro příjem dat jsou zřízená v centru IoT

![IoT Central s přehledem iot edge](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>Vzory okrajů IoT

IoT Central podporuje následující vzory zařízení IoT Edge:

#### <a name="iot-edge-as-leaf-device"></a>IoT Edge jako listové zařízení

![IoT Edge jako listové zařízení](./media/concepts-architecture/edgeasleafdevice.png)

Zařízení IoT Edge se zřizovalo v IoT Central a všechna zařízení pro příjem dat a jejich telemetrie jsou reprezentovány jako pocházející ze zařízení IoT Edge. Zařízení pro příjem připojení k zařízení IoT Edge se ve IoT Central nezřazují.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>Zařízení brány IoT Edge připojené k podřizovatelům s identitou

![IoT Edge s identitou zařízení pro příjem dat](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

Zařízení IoT Edge se zřizovalo v IoT Central spolu s navazujícími zařízeními připojenými k zařízení IoT Edge. Podpora runtime pro zřizování podřízených zařízení prostřednictvím brány není aktuálně podporována.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>Zařízení brány IoT Edge připojené k podřizovatelům s identitou poskytovanou bránou IoT Edge

![IoT Edge s následným zařízením bez identity](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

Zařízení IoT Edge se zřizovalo v IoT Central spolu s navazujícími zařízeními připojenými k zařízení IoT Edge. Runtime podpora brány poskytující identitu pro zařízení pro příjem dat a zřizování podřízených zařízení není v současné době podporována. Pokud si přinesete vlastní modul překladu identity, IoT Central může podporovat tento vzor.

## <a name="cloud-gateway"></a>Cloudová brána

Azure IoT Central používá Azure IoT Hub jako cloudovou bránu, která umožňuje připojení zařízení. IoT Hub umožňuje:

- Přijímat data ve velkém měřítku v cloudu.
- Správa zařízení.
- Zabezpečené připojení zařízení.

Další informace o službě IoT Hub najdete v [tématu Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

Další informace o připojení zařízení v Azure IoT Central najdete v [tématu Připojení zařízení](concepts-get-connected.md).

## <a name="data-stores"></a>Úložiště dat

Azure IoT Central ukládá data aplikací v cloudu. Uložená data aplikace zahrnují:

- Šablony zařízení.
- Identity zařízení.
- Metadata zařízení.
- Data uživatelů a rolí.

Azure IoT Central používá úložiště časových řad pro naměřená data odeslaná z vašich zařízení. Data časových řad ze zařízení používaných analytickou službou.

## <a name="analytics"></a>Analýza

Analytická služba je zodpovědná za generování vlastních dat sestav, která aplikace zobrazí. Operátor může [přizpůsobit analýzy](howto-create-analytics.md) zobrazené v aplikaci. Analytická služba je postavená na [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) a zpracovává naměřená data odeslaná z vašich zařízení.

## <a name="rules-and-actions"></a>Pravidla a akce

[Pravidla a akce](tutorial-create-telemetry-rules.md) úzce spolupracují na automatizaci úkolů v rámci aplikace. Tvůrce může definovat pravidla na základě telemetrie zařízení, jako je například teplota přesahující definovanou prahovou hodnotu. Azure IoT Central používá procesor datového proudu k určení, kdy jsou splněny podmínky pravidla. Pokud je splněna podmínka pravidla, spustí akci definovanou tvůrcem. Akce může například odeslat e-mail s upozorněním inženýra, že teplota v zařízení je příliš vysoká.

## <a name="metadata-management"></a>Správa metadat

V aplikaci Azure IoT Central šablony zařízení definují chování a možnosti typů zařízení. Například šablona zařízení chladničky určuje telemetrická data, kterou chladnička odešle do vaší aplikace.

![Architektura šablony](media/concepts-architecture/template-architecture.png)

V šabloně aplikačního zařízení IoT Central obsahuje:

- **Modely schopností zařízení** určují možnosti zařízení, jako je například telemetrická data, kterou odesílá, vlastnosti, které definují stav zařízení a příkazy, na které zařízení reaguje. Možnosti zařízení jsou uspořádány do jednoho nebo více rozhraní. Další informace o modelech funkcí zařízení najdete v dokumentaci [k technologii IoT Plug and Play (preview).](../../iot-pnp/overview-iot-plug-and-play.md)
- **Vlastnosti cloudu** určují vlastnosti, které pro zařízení ukládají úložiště IoT Central. Tyto vlastnosti jsou uloženy pouze v IoT Central a nikdy odeslány do zařízení.
- **Zobrazení** určují řídicí panely a formuláře, které vytvoří tvůrce, aby operátor mohl sledovat a spravovat zařízení.
- **Vlastní nastavení** umožňují tvůrce přepsat některé definice v modelu funkce zařízení, aby byly relevantnější pro aplikaci IoT Central.

Aplikace může mít jedno nebo více simulovaných a skutečných zařízení založených na každé šabloně zařízení.

## <a name="data-export"></a>Export dat

V aplikaci Azure IoT Central můžete [průběžně exportovat data](howto-export-data.md) do vlastních center událostí Azure a instancí Azure Service Bus. Data můžete také pravidelně exportovat do účtu úložiště objektů blob Azure. IoT Central může exportovat měření, zařízení a šablony zařízení.

## <a name="batch-device-updates"></a>Dávkové aktualizace zařízení

V aplikaci Azure IoT Central můžete [vytvářet a spouštět úlohy](howto-run-a-job.md) pro správu připojených zařízení. Tyto úlohy umožňují hromadné aktualizace vlastností nebo nastavení zařízení nebo spouštět příkazy. Můžete například vytvořit úlohu pro zvýšení rychlosti ventilátoru pro více chladicích prodejních automatů.

## <a name="role-based-access-control-rbac"></a>Řízení přístupu na základě role (RBAC)

Správce [můžete definovat pravidla přístupu](howto-manage-users-roles.md) pro aplikaci Azure IoT Central pomocí jedné z předdefinovaných rolí nebo vytvořením vlastní role. Role určují, ke kterým oblastem aplikace má uživatel přístup a jaké akce může provádět.

## <a name="security"></a>Zabezpečení

Mezi funkce zabezpečení v rámci Azure IoT Central patří:

- Data jsou šifrována při přenosu a v klidovém stavu.
- Ověřování poskytuje azure active directory nebo účet Microsoft. Je podporováno dvoufaktorové ověřování.
- Úplná izolace tenanta.
- Zabezpečení na úrovni zařízení.

## <a name="ui-shell"></a>Prostředí ui

Prostředí uživatelského rozhraní je moderní, responzivní aplikace založená na prohlížeči HTML5.
Správce může přizpůsobit rozhraní aplikace použitím vlastních motivů a úpravou odkazů nápovědy tak, aby ukazovaly na vlastní prostředky nápovědy. Další informace o přizpůsobení uživatelského rozhraní najdete v článku [Přizpůsobení centrálního uživatelského rozhraní Azure IoT.](howto-customize-ui.md)

Operátor může vytvářet přizpůsobené řídicí panely aplikací. Můžete mít několik řídicích panelů, které zobrazují různá data a přepínají mezi nimi.

## <a name="next-steps"></a>Další kroky

Teď, když jste se dozvěděli o architektuře Azure IoT Central, je dalším doporučeným krokem informace o [připojení zařízení](concepts-get-connected.md) v Azure IoT Central.
