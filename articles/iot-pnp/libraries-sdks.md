---
title: Knihovny a sady SDK IoT Plug and Play
description: Informace o knihovnách zařízení a služeb, které jsou k dispozici pro vývoj řešení s podporou technologie IoT Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 394edc24cfeefb96cb2f412985cb990a2306bb09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064331"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>Knihovny a sady SDK IoT Plug and Play

Knihovny a sady SDK IoT Plug and Play umožňují vývojářům vytvářet řešení IoT pomocí různých programovacích jazyků na různých platformách. Následující tabulka obsahuje odkazy na ukázky a rychlé starty, které vám pomohou začít:

## <a name="microsoft-supported-libraries-and-sdks"></a>Knihovny a sady SDK podporované společností Microsoft

| Platforma | Knihovna/balíček | Zdrojový kód | Ukázka | Rychlý start | Odkaz |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [Sada Device SDK na apt-get](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Ukázky klientů Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Připojení ke službě IoT Hub](./quickstart-connect-pnp-device-c-linux.md) | [Odkaz](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Windows  | [Sada SDK zařízení na vcpkg](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Ukázky klientů Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Připojení ke službě IoT Hub](./quickstart-connect-pnp-device-c-windows.md) | [Odkaz](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/mbed  | [Sada SDK zařízení na embedu](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Ukázky klientů Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Odkaz](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Arduino  | [Sada SDK zařízení v Arduino IDE](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Ukázky klientů Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Odkaz](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [Sada SDK zařízení na cocoapodu](https://cocoapods.org/pods/AzureIoTHubClient) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Ukázky klientů Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Odkaz](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Digitální dvojče vzorky](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [Připojení ke službě IoT Hub](./quickstart-connect-pnp-device-csharp.md) | [Odkaz](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Digitální dvojče vzorky](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [Připojení ke službě IoT Hub](./quickstart-connect-pnp-device-java.md) | [Odkaz](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [NPM](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [Digitální dvojče vzorky](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [Připojení ke službě IoT Hub](./quickstart-connect-pnp-device-node.md) | [Odkaz](https://docs.microsoft.com/javascript/api/azure-iot-device/) |

## <a name="iot-hub-support"></a>Podpora pro IoT Hub

Možnosti zařízení IoT Plug and Play podporují jenom [bezplatné a standardní vrstvy IoT hubů](../iot-hub/iot-hub-scaling.md).

## <a name="next-steps"></a>Další kroky

Kromě sad SDK zařízení a knihoven můžete k interakci s úložišti modelu použít rozhraní REST API.