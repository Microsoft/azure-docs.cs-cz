---
title: Koncepty architektury v Azure IoT centrální | Microsoft Docs
description: Tento článek představuje klíčové koncepty týkající se architektuře Azure IoT centrální
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 44408e7b6ad1a068f265bf7b78d973e6aae3001b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628755"
---
# <a name="azure-iot-central-architecture"></a>Architektura služby Azure IoT – střed

Tento článek obsahuje přehled Microsoft Azure IoT centrální architektury.

![Architektura vysoké úrovně](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Zařízení

Zařízení vyměňovat data s Azure IoT centrální aplikace. Zařízení můžete:

- Odešlete měření například telemetrie.
- Synchronizujte nastavení s vaší aplikací.

V Azure IoT centrální data, která zařízení můžete exchange s vaší aplikací je zadán v šabloně zařízení. Další informace o šablonách zařízení najdete v tématu [Metadata správy](#metadata-management).

Další informace o připojení zařízení k Azure IoT centrální aplikaci najdete v tématu [připojení zařízení](concepts-connectivity.md).

## <a name="cloud-gateway"></a>Cloudová brána

Střed Azure IoT používá Azure IoT Hub jako cloudová brána, která umožňuje připojení zařízení. Umožňuje, aby IoT Hub:

- Přijímání dat ve velkém měřítku v cloudu.
- Správa zařízení.
- Zabezpečené připojení zařízení.

Další informace o IoT Hub, najdete v části [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

Další informace o připojení zařízení v Azure IoT centrální najdete v tématu [připojení zařízení](concepts-connectivity.md).

## <a name="data-stores"></a>Úložiště dat

Azure IoT střed ukládá data aplikací v cloudu. Uložená data aplikací obsahuje:

- Šablony zařízení.
- Identity zařízení.
- Metadata zařízení.
- Data uživatele a role.

Střed Azure IoT používá čas ukládání řady pro měření dat odesílaných ze zařízení. Čas řady data ze zařízení, které používá služba analytics.

## <a name="analytics"></a>Analýzy

Analytická služba je zodpovědná za generování vlastních sestav data, která aplikace zobrazí. Operátor může [přizpůsobit analytics](howto-create-analytics.md) zobrazí v aplikaci. Analytická služba je postavený na [Statistika řady čas Azure](https://azure.microsoft.com/services/time-series-insights/) a zpracuje měření dat odesílaných ze zařízení.

## <a name="rules-and-actions"></a>Pravidla a akce

[Pravidla a akce](howto-create-telemetry-rules.md) pracovní úzce spolupracují k automatizaci úloh v aplikaci. Tvůrce můžete definovat pravidla založená na telemetrie zařízení, jako je například teploty překročení definovanou prahovou hodnotu. Střed Azure IoT používá procesor datového proudu k určení, pokud jsou splněny podmínky pravidla. Když je splněna podmínka pravidla, aktivuje akce definované Tvůrce. Například akce Odeslat e-mail s oznámením technika, že teplota v zařízení je příliš vysoká.

## <a name="metadata-management"></a>Správa metadat

V aplikaci Azure IoT centrální zařízení šablony definují chování a schopností typů zařízení. Například šablonu ledničce zařízení určuje telemetrie, které chladničce odesílá do vaší aplikace.

![Architektura šablon](media/concepts-architecture/template_architecture.png)

V šabloně zařízení:

- **Měření** zadejte telemetrie zařízení odesílá do aplikace.
- **Nastavení** zadat konfigurace, které můžete nastavit operátor.
- **Vlastnosti** zadejte metadata, která můžete nastavit operátor.
- **Pravidla** automatizovat chování v aplikaci na základě dat odeslaných ze zařízení.
- **Řídicí panely** jsou přizpůsobitelné zobrazení zařízení v aplikaci.

Aplikace může mít jeden nebo více simulované a skutečné zařízení na základě šablony jednotlivých zařízení.

## <a name="rbac"></a>RBAC

[Správce můžete definovat pravidla přístupu k](howto-administer.md) pro aplikaci Azure IoT centrální pomocí předdefinované role. Správce můžete přiřadit role, které určují, které oblasti aplikace má uživatel přístup k uživatelům.

## <a name="security"></a>Zabezpečení

Funkce zabezpečení v rámci Azure IoT centrální:

- Data se šifrují během přenosu i v klidu.
- Ověřování se poskytuje tak, že Azure Active Directory nebo Account Microsoft. Dvoufaktorové ověřování je podporováno.
- Izolace úplné klienta.
- Zabezpečení na úrovni zařízení.

## <a name="ui-shell"></a>Prostředí uživatelského rozhraní

Prostředí uživatelského rozhraní je moderní a pohotově reagujících, HTML5 založené na prohlížeči aplikace.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili o architektuře Azure IoT centrální, navrhované dalším krokem je další informace o [připojení zařízení](concepts-connectivity.md) v centrální Azure IoT.