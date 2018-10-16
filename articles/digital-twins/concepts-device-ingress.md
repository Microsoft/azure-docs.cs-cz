---
title: Zařízení připojení a telemetrie příchozí přenos dat se digitální dvojče Azure | Dokumentace Microsoftu
description: Přehled služby registrace zařízení pomocí Dvojčat digitální Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 61d05a7e9936a0edd17c5528ce4f55233b6e7e0e
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324052"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Příchozí připojení a telemetrii zařízení

Telemetrických dat odesílaných zařízeními a senzory formuláře páteřní jakékoli řešení IoT. V důsledku toho představující tyto nesourodé prostředky a jejich správu v rámci umístění je hlavní problém při vývoji aplikace IoT. Azure digitální dvojče zjednodušuje vývoj řešení IoT sdružením zařízení a senzorů s Prostorové řady grafu.

Abyste mohli začít `IoTHub` prostředků by měl být vytvořen v kořenovém adresáři prostorový graf, povolení všechna zařízení pod kořenové místa pro odesílání zpráv. Po vytvoření služby IoT Hub a zařízení s senzorů jsou zaregistrovány v rámci instance digitální dvojče zařízení můžete zahájila odesílání dat do služby digitální dvojče prostřednictvím [sady SDK Azure IoT Device](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-device-sdks).

Podrobný průvodce pro registraci zařízení najdete v [kurz k nasazení a konfiguraci digitální dvojče](tutorial-facilities-setup.md). Na první pohled postup je:

- Nasaďte instanci Azure digitální dvojče z [webu Azure portal](https://portal.azure.com)
- Vytvoření mezer v grafu
- Vytvoření `IoTHub` prostředků a přiřaďte ho ke místo v grafu
- Vytvoření zařízení a senzorů v grafu a přiřadit je k prostory vytvořené v předchozích krocích
- Vytvoření předávaný k filtrování telemetrických zpráv na základě podmínek
- Vytvoření [ **uživatelem definovanou funkci** ](concepts-user-defined-functions.md) a přiřaďte ho ke místo v grafu pro vlastní zpracování telemetrických zpráv
- Přiřazení role povolit uživatelem definovanou funkci získat přístup k datům grafu
- Získat připojovací řetězec služby IoT Hub zařízení z rozhraní API pro správu digitálních Dvojčat
- Konfigurace připojovacího řetězce zařízení na zařízení pomocí sady SDK Azure IoT Device

Dále dozvíte, jak získat připojovací řetězec služby IoT Hub zařízení z rozhraní API pro správu digitálních Dvojčata a jak používat formát zprávy telemetrických dat služby IoT Hub pro odesílání telemetrie na základě senzor. Digitální dvojče vyžaduje každá část telemetrická data, která přijme, který se má přidružit ze senzorů v rámci prostorový graf k zajištění toho, data je zpracován a směrování v rámci příslušné prostorových kontextu.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Získat připojovací řetězec služby IoT Hub zařízení z rozhraní API pro správu

Provést volání GET na rozhraní API zařízení pomocí `includes=ConnectionString` parametr získat připojovací řetězec zařízení služby IoT Hub. Můžete filtrovat podle ID identifikátor GUID nebo hardwarového zařízení najít dané zařízení:

```plaintext
https://yourManagementApiUrl/api/v1.0/devices/yourDeviceGuid?includes=ConnectionString
```

```plaintext
https://yourManagementApiUrl/api/v1.0/devices?hardwareIds=yourDeviceHardwareId&includes=ConnectionString
```

| Název vlastního atributu | Nahraďte |
| --- | --- |
| `yourManagementApiUrl` | Úplná cesta adresy URL pro vaše rozhraní API pro správu |
| `yourDeviceGuid` | ID zařízení |
| `yourDeviceHardwareId` | ID hardwaru zařízení |

V datové části odpovědi zkopírujte zařízení `connectionString` vlastnost, která při volání sady SDK Azure IoT Device budete používat k odesílání dat do Azure digitální dvojče.

## <a name="device-to-cloud-message"></a>Zařízení na cloudové zprávě

Můžete přizpůsobit formát zpráv vaše zařízení a datové části podle potřeb vašeho řešení. Můžete použít libovolný kontraktu dat, který lze serializovat do pole bajtů nebo datový proud, který je podporován [třída Azure IoT zařízení klienta zprávy, zprávu (byteArray byte [])](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). Zpráva může být vlastní binární formát podle vašeho výběru, tak dlouho, dokud dekódování kontraktu dat v odpovídající funkce definovaná uživatelem. Jediným požadavkem pro zprávy typu zařízení-Cloud je udržovat sadu vlastností (viz níže) k zajištění, že zprávy je zajistit tím správné směrování pro modul pro zpracování.

### <a name="telemetry-properties"></a>Vlastnosti telemetrie

Zatímco datová část obsahu `Message` může být libovolná data nahoru existuje několik požadavků na velikost, 256 kb na očekávané [Message.Properties](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet). Kroky uvedené níže zahrnují požadované a volitelné vlastnosti podporovaný systémem:

| Název vlastnosti | Hodnota | Požaduje se | Popis |
|---|---|---|---|
| DigitalTwins Telemetrie | 1.0 | ano | Konstantní hodnotu, která identifikuje zprávu do systému |
| DigitalTwins SensorHardwareId | `string(72)` | ano | Jedinečný identifikátor senzor odesílání `Message`. Tato hodnota musí odpovídat objektu `HardwareId` vlastnost pro systém ke zpracování. Například `00FF0643BE88-CO2`. |
| CreationTimeUtc | `string` | ne | [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) ve formátu data řetězec, který identifikuje doba vzorkování datové části. Například `2018-09-20T07:35:00.8587882-07:00`. |
| CorrelationId | `string` | ne | A `uuid` , který slouží k trasování událostí v systému. Například `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="sending-your-message-to-digital-twins"></a>Odesílání zpráv do digitální dvojče

Použít DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) nebo [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) volání k odeslání zprávy do služby digitální dvojče.

## <a name="next-steps"></a>Další postup

Další informace o Azure digitální dvojče zpracování dat a možnosti uživatelsky definovaných funkcí, přečtěte si [zpracování dat Azure digitální dvojče a uživatelem definovaných funkcí](concepts-user-defined-functions.md).

