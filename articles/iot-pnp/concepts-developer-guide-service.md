---
title: Příručka pro vývojáře služby – IoT technologie Plug and Play | Microsoft Docs
description: Popis technologie Plug and Play IoT pro vývojáře služeb
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: a5889be88dfd0870a2eee868c97787ff354cff68
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582727"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>Příručka pro vývojáře služby IoT technologie Plug and Play

IoT technologie Plug and Play umožňuje vytvářet inteligentní zařízení, která budou inzerovat jejich schopnosti aplikacím Azure IoT. Zařízení IoT technologie Plug and Play nevyžadují ruční konfiguraci, když ji zákazník připojí k aplikacím s podporou IoT technologie Plug and Play.

IoT technologie Plug and Play umožňuje používat zařízení, která oznámila ID modelu ve službě IoT Hub. Můžete například získat přímý přístup k vlastnostem a příkazům zařízení.

Pokud chcete použít zařízení IoT technologie Plug and Play připojené ke službě IoT Hub, jedna ze sad SDK služby IoT:

## <a name="service-sdks"></a>Sady SDK pro služby

Pomocí sad SDK služby Azure IoT ve vašem řešení můžete pracovat se zařízeními a moduly. Můžete například použít sady SDK služby ke čtení a aktualizaci dvojitých vlastností a vyvolání příkazů. Mezi podporované jazyky patří C#, Java, Node.js a Python.

Sady SDK služby umožňují přístup k informacím o zařízení z řešení, jako je například Desktop nebo webová aplikace. Sady SDK pro služby zahrnují dva obory názvů a objektové modely, které můžete použít k načtení ID modelu:

- Klient služby IoT Hub. Tato služba zpřístupňuje ID modelu jako vlastnost vlákna zařízení.

- Klient digitálního vlákna. Nové digitální vlákna rozhraní API funguje na konstrukcích modelu [DTDL (Digital vlákna Definition Language)](concepts-digital-twin.md) , jako jsou komponenty, vlastnosti a příkazy. Digitální vlákna rozhraní API usnadňují tvůrcům řešení vytváření řešení IoT technologie Plug and Play.

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-service-devguide-csharp](../../includes/iot-pnp-service-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-service-devguide-java](../../includes/iot-pnp-service-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-service-devguide-node](../../includes/iot-pnp-service-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-service-devguide-python](../../includes/iot-pnp-service-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>Další kroky

Teď, když jste se dozvěděli o modelování zařízení, tady je několik dalších prostředků:

- [Jazyk DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Sada SDK pro zařízení jazyka C](/azure/iot-hub/iot-c-sdk-ref/)
- [REST API IoT](/rest/api/iothub/device)
- [Průvodce modelováním IoT technologie Plug and Play](concepts-modeling-guide.md)
- [Instalace a použití nástrojů pro tvorbu DTDL](howto-use-dtdl-authoring-tools.md)