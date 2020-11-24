---
title: Příručka pro vývojáře zařízení (C) – IoT technologie Plug and Play | Microsoft Docs
description: Popis technologie Plug and Play IoT pro vývojáře zařízení v jazyce C
author: rido-min
ms.author: rmpablos
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 8d8da39f038f465030a2dced092ab1b008e30e5e
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511417"
---
# <a name="iot-plug-and-play-device-developer-guide"></a>Příručka pro vývojáře pro IoT technologie Plug and Play zařízení

IoT technologie Plug and Play umožňuje vytvářet inteligentní zařízení, která budou inzerovat jejich schopnosti aplikacím Azure IoT. Zařízení IoT technologie Plug and Play nevyžadují ruční konfiguraci, když ji zákazník připojí k aplikacím s podporou IoT technologie Plug and Play.

Inteligentní zařízení je možné implementovat přímo, používat [moduly](../iot-hub/iot-hub-devguide-module-twins.md)nebo používat [IoT Edge moduly](../iot-edge/about-iot-edge.md).

Tato příručka popisuje základní kroky potřebné k vytvoření zařízení, modulu nebo modulu IoT Edge, který následuje po [konvencích technologie Plug and Play IoT](../iot-pnp/concepts-convention.md).

Pokud chcete vytvořit technologie Plug and Play zařízení, modul nebo modul IoT Edge IoT, postupujte podle těchto kroků:

1. Ujistěte se, že zařízení používá protokol WebSockets MQTT nebo MQTT pro připojení k Azure IoT Hub.
1. Vytvořte model [DTDL (Digital s definicemi jazyků)](https://github.com/Azure/opendigitaltwins-dtdl) pro popis zařízení. Další informace najdete v tématu [pochopení komponent v modelech IoT technologie Plug and Play](concepts-components.md).
1. Aktualizujte zařízení nebo modul, aby informoval `model-id` jako součást připojení zařízení.
1. Implementace telemetrie, vlastností a příkazů pomocí [konvencí technologie Plug and Play IoT](concepts-convention.md)

Po dokončení implementace zařízení nebo modulu použijte [Azure IoT Explorer](howto-use-iot-explorer.md) a ověřte, jestli zařízení dodržuje konvence technologie Plug and Play IoT.

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-device-devguide-java](../../includes/iot-pnp-device-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-device-devguide-node](../../includes/iot-pnp-device-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-device-devguide-python](../../includes/iot-pnp-device-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili o službě IoT technologie Plug and Play pro vývoj zařízení, tady je několik dalších prostředků:

- [Jazyk DTDL (Digital autodefinition Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Sada SDK pro zařízení jazyka C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [REST API IoT](https://docs.microsoft.com/rest/api/iothub/device)
- [Komponenty modelu](concepts-components.md)
- [Instalace a použití nástrojů pro tvorbu DTDL](howto-use-dtdl-authoring-tools.md)
- [Příručka pro vývojáře služby IoT technologie Plug and Play](concepts-developer-guide-service.md)
