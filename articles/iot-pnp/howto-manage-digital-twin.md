---
title: Jak spravovat IoT technologie Plug and Play digitálních vláken
description: Jak spravovat zařízení IoT technologie Plug and Play pomocí digitálních vláken API
author: prashmo
ms.author: prashmo
ms.date: 12/17/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: e68003878dc0e9275461100a59e0f45486c2978f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739867"
---
# <a name="manage-iot-plug-and-play-digital-twins"></a>Správa IoT technologie Plug and Play digitálních vláken

IoT technologie Plug and Play podporuje **získání digitálního** zdvojených operací a **aktualizaci digitálních** vláken, které umožňují spravovat digitální vlákna. Můžete použít buď [rozhraní REST API](/rest/api/iothub/service/digitaltwin) , nebo jednu ze [sad SDK služby](libraries-sdks.md).

V době psaní je digitální nevlákenná verze rozhraní API `2020-09-30` .

## <a name="update-a-digital-twin"></a>Aktualizace digitálního vlákna

Zařízení IoT technologie Plug and Play implementuje Model popsaný v tématu [Digital NEDTDLed Definition Language v2 (digitální vlákna)](https://github.com/Azure/opendigitaltwins-dtdl). Vývojáři řešení můžou k aktualizaci stavu komponenty a vlastností digitálního vlákna použít **rozhraní API aktualizace digitálního vlákna** .

Zařízení IoT technologie Plug and Play, které se používá jako příklad v tomto článku, implementuje [model řadiče teploty](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) s [termostatem](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) komponentami.

Následující fragment kódu ukazuje reakci na žádost o **získání digitálního vlákna** formátovanou jako objekt JSON. Další informace o formátu digitálního vlákna najdete v tématu [vysvětlení technologie Plug and Play digitálních vláken v IoT](./concepts-digital-twin.md#digital-twin-example):

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 20.4,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 20.4,
                "desiredVersion": 4,
                "ackVersion": 4,
                "ackCode": 200,
                "ackDescription": "Successfully executed patch",
                "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
            },
            "maxTempSinceLastReboot": {
                "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
            }
        }
    },
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

Digitální vlákna umožňují aktualizovat celou komponentu nebo vlastnost pomocí [opravy JSON](http://jsonpatch.com/).

Můžete například aktualizovat `targetTemperature` vlastnost následujícím způsobem:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    }
]
```

Předchozí aktualizace nastaví požadovanou hodnotu vlastnosti v odpovídající úrovni komponenty `$metadata` , jak je znázorněno v následujícím fragmentu kódu. IoT Hub aktualizuje požadovanou verzi vlastnosti:

```json
"thermostat1": {
    "targetTemperature": 20.4,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.4,
            "desiredVersion": 5,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        }
    }
}
```

### <a name="add-replace-or-remove-a-component"></a>Přidání, nahrazení nebo odebrání součásti

Operace na úrovni součásti vyžadují prázdnou `$metadata` značku objektu v rámci hodnoty.

Operace přidat nebo nahradit komponentu nastaví požadované hodnoty všech poskytovaných vlastností. Vymaže také požadované hodnoty pro všechny zapisovatelné vlastnosti, které nejsou součástí aktualizace.

Odebrání komponenty vymaže požadované hodnoty všech vlastností, které jsou k zápisu k dispozici. Zařízení nakonec provede synchronizaci tohoto odebrání a zastaví vytváření sestav jednotlivých vlastností. Komponenta je pak odebrána z digitálního vlákna.

Následující ukázka opravy JSON ukazuje, jak přidat, nahradit nebo odebrat komponentu:

```json
[
    {
        "op": "add",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "anotherWritableProperty": 42,
            "$metadata": {}
        }
    },
    {
        "op": "replace",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "$metadata": {}
        }
    },
    {
        "op": "remove",
        "path": "/thermostat2"
    }
]
```

### <a name="add-replace-or-remove-a-property"></a>Přidat, nahradit nebo odebrat vlastnost

Operace přidat nebo nahradit nastaví požadovanou hodnotu vlastnosti. Zařízení může synchronizovat stav a ohlásit aktualizaci hodnoty spolu s `ack` kódem, verzí a popisem.

Odebráním vlastnosti se vymaže požadovaná hodnota vlastnosti, pokud je nastavená. Zařízení pak může tuto vlastnost přestat hlásit a ta se odebere z komponenty. Pokud je tato vlastnost poslední v součásti, je odebrána i tato součást.

Následující ukázka opravy JSON ukazuje, jak přidat, nahradit nebo odebrat vlastnost v rámci součásti:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    },
    {
        "op": "replace",
        "path": "/thermostat1/anotherWritableProperty",
        "value": 42
    },
    {
        "op": "remove",
        "path": "/thermostat2/targetTemperature",
    }
]
```

### <a name="rules-for-setting-the-desired-value-of-a-digital-twin-property"></a>Pravidla pro nastavení požadované hodnoty vlastnosti digitálního vlákna

**Název**

Název komponenty nebo vlastnosti musí být platný název DTDL v2.

Povolené znaky jsou a-z, A-Z, 0-9 (nikoli jako první znak) a podtržítko (nikoli jako první nebo poslední znak).

Název může být 1-64 znaků dlouhý.

**Hodnota vlastnosti**

Hodnotou musí být platná [vlastnost DTDL v2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property).

Jsou podporovány všechny primitivní typy. V rámci složitých typů se podporují výčty, mapy a objekty. Další informace najdete v tématu [schémata DTDL v2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas).

Vlastnosti nepodporují pole ani žádné složité schéma s polem.

Pro komplexní objekt je podporována maximální hloubka pěti úrovní.

Všechny názvy polí v rámci složitého objektu by měly být platnými názvy DTDL v2.

Všechny klíče mapy by měly být platnými názvy DTDL v2.

## <a name="troubleshoot-update-digital-twin-api-errors"></a>Řešení potíží s aktualizací digitálního vlákna API – chyby

Digitální dvojitá rozhraní API vyvolá následující obecnou chybovou zprávu:

`ErrorCode:ArgumentInvalid;'{propertyName}' exists within the device twin and is not digital twin conformant property. Please refer to aka.ms/dtpatch to update this to be conformant.`

Pokud se zobrazí tato chyba, ujistěte se, že oprava aktualizace dodržuje [pravidla pro nastavení požadované hodnoty vlastnosti digitálního vlákna](#rules-for-setting-the-desired-value-of-a-digital-twin-property) .

Když aktualizujete komponentu, ujistěte se, že je nastaven [prázdný objekt $metadata značka](#add-replace-or-remove-a-component) .

Aktualizace můžou selhat, pokud se hodnoty hlášené zařízením neshodují s [konvencemi Plug and Play IoT](./concepts-convention.md#writable-properties).

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili o digitálních prostředcích, tady je několik dalších prostředků:

- [Interakce se zařízením z vašeho řešení](quickstart-service.md)
- [Digitální zdvojené REST API IoT](/rest/api/iothub/service/digitaltwin)
- [Průzkumník Azure IoT](howto-use-iot-explorer.md)
