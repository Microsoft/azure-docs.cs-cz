---
title: IoT technologie Plug and Play aktuální verze | Microsoft Docs
description: Podívejte se, co je součástí aktuální verze technologie Plug and Play IoT.
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4bd1bb93c9ce0f491c5bf1153917491b88d55109
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043710"
---
# <a name="whats-in-the-current-iot-plug-and-play-release"></a>Co je v aktuální verzi technologie Plug and Play IoT

Tento článek shrnuje nástroje, sady SDK a rozhraní API, které podporují aktuální verzi technologie Plug and Play IoT. Uvedená čísla verzí představují číslo verze v době, kdy technologie Plug and Play IoT všeobecně dostupná. Čísla verzí se můžou po vydání zvýšit.

## <a name="modeling-language"></a>Modelovací jazyk

[Digital DTDLs Definition Language () v2](https://github.com/Azure/opendigitaltwins-dtdl).

Další informace o tom, jak zařízení IoT technologie Plug and Play pracují s DTDL, najdete v tématu věnovaném [konvencím technologie Plug and Play IoT](concepts-convention.md).

## <a name="tools-and-utilities"></a>Nástroje

- Azure IoT Explorer 0.12.0.

    Další informace najdete v tématu [instalace a použití Azure IoT Exploreru](howto-use-iot-explorer.md).

- 1.0.0 rozšíření VS Code.

    Další informace najdete v tématu [instalace a použití nástrojů pro vytváření DTDL](howto-use-dtdl-authoring-tools.md).

- Rozšíření sady Visual Studio 2019 1.0.0.

    Další informace najdete v tématu [instalace a použití nástrojů pro vytváření DTDL](howto-use-dtdl-authoring-tools.md).

- Rozšíření Azure CLI IoT 0.10.0.

    Rozšíření Azure IoT obsahuje příkazy, které vám pomůžou zařízení certifikovat. Viz třída `az iot product -h`.

## <a name="libraries-and-sdks"></a>Knihovny a sady SDK

Další informace o knihovnách a sadách SDK najdete v tématu [Microsoft SDK for IoT technologie Plug and Play](libraries-sdks.md).

- Sada SDK pro zařízení C [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md)
- [GitHub](https://github.com/Azure/azure-sdk-for-c/) SDK pro zařízení v sadě C Embedded
- Sada [1.31.0 NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) sady SDK pro zařízení .NET
- Sada SDK pro zařízení Java [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client)
- [2.3.0 PIP](https://pypi.org/project/azure-iot-device/) sady SDK pro zařízení Python
- [Npm 1.17.2](https://www.npmjs.com/package/azure-iot-device) sady SDK pro zařízení Node.js
- .NET – IoT Hub služby [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices )
- Java – IoT Hub [Maven Service 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0)
- Služba Node.js-IoT Hub [npm 1.13.0](https://www.npmjs.com/package/azure-iothub)
- Python-IoT Hub/Digital s vyzdvojenými službami [PIP – 2.2.3](https://pypi.org/project/azure-iot-hub)
- DTDL modelu [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser)pro analyzátor modelů.

## <a name="rest-apis"></a>Rozhraní REST API

REST API [2020-09-30](/rest/api/iothub).

Další informace najdete v tématu [Příručka pro vývojáře IoT technologie Plug and Play](concepts-developer-guide-service.md).

## <a name="iot-hub"></a>IoT Hub

Technologie Plug and Play IoT podporuje IoT Hub ve všech oblastech. IoT technologie Plug and Play podporuje jenom standardní centra IoT úrovně Standard nebo Free.

## <a name="announcements"></a>Oznámení

Aktuální a předchozí oznámení o technologie Plug and Play IoT najdete v následujících blogových příspěvcích:

- [Aktualizace veřejné verze Preview (zveřejněné dne 29. srpna 2020)](https://techcommunity.microsoft.com/t5/internet-of-things/add-quot-plug-and-play-quot-to-your-iot-solutions/ba-p/1548531)
- [Příprava a certifikace zařízení pro IoT technologie Plug and Play (zveřejněné 26. srpna 2020)](https://azure.microsoft.com/blog/prepare-and-certify-your-devices-for-iot-plug-and-play/)
- [Technologie Plug and Play IoT je teď k dispozici ve verzi Preview (zveřejněné dne 22. srpna 2019).](https://azure.microsoft.com/blog/iot-plug-and-play-is-now-available-in-preview/)
- [Sestavování s využitím Azure IoT Central a IoT technologie Plug and Play (zveřejněné 7. května 2019)](https://azure.microsoft.com/blog/build-with-azure-iot-central-and-iot-plug-and-play/)