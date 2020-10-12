---
title: Datová část telemetrie, vlastností a příkazů v Azure IoT Central | Microsoft Docs
description: Šablony zařízení Azure IoT Central umožňují určit telemetrii, vlastnosti a příkazy zařízení, které musí implementovat. Porozumíte formátu dat, která zařízení můžou vyměňovat pomocí IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 554079ddec3332ced2817d18ea55ce1260d68817
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87291608"
---
# <a name="telemetry-property-and-command-payloads"></a>Datové části telemetrie, vlastností a příkazů

_Tento článek se týká vývojářů zařízení._

Šablona zařízení v Azure IoT Central je podrobný plán, který definuje:

* Telemetrie zařízení odesílá IoT Central.
* Vlastnosti zařízení se synchronizuje s IoT Central.
* Příkazy, které IoT Central volání na zařízení.

Tento článek popisuje, pro vývojáře zařízení, datové části JSON, které zařízení odesílají a získávají pro telemetrii, vlastnosti a příkazy definované v šabloně zařízení.

Tento článek nepopisuje všechny možné typy telemetrie, vlastností a datových částí příkazu, ale příklady ilustrují všechny typy klíčů.

Každý příklad ukazuje fragment kódu z modelu funkce zařízení (DCM), který definuje typ a ukázkovou datovou část JSON pro ilustraci, jak by mělo zařízení pracovat s aplikací IoT Central.

> [!NOTE]
> IoT Central akceptuje platný JSON, ale může se použít jenom pro vizualizace, pokud se shoduje s definicí v DCM. Data, která se neshodují s definicí, můžete exportovat v tématu [Export dat IoT do cílových umístění v Azure](howto-export-data.md).

Soubor JSON, který definuje DCM, používá [digitální DTDL (digitald Definition Language) V1](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/dtdlv1.md). Tato specifikace zahrnuje definici `@id` formátu vlastnosti.

Vzorový kód zařízení, který zobrazuje některé z těchto používaných datových částí, najdete v tématu věnovaném [Vytvoření a připojení klientské aplikace do aplikace azure IoT Central (Node.js)](tutorial-connect-device-nodejs.md) a [Vytvoření a připojení klientské aplikace k vašim kurzům pro Azure IoT Central aplikace (Python)](tutorial-connect-device-python.md) .

## <a name="view-raw-data"></a>Zobrazit nezpracovaná data

IoT Central umožňuje zobrazit nezpracovaná data, která zařízení odesílá do aplikace. Toto zobrazení je užitečné při řešení potíží s datovou částí odeslanou ze zařízení. Chcete-li zobrazit nezpracovaná data, která zařízení odesílá:

1. Přejděte na zařízení na stránce **zařízení** .

1. Vyberte kartu **nezpracovaná data** :

    :::image type="content" source="media/concepts-telemetry-properties-commands/raw-data.png" alt-text="Zobrazení nezpracovaných dat":::

    V tomto zobrazení můžete vybrat sloupce, které chcete zobrazit, a nastavit časový rozsah, který chcete zobrazit. Sloupec **nemodelovaná data** zobrazuje data ze zařízení, která neodpovídají žádné definici vlastnosti nebo telemetrie v šabloně zařízení.

## <a name="telemetry"></a>Telemetrie

### <a name="primitive-types"></a>Primitivní typy

V této části jsou uvedeny příklady primitivních typů telemetrie, které zařízení streamuje do aplikace IoT Central.

Následující fragment kódu z DCM ukazuje definici `boolean` typu telemetrie:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "BooleanTelemetry"
  },
  "name": "BooleanTelemetry",
  "schema": "boolean"
}
```

Klient zařízení by měl odeslat telemetrii jako JSON, která vypadá jako v následujícím příkladu:

```json
{ "BooleanTelemetry": true }
```

Následující fragment kódu z DCM ukazuje definici `string` typu telemetrie:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "StringTelemetry"
  },
  "name": "StringTelemetry",
  "schema": "string"
}
```

Klient zařízení by měl odeslat telemetrii jako JSON, která vypadá jako v následujícím příkladu:

```json
{ "StringTelemetry": "A string value - could be a URL" }
```

Následující fragment kódu z DCM ukazuje definici `integer` typu telemetrie:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "IntegerTelemetry"
  },
  "name": "IntegerTelemetry",
  "schema": "integer"
}

```

Klient zařízení by měl odeslat telemetrii jako JSON, která vypadá jako v následujícím příkladu:

```json
{ "IntegerTelemetry": 23 }
```

Následující fragment kódu z DCM ukazuje definici `double` typu telemetrie:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DoubleTelemetry"
  },
  "name": "DoubleTelemetry",
  "schema": "double"
}
```

Klient zařízení by měl odeslat telemetrii jako JSON, která vypadá jako v následujícím příkladu:

```json
{ "DoubleTelemetry": 56.78 }
```

Následující fragment kódu z DCM ukazuje definici `dateTime` typu telemetrie:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DateTimeTelemetry"
  },
  "name": "DateTimeTelemetry",
  "schema": "dateTime"
}
```

Klient zařízení by měl odeslat telemetrii jako JSON, která vypadá jako v následujících příkladech, `DateTime` musí být kompatibilní s ISO 8061:

```json
{ "DateTimeTelemetry": "2020-08-30T19:16:13.853Z" }
```

Následující fragment kódu z DCM ukazuje definici `duration` typu telemetrie:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "DurationTelemetry"
  },
  "name": "DurationTelemetry",
  "schema": "duration"
}
```

Klient zařízení by měl odeslat telemetrii jako JSON, která vypadá jako v následujícím příkladu – doba trvání musí odpovídat normám ISO 8601:

```json
{ "DurationTelemetry": "PT10H24M6.169083011336625S" }
```

### <a name="complex-types"></a>Komplexní typy

V této části jsou uvedeny příklady komplexních typů telemetrie, které zařízení streamuje do aplikace IoT Central.

Následující fragment kódu z DCM ukazuje definici `geopoint` typu telemetrie:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "GeopointTelemetry"
  },
  "name": "GeopointTelemetry",
  "schema": "geopoint"
}
```

Klient zařízení by měl odeslat telemetrii jako JSON, která vypadá jako v následujícím příkladu. IoT Central zobrazuje hodnotu jako kód PIN na mapě:

```json
{
  "GeopointTelemetry": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Následující fragment kódu z DCM ukazuje definici `Enum` typu telemetrie:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "EnumTelemetry"
  },
  "name": "EnumTelemetry",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Klient zařízení by měl odeslat telemetrii jako JSON, která vypadá jako v následujícím příkladu. Možné hodnoty jsou `0` , `1` a `2` , které se zobrazují v IoT Central jako `Item1` , `Item2` a `Item3` :

```json
{ "EnumTelemetry": 1 }
```

Následující fragment kódu z DCM ukazuje definici `Object` typu telemetrie. Tento objekt má tři pole s typy `dateTime` , `integer` a `Enum` :

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "ObjectTelemetry"
  },
  "name": "ObjectTelemetry",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property1"
        },
        "name": "Property1",
        "schema": "dateTime"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property2"
        },
        "name": "Property2",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Property3"
        },
        "name": "Property3",
        "schema": {
          "@id": "<element id>",
          "@type": "Enum",
          "displayName": {
            "en": "Enum"
          },
          "valueSchema": "integer",
          "enumValues": [
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item1"
              },
              "enumValue": 0,
              "name": "Item1"
            },
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item2"
              },
              "enumValue": 1,
              "name": "Item2"
            },
            {
              "@id": "<element id>",
              "@type": "EnumValue",
              "displayName": {
                "en": "Item3"
              },
              "enumValue": 2,
              "name": "Item3"
            }
          ]
        }
      }
    ]
  }
}
```

Klient zařízení by měl odeslat telemetrii jako JSON, která vypadá jako v následujícím příkladu. `DateTime` typy musí být kompatibilní s ISO 8061. Možné hodnoty pro `Property3` jsou `0` , `1` a, které se zobrazují v IoT Central jako `Item1` , `Item2` a `Item3` :

```json
{
  "ObjectTelemetry": {
      "Property1": "2020-09-09T03:36:46.195Z",
      "Property2": 37,
      "Property3": 2
  }
}
```

Následující fragment kódu z DCM ukazuje definici `vector` typu telemetrie:

```json
{
  "@id": "<element id>",
  "@type": "Telemetry",
  "displayName": {
    "en": "VectorTelemetry"
  },
  "name": "VectorTelemetry",
  "schema": "vector"
}
```

Klient zařízení by měl odeslat telemetrii jako JSON, která vypadá jako v následujícím příkladu:

```json
{
  "VectorTelemetry": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="event-and-state-types"></a>Typy událostí a stavů

V této části jsou uvedeny příklady událostí telemetrie a stavy, které zařízení odesílá do aplikace IoT Central.

Následující fragment kódu z DCM zobrazuje definici `integer` typu události:

```json
{
  "@id": "<element id>",
  "@type": [
    "Telemetry",
    "SemanticType/Event"
  ],
  "displayName": {
    "en": "IntegerEvent"
  },
  "name": "IntegerEvent",
  "schema": "integer"
}
```

Klient zařízení by měl odesílat data události jako formát JSON, který vypadá jako v následujícím příkladu:

```json
{ "IntegerEvent": 74 }
```

Následující fragment kódu z DCM ukazuje definici `integer` typu stavu:

```json
{
  "@id": "<element id>",
  "@type": [
    "Telemetry",
    "SemanticType/State"
  ],
  "displayName": {
    "en": "IntegerState"
  },
  "name": "IntegerState",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level1"
        },
        "enumValue": 1,
        "name": "Level1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level2"
        },
        "enumValue": 2,
        "name": "Level2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Level3"
        },
        "enumValue": 3,
        "name": "Level3"
      }
    ]
  }
}
```

Klient zařízení by měl odeslat stav jako JSON, který vypadá jako v následujícím příkladu. Možné hodnoty celočíselného stavu jsou `1` , `2` nebo `3` :

```json
{ "IntegerState": 2 }
```

## <a name="properties"></a>Vlastnosti

> [!NOTE]
> Formáty datové části pro vlastnosti se vztahují na aplikace vytvořené v nebo po 07/14/2020.

### <a name="primitive-types"></a>Primitivní typy

V této části jsou uvedeny příklady primitivních typů vlastností, které zařízení odesílá do aplikace IoT Central.

Následující fragment kódu z DCM ukazuje definici `boolean` typu vlastnosti:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "BooleanProperty"
  },
  "name": "BooleanProperty",
  "schema": "boolean"
}
```

Klient zařízení by měl poslat datovou část JSON, která vypadá jako v následujícím příkladu, jako hlášená vlastnost v zařízení s dvojitou vlastností:

```json
{ "BooleanProperty": false }
```

Následující fragment kódu z DCM ukazuje definici `boolean` typu vlastnosti:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "LongProperty"
  },
  "name": "LongProperty",
  "schema": "long"
}
```

Klient zařízení by měl poslat datovou část JSON, která vypadá jako v následujícím příkladu, jako hlášená vlastnost v zařízení s dvojitou vlastností:

```json
{ "LongProperty": 439 }
```

Následující fragment kódu z DCM ukazuje definici `date` typu vlastnosti:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "DateProperty"
  },
  "name": "DateProperty",
  "schema": "date"
}
```

Klient zařízení by měl poslat datovou část JSON, která vypadá jako v následujícím příkladu jako vlastnost hlášená v zařízení je v nevlákna. `Date` typy musí být kompatibilní s ISO 8061:

```json
{ "DateProperty": "2020-05-17" }
```

Následující fragment kódu z DCM ukazuje definici `duration` typu vlastnosti:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "DurationProperty"
  },
  "name": "DurationProperty",
  "schema": "duration"
}
```

Klient zařízení by měl poslat datovou část JSON, která vypadá jako v následujícím příkladu jako vlastnost hlášená v době trvání vlákna zařízení musí být kompatibilní s normou ISO 8601:

```json
{ "DurationProperty": "PT10H24M6.169083011336625S" }
```

Následující fragment kódu z DCM ukazuje definici `float` typu vlastnosti:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "FloatProperty"
  },
  "name": "FloatProperty",
  "schema": "float"
}
```

Klient zařízení by měl poslat datovou část JSON, která vypadá jako v následujícím příkladu, jako hlášená vlastnost v zařízení s dvojitou vlastností:

```json
{ "FloatProperty": 1.9 }
```

Následující fragment kódu z DCM ukazuje definici `string` typu vlastnosti:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "StringProperty"
  },
  "name": "StringProperty",
  "schema": "string"
}
```

Klient zařízení by měl poslat datovou část JSON, která vypadá jako v následujícím příkladu, jako hlášená vlastnost v zařízení s dvojitou vlastností:

```json
{ "StringProperty": "A string value - could be a URL" }
```

### <a name="complex-types"></a>Komplexní typy

V této části jsou uvedeny příklady komplexních typů vlastností, které zařízení odesílá do aplikace IoT Central.

Následující fragment kódu z DCM ukazuje definici `geopoint` typu vlastnosti:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "GeopointProperty"
  },
  "name": "GeopointProperty",
  "schema": "geopoint"
}
```

Klient zařízení by měl poslat datovou část JSON, která vypadá jako v následujícím příkladu, jako hlášená vlastnost v zařízení s dvojitou vlastností:

```json
{
  "GeopointProperty": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Následující fragment kódu z DCM ukazuje definici `Enum` typu vlastnosti:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "EnumProperty"
  },
  "name": "EnumProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Klient zařízení by měl poslat datovou část JSON, která vypadá jako v následujícím příkladu jako vlastnost hlášená v zařízení je v nevlákna. Možné hodnoty jsou `0` , `1` a, které se zobrazují v IoT Central jako `Item1` , `Item2` a `Item3` :

```json
{ "EnumProperty": 1 }
```

Následující fragment kódu z DCM ukazuje definici `Object` typu vlastnosti. Tento objekt má dvě pole s typy `string` a `integer` :

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

Klient zařízení by měl poslat datovou část JSON, která vypadá jako v následujícím příkladu, jako hlášená vlastnost v zařízení s dvojitou vlastností:

```json
{
  "ObjectProperty": {
    "Field1": 37,
    "Field2": "A string value"
  }
}
```

Následující fragment kódu z DCM ukazuje definici `vector` typu vlastnosti:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "VectorProperty"
  },
  "name": "VectorProperty",
  "schema": "vector"
}
```

Klient zařízení by měl poslat datovou část JSON, která vypadá jako v následujícím příkladu, jako hlášená vlastnost v zařízení s dvojitou vlastností:

```json
{
  "VectorProperty": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="writeable-property-types"></a>Zapisovatelné typy vlastností

V této části jsou uvedeny příklady typů vlastností s možností zápisu, které zařízení obdrží z aplikace IoT Central.

IoT Central očekává odpověď ze zařízení do zapisovatelných aktualizací vlastností. Zpráva odpovědi by měla zahrnovat `ac` pole a `av` . Pole `ad` je nepovinné. Příklady najdete v následujících fragmentech kódu.

`ac` je číselné pole, které používá hodnoty v následující tabulce:

| Hodnota | Popisek | Description |
| ----- | ----- | ----------- |
| `'ac': 200` | Dokončeno | Operace změny vlastnosti byla úspěšně dokončena. |
| `'ac': 202`  ani `'ac': 201` | Čekající | Operace změny vlastnosti čeká na vyřízení nebo probíhá. |
| `'ac': 4xx` | Chyba | Požadovaná změna vlastnosti nebyla platná nebo došlo k chybě. |
| `'ac': 5xx` | Chyba | U zařízení došlo k neočekávané chybě při zpracování požadované změny. |

`av` je číslo verze odesílané do zařízení.

`ad` je popis řetězce možností.

Následující fragment kódu z DCM ukazuje definici `string` typu zapisovatelné vlastnosti:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "StringPropertyWritable"
  },
  "name": "StringPropertyWritable",
  "writable": true,
  "schema": "string"
}
```

Zařízení obdrží následující datovou část z IoT Central:

```json
{  
  "StringPropertyWritable": "A string from IoT Central", "$version": 7
}
```

Zařízení by mělo po zpracování aktualizace odeslat následující datovou část JSON, která se IoT Central. Tato zpráva obsahuje číslo verze původní aktualizace přijaté od IoT Central. Když IoT Central obdrží tuto zprávu, označí vlastnost jako **synchronizovanou** v uživatelském rozhraní:

```json
{
  "StringPropertyWritable": {
    "value": "A string from IoT Central",
    "ac": 200,
    "ad": "completed",
    "av": 7
  }
}
```

Následující fragment kódu z DCM ukazuje definici `Enum` typu zapisovatelné vlastnosti:

```json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "EnumPropertyWritable"
  },
  "name": "EnumPropertyWritable",
  "writable": true,
  "schema": {
    "@id": "<element id>",
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "@id": "<element id>",
        "@type": "EnumValue",
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Zařízení obdrží následující datovou část z IoT Central:

```json
{  
  "EnumPropertyWritable":  1 , "$version": 10
}
```

Zařízení by mělo po zpracování aktualizace odeslat následující datovou část JSON, která se IoT Central. Tato zpráva obsahuje číslo verze původní aktualizace přijaté od IoT Central. Když IoT Central obdrží tuto zprávu, označí vlastnost jako **synchronizovanou** v uživatelském rozhraní:

```json
{
  "EnumPropertyWritable": {
    "value": 1,
    "ac": 200,
    "ad": "completed",
    "av": 10
  }
}
```

## <a name="commands"></a>Příkazy

### <a name="synchronous-command-types"></a>Synchronní typy příkazů

Následující fragment kódu z DCM zobrazuje definici synchronního příkazu, který nemá žádné parametry a neočekává, že zařízení vrátí cokoli:

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "displayName": {
    "en": "SynchronousCommandBasic"
  },
  "name": "SynchronousCommandBasic"
}
```

Zařízení obdrží v žádosti prázdnou datovou část a v odpovědi musí vracet prázdnou datovou část s `200` kódem odpovědi HTTP, aby označovala úspěch.

Následující fragment kódu z DCM ukazuje definici synchronního příkazu, který má celočíselný parametr a očekává, že zařízení vrátí celočíselnou hodnotu:

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "SynchronousCommandSimple"
  },
  "name": "SynchronousCommandSimple"
}
```

Zařízení obdrží jako datovou část požadavku celočíselnou hodnotu. Zařízení by mělo vracet celočíselnou hodnotu jako datovou část odpovědi s `200` kódem odpovědi HTTP, aby označovalo úspěch.

Následující fragment kódu z DCM zobrazuje definici synchronního příkazu, který obsahuje parametr objektu a očekává, že zařízení vrátí objekt. V tomto příkladu mají oba objekty celočíselné a řetězcové pole:

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "synchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": {
      "@id": "<element id>",
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": {
      "@id": "<element id>",
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "@id": "<element id>",
          "@type": "SchemaField",
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "displayName": {
    "en": "SynchronousCommandComplex"
  },
  "name": "SynchronousCommandComplex"
}
```

Následující fragment kódu ukazuje ukázkovou datovou část požadavku odeslanou do zařízení:

```json
{ "Field1": 56, "Field2": "A string value" }
```

Následující fragment kódu ukazuje příklad datové části odpovědi odeslané ze zařízení. `200`K indikaci úspěchu použijte kód odpovědi http:

```json
{ "Field1": 87, "Field2": "Another string value" }
```

### <a name="asynchronous-command-types"></a>Typy asynchronních příkazů

Následující fragment kódu z DCM ukazuje definici asynchronního příkazu. Příkaz má celočíselný parametr a očekává, že zařízení vrátí celočíselnou hodnotu:

```json
{
  "@id": "<element id>",
  "@type": "Command",
  "commandType": "asynchronous",
  "durable": false,
  "request": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@id": "<element id>",
    "@type": "SchemaField",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "AsynchronousCommandSimple"
  },
  "name": "AsynchronousCommandSimple"
}
```

Zařízení obdrží jako datovou část požadavku celočíselnou hodnotu. Zařízení by mělo vracet prázdnou datovou část odpovědi s `202` kódem odpovědi HTTP a indikovat, že zařízení přijalo požadavek na asynchronní zpracování.

Až zařízení dokončí zpracování žádosti, měla by odeslat vlastnost IoT Central, která vypadá jako v následujícím příkladu. Název vlastnosti musí být stejný jako název příkazu:

```json
{
  "AsynchronousCommandSimple": {
    "value": 87
  }
}
```

## <a name="next-steps"></a>Další kroky

Jako vývojář zařízení teď, když jste se dozvěděli o šablonách zařízení, je v rámci navržených dalších kroků Přečtěte si téma [připojení k Azure IoT Central](./concepts-get-connected.md) , kde se dozvíte další informace o registraci zařízení v IoT Central a o tom, jak IoT Central zabezpečení připojení zařízení.
