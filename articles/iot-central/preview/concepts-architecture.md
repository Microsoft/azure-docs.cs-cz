---
title: Koncepty architektury v Azure IoT Central | Microsoft Docs
description: Tento článek představuje klíčové koncepty týkající se architektury Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 10/15/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: cb2ca8fe227abd107daa60a0f7d31ba5dc4e7c1b
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73895329"
---
# <a name="azure-iot-central-architecture-preview-features"></a>Architektura Azure IoT Central (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Tento článek poskytuje přehled architektury Microsoft Azure IoT Central.

![Architektura nejvyšší úrovně](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Zařízení

Zařízení vyměňují data v aplikaci Azure IoT Central. Zařízení může:

- Odesílejte měření, jako je telemetrie.
- Synchronizujte nastavení s vaší aplikací.

V Azure IoT Central se data, která zařízení můžou vyměňovat s vaší aplikací, zadává v šabloně zařízení. Další informace o šablonách zařízení najdete v tématu [Správa metadat](#metadata-management).

Další informace o tom, jak se zařízení připojují k aplikaci Azure IoT Central, najdete v tématu [připojení zařízení](overview-iot-central-get-connected.md).

## <a name="cloud-gateway"></a>Cloudová brána

Azure IoT Central používá Azure IoT Hub jako cloudovou bránu, která umožňuje připojení zařízení. IoT Hub povoluje:

- Přijímání dat v cloudu se škálováním.
- Správa zařízení.
- Zabezpečte připojení zařízení.

Další informace o IoT Hub najdete v tématu [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

Další informace o připojení zařízení v Azure IoT Central najdete v tématu [připojení zařízení](overview-iot-central-get-connected.md).

## <a name="data-stores"></a>Úložiště dat

Azure IoT Central ukládá data aplikací v cloudu. Uložená data aplikací zahrnují:

- Šablony zařízení.
- Identity zařízení.
- Metadata zařízení.
- Data uživatelů a rolí.

Azure IoT Central využívá úložiště časových řad k měření dat odesílaných z vašich zařízení. Data časové řady ze zařízení, která služba Analytics používá.

## <a name="analytics"></a>Analýza

Služba analýzy zodpovídá za generování vlastních dat sestav, která aplikace zobrazuje. Operátor může [přizpůsobit analýzy](howto-create-analytics.md) zobrazené v aplikaci. Analytická služba je postavená na [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) a zpracovává data měření odesílaná z vašich zařízení.

## <a name="rules-and-actions"></a>Pravidla a akce

[Pravidla a akce](tutorial-create-telemetry-rules.md) společně spolupracují na automatizaci úloh v rámci aplikace. Tvůrce může definovat pravidla na základě telemetrie zařízení, například teploty překračující stanovenou prahovou hodnotu. Azure IoT Central využívá procesor datových proudů k určení, kdy jsou podmínky pravidla splněné. Když je splněna podmínka pravidla, aktivuje akci definovanou tvůrcem. Akce může například odeslat e-mail s informacemi o tom, že teplota v zařízení je příliš vysoká.

## <a name="metadata-management"></a>Správa metadat

V aplikaci IoT Central Azure definují šablony zařízení chování a možnosti typů zařízení. Například šablona zařízení chladničky určuje telemetrii, kterou do vaší aplikace posílá chladnička.

![Architektura šablon](media/concepts-architecture/template-architecture.png)

V šabloně aplikace IoT Central ve verzi Preview:

- **Modely schopností zařízení** určují možnosti zařízení, jako je například telemetrie, kterou posílá, vlastnosti, které definují stav zařízení, a příkazy, na které zařízení reaguje. Možnosti zařízení jsou uspořádány do jednoho nebo více rozhraní. Další informace o modelech schopností zařízení najdete v dokumentaci ke službě [IoT technologie Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) .
- **Vlastnosti cloudu** určují vlastnosti IoT Central úložišť pro zařízení. Tyto vlastnosti jsou uloženy pouze v IoT Central a nejsou nikdy odesílány do zařízení.
- **Zobrazení** určují řídicí panely a formuláře, které tvůrce vytvoří, aby mohl obsluhu monitorovat a spravovat zařízení.
- **Přizpůsobení** umožní tvůrci přepsat některé definice v modelu schopností zařízení, aby byly lépe relevantní pro IoT Central aplikace.

Aplikace může mít jedno nebo několik simulovaných a reálných zařízení založených na každé šabloně zařízení.

## <a name="data-export"></a>Export dat

V aplikaci IoT Central Azure můžete [data průběžně exportovat](howto-export-data.md) do vlastních instancí Azure Event Hubs a Azure Service Bus. Data můžete také pravidelně exportovat do svého účtu služby Azure Blob Storage. IoT Central může exportovat měření, zařízení a šablony zařízení.

## <a name="batch-device-updates"></a>Aktualizace zařízení Batch

V aplikaci IoT Central Azure můžete [vytvářet a spouštět úlohy](../core/howto-run-a-job.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json) pro správu připojených zařízení. Tyto úlohy umožňují hromadné aktualizace vlastností a nastavení zařízení nebo spouštění příkazů. Můžete například vytvořit úlohu pro zvýšení rychlosti ventilátoru pro více chladírenských prodejních počítačů.

## <a name="role-based-access-control-rbac"></a>Řízení přístupu na základě role (RBAC)

[Správce může definovat pravidla přístupu](howto-administer.md) pro aplikaci Azure IoT Central s použitím předdefinovaných rolí. Správce může přiřadit uživatele k rolím, které určují oblasti aplikace, ke které má uživatel přístup.

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

Teď, když jste se dozvěděli o architektuře Azure IoT Central, je doporučeným dalším krokem informace o [připojení zařízení](overview-iot-central-get-connected.md) ve službě Azure IoT Central.