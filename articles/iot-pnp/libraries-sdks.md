---
title: technologie Plug and Play knihovny a sady SDK pro IoT
description: Informace o knihovnách zařízení a služeb, které jsou k dispozici pro vývoj řešení s podporou IoT technologie Plug and Play.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 5638cd9973c6a4df809e0b200efe85b067aae026
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407791"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Microsoft SDK pro IoT technologie Plug and Play

Knihovny technologie Plug and Play IoT a sady SDK umožňují vývojářům vytvářet řešení IoT s využitím nejrůznějších programovacích jazyků na různých platformách. Následující tabulka obsahuje odkazy na ukázky a rychlé starty, které vám pomůžou začít:

## <a name="device-sdks-ga"></a>Sady SDK pro zařízení (GA)

| Jazyk | Balíček | Úložiště kódu | ukázky | Rychlý start | Referenční informace |
|---|---|---|---|---|---|
| C – zařízení | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/releases/tag/2020-07-19) | [ukázky](https://github.com/Azure/azure-iot-sdk-c/tree/2020-07-19/iothub_client/samples/pnp) | [Připojení ke službě IoT Hub](quickstart-connect-device-c.md) | [Odkaz](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET – zařízení | [1.27.0 NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/1.27.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [ukázky](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples/PnpDeviceSamples) | [Připojení ke službě IoT Hub](quickstart-connect-device-csharp.md) | [Odkaz](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet) |
| Java – zařízení | [Maven 1.24.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client/1.24.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [ukázky](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Připojení ke službě IoT Hub](quickstart-connect-device-java.md) | [Odkaz](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable) |
| Python – zařízení | [PIP 2.1.4](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [ukázky](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Připojení ke službě IoT Hub](quickstart-connect-device-python.md) | [Odkaz](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python) |
| Uzel – zařízení | [NPM 1.17.0](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [ukázky](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Připojení ke službě IoT Hub](quickstart-connect-device-node.md) | [Odkaz](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest) |

## <a name="device-sdks-preview"></a>Sady SDK pro zařízení (Preview)

| Jazyk | Úložiště kódu/ukázky |
|---|---|
|Sada Azure SDK pro Embedded| [GitHub](https://github.com/Azure/azure-sdk-for-c/#) |
|Middleware Azure RTO IoT| [GitHub](https://github.com/azure-rtos/azure-iot-preview#) |
|Úvodní příručky ke službě Azure RTO | [GitHub](https://github.com/azure-rtos/getting-started) |

## <a name="service-sdks-preview"></a>Sady SDK pro služby (Preview)

| Jazyk | Balíček | Úložiště kódu | ukázky | Rychlý start | Referenční informace |
|---|---|---|---|---|---|
| .NET – verze Preview služby IoT Hub | [NuGet 1.27.1-Preview-002](https://www.nuget.org/packages/Microsoft.Azure.Devices/1.27.1-preview-002 ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh) | [ukázky](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh/iothub/service/samples/PnpServiceSamples) | – | – |
| Verze Preview služby IoT Hub Java | [Maven 1.1.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client-preview/1.1.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh) | [ukázky](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh/service/iot-service-samples/pnp-service-sample) | – | – |
| Služba Node-IoT Hub – verze Preview | [NPM 1.12.4-PNP-Refresh. 4](https://www.npmjs.com/package/azure-iothub/v/1.12.4-pnp-refresh.4) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [ukázky](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/service/samples) | – | – |
| Verze Preview služby IoT Hub/Digital s příběhu v Pythonu | [PIP 2.2.1 RC1](https://pypi.org/project/azure-iot-hub/2.2.1rc1/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh) | [ukázky](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh/azure-iot-hub/samples) | [Interakce s rozhraním API IoT Hub digitálního vlákna](quickstart-service-python.md) | – |
| Služba je ve verzi Preview pro službu digitálního vyzdvojení uzlů | [NPM 1.0.0-PNP-Refresh. 3](https://www.npmjs.com/package/azure-iot-digitaltwins-service/v/1.0.0-pnp-refresh.3) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [ukázky](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/digitaltwins/samples/service/javascript) | [Interakce s rozhraním API IoT Hub digitálního vlákna](quickstart-service-node.md) | – |

## <a name="next-steps"></a>Další kroky

Chcete-li vyzkoušet sady SDK a knihovny, přečtěte si [příručku pro vývojáře](concepts-developer-guide.md) a [rychlé starty zařízení](quickstart-connect-device-c.md) a [služby](quickstart-service-node.md).
