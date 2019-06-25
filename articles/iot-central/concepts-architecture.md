---
title: Koncepce architektury v Azure IoT Central | Dokumentace Microsoftu
description: Tento článek představuje klíčové koncepty týkající se architektura Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 4bc9a79576c3165585a4a2c897bd41bfb77c080c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66693131"
---
# <a name="azure-iot-central-architecture"></a>Architektura služby Azure IoT Central

Tento článek obsahuje přehled architektury Microsoft Azure IoT Central.

![Architektura vysoké úrovně](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Zařízení

Zařízení exchange dat pomocí aplikace Azure IoT Central. Může se zařízení:

- Odeslání měření, jako je například telemetrická data.
- Synchronizujte nastavení s vaší aplikací.

V Azure IoT Central je v šabloně zařízení zadat data, která zařízení si mohou vyměňovat s vaší aplikací. Další informace o šablonách zařízení, najdete v části [Metadata správy](#metadata-management).

Další informace o jak zařízení připojit k aplikaci Azure IoT Central, naleznete v tématu [připojení zařízení](concepts-connectivity.md).

## <a name="cloud-gateway"></a>Cloudová brána

Azure IoT Central používá Azure IoT Hub jako cloudová brána, která umožňuje připojení zařízení. IoT Hub umožňuje pomocí:

- Příjem dat ve velkém měřítku v cloudu.
- Správa zařízení.
- Zabezpečené připojení zařízení.

Další informace o službě IoT Hub najdete v tématu [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

Další informace o připojení zařízení v Azure IoT Central, naleznete v tématu [připojení zařízení](concepts-connectivity.md).

## <a name="data-stores"></a>Úložiště dat

Azure IoT Central ukládá data aplikací v cloudu. Data aplikace uložená zahrnují:

- Šablony zařízení.
- Identit zařízení.
- Metadata zařízení.
- Data uživatelů a rolí.

Azure IoT Central používá čas ukládání řady pro měření dat odesílaných ze zařízení. Data časových řad ze zařízení, které používá služba analytics.

## <a name="analytics"></a>Analýzy

Analytická služba je zodpovědná za generování vlastních sestav data, která aplikace zobrazí. Operátor může [přizpůsobení analýzy](howto-create-analytics.md) zobrazovat v aplikaci. Analytická služba je postavený na [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) a zpracovává data měření odeslané ze zařízení.

## <a name="rules-and-actions"></a>Pravidla a akce

[Pravidla a akce](howto-create-telemetry-rules.md) práce pro automatizaci úloh v rámci aplikace úzce spolupracují. Tvůrce můžete definovat pravidla na základě telemetrie zařízení, jako je například teploty překročení definované mezní hodnoty. Azure IoT Central datového proudu procesoru používá k určení, kdy jsou splněny podmínky pravidla. Pokud je splněna podmínka pravidla, aktivuje akci určené Tvůrce. Akci můžete například odeslat e-mail s oznámením pracovník, teploty v zařízení je příliš vysoká.

## <a name="metadata-management"></a>Správa metadat

V aplikaci Azure IoT Central zařízení šablony definují chování a schopnost typů zařízení. Například šablona zařízení lednice určuje telemetrie, které lednice odesílá do vaší aplikace.

![Architektura šablon](media/concepts-architecture/template_architecture.png)

V šabloně zařízení:

- **Měření** zadejte telemetrie zařízení odesílá do aplikace.
- **Nastavení** zadat konfigurace, které můžete nastavit operátor.
- **Vlastnosti** zadat metadata, která můžete nastavit operátor.
- **Pravidla** automatizovat chování v aplikaci na základě dat poslaných ze zařízení.
- **Řídicí panely** jsou přizpůsobitelné zobrazení zařízení v aplikaci.

Aplikace může mít jeden nebo více skutečných a simulovaných zařízení, na základě šablony jednotlivých zařízení.

## <a name="data-export"></a>Export dat

V aplikaci Azure IoT Central je možné [průběžně exportovat data](howto-export-data-event-hubs-service-bus.md) do své služby Azure Event Hubs a instance služby Azure Service Bus. Data můžete také pravidelně exportovat do účtu úložiště objektů Blob v Azure. Měření, zařízení a šablon můžete exportovat IoT Central.

## <a name="batch-device-updates"></a>Dávkové aktualizace zařízení

V aplikaci Azure IoT Central je možné [vytvářet a spouštět úlohy](howto-run-a-job.md) ke správě připojených zařízení. Tyto úlohy umožňují hromadné aktualizace vlastností zařízení nebo nastavení, nebo spouštět příkazy. Můžete například vytvořit úlohu zrychlit ventilátor pro více chladicí automaty.

## <a name="role-based-access-control-rbac"></a>Řízení přístupu na základě role (RBAC)

[Správce můžete definovat pravidla přístupu](howto-administer.md) pro Azure IoT Central aplikaci s využitím předdefinovaných rolí. Správce můžete přiřadit role, které určují, jaké oblasti aplikace má uživatel přístup k uživatelům.

## <a name="security"></a>Zabezpečení

Funkce zabezpečení v rámci Azure IoT Central:

- Data se šifrují přenášená i neaktivní uložená.
- Ověřování se poskytuje tak, že Azure Active Directory nebo Account Microsoft. Dvojúrovňové ověřování je podporováno.
- Izolace úplné tenanta.
- Zabezpečení na úrovni zařízení.

## <a name="ui-shell"></a>Prostředí uživatelského rozhraní

Prostředí uživatelského rozhraní je moderních, interaktivních na HTML5 využívající prohlížeč aplikace.
Správce může přizpůsobit uživatelské rozhraní aplikace použití vlastní motivy a úpravou odkazy nápovědy tak, aby odkazoval na prostředky vlastní nápovědy. Další informace o přizpůsobení uživatelského rozhraní, naleznete v tématu [přizpůsobení Azure IoT Central uživatelského rozhraní](howto-customize-ui.md) článku.

Operátor můžete vytvořit řídicí panely personalizované aplikace. Můžete mít několik řídicích panelů, které zobrazují různá data a mezi nimi přepínat.

## <a name="next-steps"></a>Další postup

Teď, když jste se dozvěděli o architektuře Azure IoT Central, navrhované dalším krokem je další informace o [připojení zařízení](concepts-connectivity.md) v Azure IoT Central.