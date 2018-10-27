---
title: Koncepce architektury v Azure IoT Central | Dokumentace Microsoftu
description: Tento článek představuje klíčové koncepty týkající se architektura Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 564ea3efe35a1054b8c905cff4b7f4c739cc9216
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50156370"
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

## <a name="next-steps"></a>Další postup

Teď, když jste se dozvěděli o architektuře Azure IoT Central, navrhované dalším krokem je další informace o [připojení zařízení](concepts-connectivity.md) v Azure IoT Central.