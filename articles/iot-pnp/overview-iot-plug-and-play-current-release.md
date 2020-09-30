---
title: IoT technologie Plug and Play aktuální verze | Microsoft Docs
description: Podívejte se, co je součástí aktuální verze technologie Plug and Play IoT.
author: dominicbetts
ms.author: dobett
ms.date: 09/19/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4cfc9ca14a4b344423a27d9683114de5275fb20b
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91583551"
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

    Další informace najdete v tématu [instalace a použití rozšíření Azure IoT pro rozhraní příkazového řádku Azure CLI](howto-use-iot-pnp-cli.md).

    > [!TIP]
    > Rozšíření Azure IoT obsahuje příkazy, které vám pomůžou zařízení certifikovat. Viz třída `az iot product -h`.



## <a name="libraries-and-sdks"></a>Knihovny a sady SDK

Další informace o knihovnách a sadách SDK najdete v tématu [Microsoft SDK for IoT technologie Plug and Play](libraries-sdks.md).

- Sada SDK pro zařízení C [VcPkg 2020-07-19](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c)
- Sada SDK pro zařízení .NET [NuGet Microsoft. Azure. Devices. Client 1.30.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
- Sada Java SDK [pro zařízení Maven IoT-Device-1.25.0 klienta](https://search.maven.org/artifact/com.microsoft.azure.sdk.iot/iot-device-client)
- Python SDK pro zařízení – [PIP Azure – IoT-Device v 2.2.0](https://pypi.org/project/azure-iot-device/)
- Sada SDK pro Node.js zařízení [npm Azure-IoT-Device 1.17.1](https://www.npmjs.com/package/azure-iot-device)
- .NET – IoT Hub služby [NuGet Microsoft. Azure. Devices 1.22.0](https://www.nuget.org/packages/Microsoft.Azure.Devices/)
- Java-IoT Hub Service [Maven IoT-Service-1.25.0 klienta](https://search.maven.org/artifact/com.microsoft.azure.sdk.iot/iot-service-client)
- Služba Node.js-IoT Hub [npm Azure-iothub 1.12.5](https://www.npmjs.com/package/azure-iothub)
- Python-IoT Hub/digitální vyzdvojení služby [PIP Azure-IoT-Hub 2.2.2](https://pypi.org/project/azure-iot-hub/)
- DTDL modelu [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser)pro analyzátor modelů.

### <a name="preview"></a>Preview

- Sada Azure SDK pro Embedded [1.0.0 – Preview. 5](https://github.com/Azure/azure-sdk-for-c/releases/tag/1.0.0-preview.5)
- Middleware Azure RTO IoT [v6.0_beta3](https://github.com/azure-rtos/azure-iot-preview/releases/tag/v6.0_beta3)

## <a name="rest-apis"></a>Rozhraní REST API

REST API [2020-09-30](https://docs.microsoft.com/rest/api/iothub).

Další informace najdete v tématu [Příručka pro vývojáře IoT technologie Plug and Play](concepts-developer-guide-service.md).

## <a name="iot-hub"></a>IoT Hub

Technologie Plug and Play IoT podporuje IoT Hub ve všech oblastech. IoT technologie Plug and Play podporuje jenom standardní centra IoT úrovně Standard nebo Free.

## <a name="announcements"></a>Oznámení

Aktuální a předchozí oznámení o technologie Plug and Play IoT najdete v následujících blogových příspěvcích:

- [Aktualizace veřejné verze Preview (zveřejněné dne 29. srpna 2020)](https://techcommunity.microsoft.com/t5/internet-of-things/add-quot-plug-and-play-quot-to-your-iot-solutions/ba-p/1548531)
- [Příprava a certifikace zařízení pro IoT technologie Plug and Play (zveřejněné 26. srpna 2020)](https://azure.microsoft.com/blog/prepare-and-certify-your-devices-for-iot-plug-and-play/)
- [Technologie Plug and Play IoT je teď k dispozici ve verzi Preview (zveřejněné dne 22. srpna 2019).](https://azure.microsoft.com/blog/iot-plug-and-play-is-now-available-in-preview/)
- [Sestavování s využitím Azure IoT Central a IoT technologie Plug and Play (zveřejněné 7. května 2019)](https://azure.microsoft.com/blog/build-with-azure-iot-central-and-iot-plug-and-play/)

