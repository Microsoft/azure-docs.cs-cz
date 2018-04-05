---
title: Pochopení SDK služby Azure IoT | Microsoft Docs
description: Příručka vývojáře – informace a odkazy na různých Azure IoT zařízení a služby sadách SDK, které můžete použít k vytváření aplikací pro zařízení a aplikací back-end.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aec2126369f45a89050dbd8b2d3cae7e00ccb8ed
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="understand-and-use-azure-iot-sdks"></a>Rady pro pochopení a použití sady SDK služby Azure IoT

Existují tři kategorie software development Kit (SDK) pro práci se službou IoT Hub:

* **Sady SDK zařízení** umožňují vytvářet aplikace, které běží na zařízení IoT. Tyto aplikace odesílat telemetrická data do služby IoT hub a volitelně přijímat zprávy pomocí služby IoT hub.

* **Služba SDK** umožňují spravovat služby IoT hub a volitelně odesílání zpráv do vašeho zařízení IoT.

* **Azure IoT Edge** vám umožní sestavovat bran pro zařízení, která nepoužívají jedním z podporovaných protokolů. Brány může také zpracovat zprávy na okraj.

Sady SDK jsou k dispozici pro podporu více programovacích jazyků.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Azure SDK zařízení IoT

Microsoft Azure IoT zařízení sady SDK obsahovat kód, který usnadňuje vytváření zařízení a aplikací, které se připojují k a spravuje služby Azure IoT Hub.

Toto zařízení Azure IoT sady SDK jsou k dispozici ke stažení z webu GitHub:

* [Pro zařízení Azure IoT SDK pro .NET][lnk-dotnet-device-sdk]
* [Pro zařízení Azure IoT SDK pro jazyk Java][lnk-java-device-sdk]
* [Pro zařízení Azure IoT SDK pro Node.js][lnk-node-device-sdk]
* [Pro zařízení Azure IoT SDK pro jazyk Python][lnk-python-device-sdk]
* [Pro zařízení Azure IoT SDK pro jazyk C] [ lnk-c-device-sdk] napsané v jazyce ANSI C (C99) přenositelnost široký kompatibilitu a platformy. Existují dva klientské knihovny zařízení pro jazyk C nízké úrovně **iothub_client** a **serializátor**.

> [!NOTE]
> Prohlédněte si soubory readme v úložišť GitHub pro informace o použití jazyka a specifické pro platformu balíček správci nainstalovat binární soubory a závislosti na vývojovém počítači.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Kompatibilita operačního systému platformy a hardwaru

Další informace o kompatibilitě sady SDK s konkrétním hardwarovým zařízení najdete v tématu [Azure certifikované pro katalog zařízení IoT][lnk-certified].

## <a name="azure-iot-service-sdks"></a>Azure IoT service SDKs

Sady SDK služby Azure IoT obsahovat kód, který usnadňuje vytváření aplikace, které spolupracují přímo službou IoT Hub pro správu zařízení a zabezpečení.

Následující služby Azure IoT sady SDK jsou k dispozici ke stažení z webu GitHub:

* [IoT služba Azure SDK pro .NET][lnk-dotnet-service-sdk]
* [IoT služba Azure SDK pro jazyk Java][lnk-java-service-sdk]
* [IoT služba Azure SDK pro Node.js][lnk-node-service-sdk]
* [IoT služba Azure SDK pro jazyk Python][lnk-python-service-sdk]
* [IoT služba Azure SDK pro jazyk C][lnk-c-service-sdk]

> [!NOTE]
> Prohlédněte si soubory readme v úložišť GitHub pro informace o použití jazyka a specifické pro platformu balíček správci nainstalovat binární soubory a závislosti na vývojovém počítači.

## <a name="azure-iot-edge"></a>Azure IoT Edge

Azure IoT Edge obsahuje infrastruktury a moduly vytvářet řešení IoT brány. Můžete rozšířit IoT Edge k vytvoření brány přizpůsobit každý scénář začátku do konce.

Můžete si stáhnout [Azure IoT Edge] [ lnk-iot-edge] z Githubu.

## <a name="online-api-reference-documentation"></a>Online referenční dokumentace rozhraní API

Následující seznam obsahuje odkazy na online referenční dokumentace rozhraní API pro zařízení Azure IoT, služby a knihovny brány:

* [Internet věcí (IoT) rozhraní .NET][lnk-dotnet-ref]
* [Pro zařízení Azure IoT SDK pro jazyk Java][lnk-java-ref]
* [IoT služba Azure SDK pro jazyk Java][lnk-java-service-ref]
* [Pro zařízení Azure IoT SDK pro Node.js][lnk-node-ref]
* [IoT služba Azure SDK pro Node.js][lnk-node-service-ref]
* [Pro zařízení Azure IoT SDK pro jazyk C][lnk-c-ref]
* [Centrum IoT REST][lnk-rest-ref]
* [Azure IoT Edge][lnk-gateway-ref]

## <a name="next-steps"></a>Další postup

Zahrnout další referenční témata v této příručce pro vývojáře IoT Hub:

* [Koncové body centra IoT][lnk-devguide-endpoints]
* [IoT Hub dotazovacího jazyka pro dvojčata zařízení, úlohy a směrování zpráv][lnk-devguide-query]
* [Kvóty a omezení][lnk-devguide-quotas]
* [Podpora MQTT centra IoT][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-c-service-sdk]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_service_client
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/device
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/service
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/device
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/service
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device
[lnk-python-service-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/service
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-iot-edge]: https://github.com/Azure/iot-edge

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service
[lnk-node-service-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-gateway-ref]: http://azure.github.io/iot-edge/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
