---
title: Principy sad SDK Azure IoT | Dokumentace Microsoftu
description: Příručka pro vývojáře – informace a odkazy na různé Azure IoT zařízení a služby sady SDK, které můžete použít k vytváření aplikací pro zařízení a back endové aplikace.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dobett
ms.openlocfilehash: d58c86c17cdab360f37a09b28bdf705cb781a620
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50023814"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Pochopit a používat sady SDK služby Azure IoT Hub

Existují tři kategorie software development Kit (SDK) pro práci se službou IoT Hub:

* **Sady SDK pro zařízení** umožňují vývoj aplikací pro zařízení IoT pomocí klienta zařízení nebo modul klienta. Tyto aplikace odesílat telemetrická data do služby IoT hub a volitelně přijímat zprávy, úlohu, metody nebo aktualizace dvojčat ze služby IoT hub.  Modul klienta můžete použít také k vytváření [moduly](../iot-edge/iot-edge-modules.md) pro [modul runtime Azure IoT Edge](../iot-edge/about-iot-edge.md).

* **Sady SDK pro služby** vám umožní spravovat vaše Centrum IoT a volitelně zasílání zpráv, plánování úloh, vyvolání přímých metod nebo odeslání požadovanou vlastnost aktualizací do zařízení IoT nebo moduly.

* **Sady SDK zařízení pro zřizování** umožňují zřizovat zařízení do služby IoT Hub pomocí [služby Device Provisioning](../iot-dps/about-iot-dps.md).

Další informace o [výhodami vývoje pomocí sad Azure IoT SDK](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Azure IoT SDK pro zařízení

Zařízení Microsoft Azure IoT SDK obsahovat kód, který usnadňuje vytváření zařízení a aplikací, připojení k a jsou spravované pomocí služby Azure IoT Hub.

Azure IoT Hub device sady SDK pro .NET: 

* Nainstalujte z [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-csharp)
* [Referenční materiály k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Odkaz na modul](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

Azure IoT Hub device sady SDK pro jazyk C, zapsán ve standardu ANSI C (C99) pro přenositelnost a kompatibilita různé platformy:

* Nainstalujte z [apt-get, MBED, rozhraním Arduino IDE nebo Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-c)
* [Referenční materiály k rozhraní API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [Odkaz na modul](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_module_client.h)

Azure IoT Hub device sady SDK pro Javu: 

* Přidat do [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk) projektu
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-java)
* [Referenční materiály k rozhraní API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Odkaz na modul](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._module_client?view=azure-java-stable)

Azure IoT Hub device sady SDK pro Node.js: 

* Nainstalujte z [npm](https://www.npmjs.com/package/azure-iot-device)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-node)
* [Referenční materiály k rozhraní API](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Odkaz na modul](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Azure IoT Hub device sady SDK pro Python: 

* Nainstalujte z [pip](https://pypi.python.org/pypi/azure-iothub-device-client/)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-python)
* Reference k rozhraní API: viz [reference k rozhraní API jazyka C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

Azure IoT Hub device sady SDK pro iOS: 

* Nainstalujte z [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Ukázky](https://github.com/Azure-Samples/azure-iot-samples-ios)
* Reference k rozhraní API: viz [reference k rozhraní API jazyka C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

> [!NOTE]
> Prohlédněte si soubory readme v úložištích GitHub. informace o používání jazyka a správců balíčků specifických pro platformu nainstalovat binární soubory a závislosti na vývojovém počítači.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Kompatibilita operačního systému platformy a hardwaru

Podporované platformy pro sady SDK najdete v [podpora platformy sady SDK Azure IoT](iot-hub-device-sdk-platform-support.md).

Další informace o kompatibilitě sady SDK s konkrétním hardwarovým zařízením, najdete v článku [programu Azure Certified for IoT zařízení katalogu](https://catalog.azureiotsuite.com/) nebo na individuální úložiště.

## <a name="azure-iot-service-sdks"></a>Sady SDK pro služby Azure IoT

Sady SDK služby Azure IoT obsahují kód pro usnadnění vytváření aplikací, které spolupracují přímo se službou IoT Hub ke správě zařízení a zabezpečení.

Azure IoT Hub služba SDK pro .NET:

* Stáhnout z [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices/)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-csharp)
* [Referenční materiály k rozhraní API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

Azure IoT Hub sady SDK služby pro Javu: 

* Přidat do [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
) projektu
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-java)
* [Referenční materiály k rozhraní API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

Azure IoT Hub sady SDK služby pro Node.js: 

* Stáhnout z [npm](https://www.npmjs.com/package/azure-iothub)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-node)
* [Referenční materiály k rozhraní API](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Azure IoT Hub sady SDK služby pro jazyk Python: 

* Stáhnout z [pip](https://pypi.python.org/pypi/azure-iothub-service-client/)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-python)

Azure IoT Hub sady SDK služby pro jazyk C: 

* Stáhnout z [apt-get, MBED, rozhraním Arduino IDE nebo Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Zdrojový kód](https://github.com/Azure/azure-iot-sdk-c)

Azure IoT Hub služba SDK pro iOS: 

* Nainstalujte z [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Ukázky](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Prohlédněte si soubory readme v úložištích GitHub. informace o používání jazyka a správců balíčků specifických pro platformu nainstalovat binární soubory a závislosti na vývojovém počítači.

## <a name="device-provisioning-sdks"></a>Zřizování sady SDK pro zařízení

**Sadami SDK zřizování služby Microsoft Azure** umožňují zřizovat zařízení do služby IoT Hub pomocí [služby Device Provisioning](../iot-dps/about-iot-dps.md).

Azure zřizování zařízení a služby sady SDK pro jazyk C#:

* [Klientská sada SDK zařízení pro zřizování](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/device)
* [Klientská sada SDK služby zřizování](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/service)

Azure zřizování zařízení a služby sady SDK pro Javu:

* [Klientská sada SDK zařízení pro zřizování](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-device-client)
* [Klientská sada SDK služby zřizování](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-service-client)

Azure zřizování zařízení a služby sady SDK pro Node.js:

* [Klientská sada SDK zařízení pro zřizování](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device)
* [Klientská sada SDK služby zřizování](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)

Azure zřizování zařízení a služby sady SDK pro Python:

* [Klientská sada SDK zařízení pro zřizování](https://github.com/Azure/azure-iot-sdk-python/blob/master/provisioning_device_client)
* [Klientská sada SDK služby zřizování](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client)

Azure zřizování zařízení a služby sady SDK pro jazyk C:

* [Klientská sada SDK zařízení pro zřizování](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [Klientská sada SDK služby zřizování](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_service_client)

## <a name="next-steps"></a>Další postup

Sady SDK Azure IoT také poskytují sadu nástrojů, které pomůžou s vývojem:
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): související nástroje příkazového řádku pro různé platformy pro usnadnění diagnostiky potíží s připojením službou IoT Hub.
* [Průzkumník zařízení](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer): aplikace klasické pracovní plochy Windows k připojení ke službě IoT Hub.

Další referenční témata v této příručce pro vývojáře IoT Hub patří:

* [Koncové body IoT Hubu](iot-hub-devguide-endpoints.md)
* [Dotazovací jazyk služby IoT Hub pro dvojčata zařízení, úlohy a směrování zpráv](iot-hub-devguide-query-language.md)
* [Kvóty a omezování](iot-hub-devguide-quotas-throttling.md)
* [Podpora MQTT centra IoT](iot-hub-mqtt-support.md)
* [Reference k rozhraní API REST centra IoT](/rest/api/iothub/)
* [Podpora platformy Azure IoT SDK](iot-hub-device-sdk-platform-support.md)