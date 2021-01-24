---
title: Most IoT technologie Plug and Play | Microsoft Docs
description: Seznamte se s mostem IoT technologie Plug and Play a Naučte se používat k připojení stávajících zařízení připojených k bráně Windows nebo Linux jako zařízení IoT technologie Plug and Play.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 05b9df2c5083d24ae6a78212bdd29cd1c740e1d1
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746488"
---
# <a name="iot-plug-and-play-bridge"></a>Přemostění IoT Plug and Play

IoT technologie Plug and Play most je open-source aplikace pro připojení stávajících zařízení připojených k bráně Windows nebo Linux jako zařízení IoT technologie Plug and Play. Po instalaci a konfiguraci aplikace v počítači se systémem Windows nebo Linux ji můžete použít k připojení připojených zařízení ke službě IoT Hub. Pomocí tohoto mostu můžete namapovat rozhraní IoT technologie Plug and Play do telemetrie, která připojená zařízení odesílají, pracují s vlastnostmi zařízení a vyvolávat příkazy.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="Na levé straně je k dispozici několik stávajících senzorů (kabelových i bezdrátových) do počítače s Windows nebo Linux, který obsahuje IoT technologie Plug and Play most. IoT technologie Plug and Play most se pak připojí ke službě IoT Hub na pravé straně":::

IoT technologie Plug and Play most se dá nasadit jako samostatný spustitelný soubor na jakémkoli zařízení IoT, v průmyslových počítačích, na serverech nebo v bráně s Windows 10 nebo Linux. Může být také zkompilován do kódu aplikace. Jednoduchý konfigurační soubor JSON oznamuje službě IoT technologie Plug and Play mostu, která připojená zařízení a periferní zařízení by měla být vystavena do Azure.

## <a name="supported-protocols-and-sensors"></a>Podporované protokoly a senzory

IoT technologie Plug and Play most podporuje ve výchozím nastavení následující typy periferních zařízení s odkazy na dokumentaci k adaptéru:

|Okraj|Windows|Linux|
|---------|---------|---------|
|[Adaptér snímače Bluetooth](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/bluetooth_sensor_adapter.md) připojuje senzory s povoleným Bluetooth s nízkou spotřebou (tivovat).       |Ano|No|
|[Adaptér kamery](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/camera_adapter.md) připojuje kamery na zařízení s Windows 10.               |Ano|No|
|[Modbus adaptér](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/modbus_adapters.md) připojuje senzory na zařízení Modbus.              |Ano|Ano|
|[MQTT adaptér](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/mqtt_adapter.md) připojuje zařízení, která používají zprostředkovatele MQTT.                  |Ano|Ano|
|[SerialPnP adaptér](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/serialpnp/Readme.md) připojuje zařízení, která komunikují přes sériové připojení.               |Ano|Ano|
|[Periferní zařízení s Windows USB](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/docs/coredevicehealth_adapter.md) využívají seznam tříd rozhraní zařízení podporovaných adaptérem pro připojení zařízení, která mají určité ID hardwaru.  |Ano|Neuvedeno|

Další informace o tom, jak technologie Plug and Play most IoT pro podporu dalších protokolů zařízení, najdete v tématu věnovaném [rozšiřování technologie Plug and Play mostu IoT](howto-author-pnp-bridge-adapter.md). Informace o tom, jak sestavit a nasadit most IoT technologie Plug and Play, najdete v tématu [sestavení a nasazení technologie Plug and Play mostu IoT](howto-build-deploy-extend-pnp-bridge.md).

## <a name="iot-plug-and-play-bridge-architecture"></a>Architektura IoT technologie Plug and Play mostu

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="Na levé straně je několik polí, která označují různá periferní zařízení připojená k počítači s Windows nebo Linux obsahujícím IoT technologie Plug and Play most. V horní části je pole označené konfigurační body směrem k mostu. Most se pak připojí ke službě IoT Hub na pravé straně diagramu.":::

### <a name="iot-plug-and-play-bridge-adapters"></a>Adaptéry pro mosty IoT technologie Plug and Play

IoT technologie Plug and Play most podporuje sadu adaptérů IoT technologie Plug and Play mostu pro různé typy zařízení. *Manifest adaptéru* staticky definuje adaptéry pro most.

Správce adaptéru mostu používá k identifikaci a volání funkcí adaptéru manifest. Správce adaptéru volá pouze funkci CREATE na adaptérech mostu, které jsou vyžadovány komponentami rozhraní uvedenými v konfiguračním souboru. Instance adaptéru se vytvoří pro každou komponentu technologie Plug and Play IoT.

Adaptér mostu vytvoří a získá popisovač digitálního vlákna. Adaptér používá tento popisovač k navázání funkce zařízení k digitálnímu vlákna.

Pomocí informací v konfiguračním souboru umožňuje adaptér mostu použít následující postupy, pomocí kterých umožní kompletnímu zařízení používat digitální dvojitou komunikaci prostřednictvím mostu:

- Naváže komunikační kanál přímo.
- Vytvoří sledovací proces zařízení, který čeká, až bude dostupný komunikační kanál.

### <a name="configuration-file"></a>Konfigurační soubor

IoT technologie Plug and Play most používá konfigurační soubor založený na formátu JSON, který určuje:

- Jak se připojit ke službě IoT Hub nebo k aplikaci IoT Central: možnosti zahrnují připojovací řetězce, parametry ověřování nebo službu Device Provisioning (DPS).
- Umístění modelů schopností technologie Plug and Play IoT, které používá most. Model definuje možnosti zařízení IoT technologie Plug and Play a je statický a neměnný.
- Seznam komponent rozhraní IoT technologie Plug and Play a následující informace pro každou součást:
- ID rozhraní a název součásti.
- Adaptér mostu potřebný k interakci s komponentou.
- Informace o zařízení, které adaptér mostu potřebuje k navázání komunikace se zařízením. Například ID hardwaru nebo konkrétní informace pro adaptér, rozhraní nebo protokol.
- Volitelný typ podtypu adaptéru mostu nebo konfigurace rozhraní, pokud adaptér podporuje více typů komunikace s podobnými zařízeními. Příklad ukazuje, jak lze nakonfigurovat komponentu senzoru Bluetooth:

    ```json
    {
      "_comment": "Component BLE sensor",
      "pnp_bridge_component_name": "blesensor1",
      "pnp_bridge_adapter_id": "bluetooth-sensor-pnp-adapter",
      "pnp_bridge_adapter_config": {
        "bluetooth_address": "267541100483311",
        "blesensor_identity" : "Blesensor1"
      }
    }
    ```

- Volitelný seznam parametrů adaptéru globálního mostu. Například adaptér mostu Bluetooth snímače má slovník podporovaných konfigurací. Komponenta rozhraní, která vyžaduje, aby adaptér senzoru Bluetooth mohl zvolit jednu z těchto konfigurací `blesensor_identity` :

    ```json
    {
      "pnp_bridge_adapter_global_configs": {
        "bluetooth-sensor-pnp-adapter": {
          "Blesensor1" : {
            "company_id": "0x499",
            "endianness": "big",
            "telemetry_descriptor": [
              {
                "telemetry_name": "humidity",
                "data_parse_type": "uint8",
                "data_offset": 1,
                "conversion_bias": 0,
                "conversion_coefficient": 0.5
              },
              {
                "telemetry_name": "temperature",
                "data_parse_type": "int8",
                "data_offset": 2,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "pressure",
                "data_parse_type": "int16",
                "data_offset": 4,
                "conversion_bias": 0,
                "conversion_coefficient": 1.0
              },
              {
                "telemetry_name": "acceleration_x",
                "data_parse_type": "int16",
                "data_offset": 6,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_y",
                "data_parse_type": "int16",
                "data_offset": 8,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              },
              {
                "telemetry_name": "acceleration_z",
                "data_parse_type": "int16",
                "data_offset": 10,
                "conversion_bias": 0,
                "conversion_coefficient": 0.00980665
              }
            ]
          }
        }
      }
    }
    ```

## <a name="download-iot-plug-and-play-bridge"></a>Stáhnout IoT technologie Plug and Play most

Můžete si stáhnout předem vytvořenou verzi mostu s podporovanými adaptéry ve službě [IoT technologie Plug and Play mostech](https://github.com/Azure/iot-plug-and-play-bridge/releases) a rozšířit seznam prostředků pro nejnovější verzi. Stáhněte si nejnovější verzi aplikace pro váš operační systém.

Můžete si také stáhnout a zobrazit zdrojový kód pro [IoT technologie Plug and Play Bridge na GitHubu](https://github.com/Azure/iot-plug-and-play-bridge).

## <a name="next-steps"></a>Další kroky

Teď, když máte přehled o architektuře IoT technologie Plug and Play Bridge, další kroky jsou další informace o:

- [Jak připojit ukázku služby IoT technologie Plug and Play Bridge běžící v systému Linux nebo Windows do IoT Hub](./howto-use-iot-pnp-bridge.md)
- [Sestavování a nasazování IoT technologie Plug and Play mostu](howto-build-deploy-extend-pnp-bridge.md)
- [Rozšiřování IoT technologie Plug and Play mostu](howto-build-deploy-extend-pnp-bridge.md)
- [IoT technologie Plug and Play most na GitHubu](https://github.com/Azure/iot-plug-and-play-bridge)
