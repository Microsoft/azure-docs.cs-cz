---
title: Zařízení připojení a telemetrie příchozí přenos dat se digitální dvojče Azure | Dokumentace Microsoftu
description: Přehled o tom, jak zařízení připojit s Dvojčaty digitální Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: alinast
ms.openlocfilehash: 374a27b10b3928838b10e4fba24e1f36721c162a
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53433671"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Možnosti připojení zařízení a příchozí přenos telemetrických dat

Telemetrických dat odesílaných zařízeními a senzory formuláře páteřní jakékoli řešení IoT. Jak reprezentaci těchto různých prostředků a spravovat je v rámci umístění jsou hlavními připomínky můžete vystavit ve vývoji aplikací IoT. Azure digitální dvojče zjednodušuje proces vývoje řešení IoT sdružením zařízení a senzorů s Prostorové řady grafu.

Abyste mohli začít, vytvořte prostředek služby Azure IoT Hub v kořenovém adresáři prostorový graf. Prostředek služby IoT Hub umožňuje všechna zařízení pod kořenové místa pro odesílání zpráv. Po vytvoření služby IoT Hub zaregistrujte senzorů v rámci instance digitální dvojče zařízení. Zařízení může odesílat data pro službu digitální dvojče [zařízení Azure IoT SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-device-sdks).

Podrobné informace o tom, jak připojit zařízení, najdete v článku [kurz k nasazení a konfiguraci digitální dvojče](tutorial-facilities-setup.md). Na první pohled postup je:

- Nasadit instanci digitální dvojče z [webu Azure portal](https://portal.azure.com).
- Vytvoření mezer v grafu.
- Vytvoří prostředek služby IoT Hub a přiřaďte ho na místo v grafu.
- Vytvoření zařízení a senzorů v grafu a přiřadit je k prostory vytvořené v předchozích krocích.
- Vytvoření předávaný k filtrování telemetrických zpráv na základě zadaných podmínek.
- Vytvoření [uživatelem definovanou funkci](concepts-user-defined-functions.md)a přiřaďte ho ke místo v grafu pro vlastní zpracování telemetrických zpráv.
- Přiřazení role povolit uživatelem definovanou funkci získat přístup k datům grafu.
- Získání připojovacího řetězce zařízení služby IoT Hub z rozhraní API pro správu digitálních Dvojčat.
- Konfigurace připojovacího řetězce zařízení na zařízení pomocí sady SDK pro zařízení Azure IoT.

V následujících částech se dozvíte, jak získat připojovací řetězec služby IoT Hub zařízení z rozhraní API pro správu digitálních Dvojčat. Také se dozvíte, jak používat formát zprávy telemetrických dat služby IoT Hub odesílat telemetrii na základě senzoru. Digitální dvojče vyžaduje každá část telemetrie, která přijme, který se má přidružit ze senzorů v rámci prostorový graf. Tento požadavek zajistí zpracování a směrování v rámci příslušné prostorová data.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Získat připojovací řetězec služby IoT Hub zařízení z rozhraní API pro správu

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Provést volání GET na rozhraní API zařízení pomocí `includes=ConnectionString` parametr získat připojovací řetězec zařízení služby IoT Hub. Identifikátor GUID nebo ID hardwaru k vyhledání dané zařízení můžete filtrovat podle zařízení.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| Parametr | Nahradit hodnotou |
| --- | --- |
| *YOUR_DEVICE_GUID* | ID zařízení |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| Hodnota parametru | Nahradit hodnotou |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | ID hardwaru zařízení |

V datové části odpovědi zkopírujte zařízení **connectionString** vlastnost. Můžete ji použít při volání sady SDK k odesílání dat do digitální dvojče zařízení Azure IoT.

## <a name="device-to-cloud-message"></a>Zprávy typu zařízení cloud

Můžete přizpůsobit formát zpráv vaše zařízení a datové části podle potřeb vašeho řešení. Použít všechny kontraktu dat, který lze serializovat do pole bajtů nebo datový proud, který je podporován [třída Azure IoT zařízení klienta zprávy, zprávu (byteArray byte [])](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). Zpráva může být vlastní binární formát podle vašeho výběru, tak dlouho, dokud dekódování kontraktu dat v odpovídající uživatelem definované funkce. Existuje pouze jeden požadavek na zprávy typu zařízení cloud. Musíte mít sadu vlastností, abyste měli jistotu, že zprávy je zajistit tím správné směrování pro modul pro zpracování.

### <a name="telemetry-properties"></a>Vlastnosti telemetrie

 Datová část obsahu **zpráva** může být libovolná data až 256 KB. Existuje několik požadavků u vlastnosti se očekává [ `Message.Properties` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) typu. V tabulce jsou uvedeny požadované a volitelné vlastnosti podporovaný systémem.

| Název vlastnosti | Hodnota | Požaduje se | Popis |
|---|---|---|---|
| **DigitalTwins Telemetrie** | 1.0 | Ano | Konstantní hodnota, která identifikuje zprávu do systému. |
| **DigitalTwins SensorHardwareId** | `string(72)` | Ano | Jedinečný identifikátor senzor, který odešle **zpráva**. Tato hodnota musí odpovídat objektu **HardwareId** vlastnost pro systém ke zpracování. Například, `00FF0643BE88-CO2`. |
| **CreationTimeUtc** | `string` | Ne | [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) formátovaný řetězec data, která identifikuje doba vzorkování datové části. Například, `2018-09-20T07:35:00.8587882-07:00`. |
| **ID korelace** | `string` | Ne | Identifikátor UUID, který se používá pro trasování událostí v systému. Například, `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>Odeslat zprávu do digitální dvojče

Použít DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) nebo [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) volání k odeslání vaší zprávy digitální dvojče.

## <a name="next-steps"></a>Další postup

Další informace o zpracování dat Azure digitální dvojče a možnosti uživatelsky definovaných funkcí, přečtěte si [zpracování dat Azure digitální dvojče a uživatelem definovaných funkcí](concepts-user-defined-functions.md).
