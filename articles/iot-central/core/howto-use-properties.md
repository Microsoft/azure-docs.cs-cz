---
title: Jak používat vlastnosti v řešení Azure IoT Central
description: Jak používat vlastnosti jen pro čtení a zapisovatelné vlastnosti v řešení Azure IoT Central
author: v-krghan
ms.author: v-krghan
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: fa9b07d80c34ec26ca920fe147ada8f8ef7f2fd7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342567"
---
# <a name="how-to-use-properties-in-an-azure-iot-central-solution"></a>Jak používat vlastnosti v řešení Azure IoT Central

V tomto článku se dozvíte, jak používat vlastnosti zařízení, které jsou definované v šabloně zařízení v aplikaci Azure IoT Central.

Vlastnosti znázorňují hodnoty bodu v čase. Zařízení může například pomocí vlastnosti nahlásit cílovou teplotu, se kterou se snaží dosáhnout. Vlastnosti také umožňují synchronizovat stav mezi zařízením a vaší IoT Centralou aplikací.  Můžete nastavit vlastnosti s možností zápisu z IoT Central.

V IoT Central aplikaci můžete také definovat vlastnosti cloudu. Hodnoty vlastností cloudu se nikdy nevyměňují pomocí zařízení a nejsou v oboru pro tento článek.

## <a name="define-your-properties"></a>Definovat vlastnosti

Vlastnosti jsou datová pole, která představují stav vašeho zařízení. Použijte vlastnosti, které reprezentují trvalý stav zařízení, například stav Zapnuto pro zařízení. Vlastnosti mohou také představovat základní vlastnosti zařízení, jako je například verze softwaru zařízení. Vlastnosti můžete deklarovat jako jen pro čtení nebo zapisovatelné.

Následující snímek obrazovky ukazuje definici vlastnosti v aplikaci Azure IoT Central.

![Definovat vlastnost](./media/howto-use-properties/property-definition.png)

Následující tabulka ukazuje nastavení konfigurace pro schopnost vlastnosti:

| Pole           | Popis                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Zobrazovaný název    | Zobrazovaný název hodnoty vlastnosti používané na řídicích panelech a formulářích.                                                                                                                                                              |
| Name            | Název vlastnosti IoT Central vygeneruje hodnotu pro toto pole ze zobrazovaného názvu, ale v případě potřeby můžete zvolit vlastní hodnotu. Toto pole musí být alfanumerické.                                                 |
| Typ funkce | Majetek.                                                                                                                                                                                                                          |
| Sémantický typ   | Sémantický typ vlastnosti, jako je například teplota, stav nebo událost. Volba sémantického typu Určuje, která z následujících polí je k dispozici.                                                                       |
| Schéma          | Datový typ vlastnosti, například Double, String nebo Vector. Dostupné možnosti určují sémantický typ. Schéma není k dispozici pro sémantické typy události a stavu.                                               |
| Writeable       | Pokud vlastnost není zapisovatelná, může zařízení nahlásit hodnoty vlastností IoT Central. Pokud je vlastnost zapisovatelná, může zařízení nahlásit hodnoty vlastností IoT Central a IoT Central může odesílat aktualizace vlastností do zařízení. |
| Závažnost        | K dispozici pouze pro sémantický typ události. Závažnosti jsou **chyby**, **informace**nebo **Upozornění**.                                                                                                                         |
| Hodnoty stavu    | K dispozici pouze pro sémantický typ State. Definujte hodnoty možných stavů, z nichž každá má zobrazované jméno, název, Výčtový typ a hodnotu.                                                                                   |
| Jednotka            | Jednotka pro hodnotu vlastnosti, například **mph**, **%** nebo ** &deg; C**.                                                                                                                                                              |
| Zobrazit jednotku    | Zobrazovací jednotka pro použití na řídicích panelech a formulářích.                                                                                                                                                                                    |
| Komentář         | Jakékoli komentáře k funkci vlastnosti.                                                                                                                                                                                        |
| Popis     | Popis schopnosti vlastnosti.                                                                                                                                                                                          |

Vlastnosti lze také definovat v rozhraní v šabloně zařízení, jak je uvedeno níže:

``` json
{
  "@type": "Property",
  "displayName": "Device State",
  "description": "The state of the device. Two states online/offline are available.",
  "name": "state",
  "schema": "boolean"
},
{
  "@type": "Property",
  "displayName": "Customer Name",
  "description": "The name of the customer currently operating the device.",
  "name": "name",
  "schema": "string",
  "writable": true
},
{
 "@type": "Property",
 "displayName": "Date ",
 "description": "The date on which the device is currently operating",
 "name": "date",
 "writable": true,
 "schema": "date"
},
{ 
 "@type": "Property",
 "displayName": "Location",
 "description": "The current location of the device",
 "name": "location",
 "writable": true,
 "schema": "geopoint"
},
{
 "@type": "Property",
 "displayName": "Vector Level",
 "description": "The Vector level of the device",
 "name": "vector",
 "writable": true,
 "schema": "vector"
}
```

Tento příklad ukazuje pět vlastností, které mohou souviset s definicí vlastnosti v uživatelském rozhraní, jak je uvedeno níže:

* `@type` Chcete-li určit typ funkce: `Property`
* `name` pro hodnotu vlastnosti.
* `schema` Zadejte datový typ pro vlastnost. Tato hodnota může být primitivní typ, například Double, Integer, Boolean nebo String. Podporují se také komplexní typy objektů, pole a mapy.
* `writable` Ve výchozím nastavení jsou vlastnosti jen pro čtení. Můžete označit vlastnost jako zapisovatelnou pomocí tohoto pole.

Volitelná pole, jako je zobrazované jméno a popis, umožňují přidat další podrobnosti k rozhraní a funkcím.

Při vytváření vlastnosti můžete určit komplexní typy **schémat** , jako je například Object, enum atd.

![Přidat funkci](./media/howto-use-properties/property.png)

Při výběru komplexního schématu, jako je například **objekt**, je nutné definovat také objekt.

![Definovat objekt](./media/howto-use-properties/object.png)

Následující kód ukazuje definici typu vlastnosti objektu. Tento objekt má dvě pole s typy řetězec a celé číslo:

``` json
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

## <a name="implement-read-only-properties"></a>Implementovat vlastnosti jen pro čtení

Ve výchozím nastavení jsou vlastnosti jen pro čtení. Vlastnosti jen pro čtení znamenají, že se hodnota vlastnosti zařízení hlásí jako aktualizace vaší aplikace IoT Central. Vaše aplikace IoT Central nemůže nastavit hodnotu vlastnosti jen pro čtení.

IoT Central používá vlákna zařízení k synchronizaci hodnot vlastností mezi zařízením a aplikací IoT Central. Hodnoty vlastností zařízení používají nedokončené hlášené vlastnosti zařízení. Další informace najdete v tématu [vlákna zařízení](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins) .

Následující fragment kódu z modelu schopností zařízení zobrazuje definici typu vlastnosti jen pro čtení:

``` json
{
  "@type": "Property",
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

Zařízení odesílá vlastnosti jen pro čtení, aby je bylo možné IoT Central. Vlastnosti jsou odesílány jako datová část JSON. Další informace naleznete v části [datové části](./concepts-telemetry-properties-commands.md).

K odeslání aktualizace vlastností do aplikace IoT Central můžete použít sadu SDK pro zařízení Azure IoT.

Vlastnosti, které jsou v zařízení, se dají odeslat do aplikace Azure IoT Central pomocí níže uvedené funkce:

``` javascript
hubClient.getTwin((err, twin) => {
    const properties = {
        model: 'environmentalSensor1.0'
    };
    twin.properties.reported.update(properties, (err) => {
        console.log(err ? `error: ${err.toString()}` : `status: success` )
    });
});
```

Tento článek používá Node.js pro jednoduchost. úplné informace o příkladech aplikací pro zařízení najdete v tématu věnovaném [Vytvoření a připojení klientské aplikace do vaší aplikace azure IoT Central (Node.js)](tutorial-connect-device-nodejs.md) a [Vytvoření a připojení klientské aplikace k vašim kurzům pro Azure IoT Central Application (Python)](tutorial-connect-device-python.md) .

Následující zobrazení v aplikaci Azure IoT Central zobrazuje vlastnosti, můžete zobrazit, že zobrazení se automaticky změní na vlastnost model zařízení _jen pro čtení_.

![Zobrazení vlastnosti jen pro čtení](./media/howto-use-properties/read-only.png)

## <a name="implement-writable-properties"></a>Implementovat vlastnosti s možností zápisu

Vlastnosti s možností zápisu jsou nastaveny pomocí operátoru v aplikaci IoT Central ve formuláři. IoT Central odešle vlastnost do zařízení. IoT Central očekává potvrzení ze zařízení. 

Následující fragment kódu z modelu schopností zařízení zobrazuje definici typu vlastnosti s možností zápisu:

``` json
{
  "@type": "Property",
  "displayName": "Brightness Level",
  "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
  "name": "brightness",
  "writable": true,
  "schema": "long"
}
```

Klient zařízení by měl poslat datovou část JSON, která vypadá jako v následujícím příkladu, jako hlášená vlastnost v zařízení s dvojitou vlastností:

``` json
{ "Brightness Level": 2 }
```

K definování a zpracování zapisovatelných vlastností, na které zařízení reaguje, můžete použít následující kód.

``` javascript
hubClient.getTwin((err, twin) => {
    twin.on('properties.desired.brightness', function(desiredBrightness) {
        console.log( `Received setting: ${desiredBrightness.value}` );
        var patch = {
            brightness: {
                value: desiredBrightness.value,
                ad: 'success',
                ac: 200,
                av: desiredBrightness.$version
            }
        }
        twin.properties.reported.update(patch, (err) => {
            console.log(err ? `error: ${err.toString()}` : `status: success` )
        });
    });
});
```

Zpráva odpovědi by měla zahrnovat `ac` pole a `av` . Pole `ad` je nepovinné. Příklady najdete v následujících fragmentech kódu.

* `ac` je číselné pole, které používá hodnoty v následující tabulce:

* `av` je číslo verze odesílané do zařízení.

* `ad` je popis řetězce možností.

| Hodnota | Štítek | Popis |
| ----- | ----- | ----------- |
| `'ac': 200` | Dokončeno | Operace změny vlastnosti byla úspěšně dokončena. |
| `'ac': 202`  ani `'ac': 201` | Čekající | Operace změny vlastnosti čeká na vyřízení nebo probíhá. |
| `'ac': 4xx` | Chyba | Požadovaná změna vlastnosti nebyla platná nebo došlo k chybě. |
| `'ac': 5xx` | Chyba | U zařízení došlo k neočekávané chybě při zpracování požadované změny. |


Další informace najdete v tématu [vlákna zařízení](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins).

Když operátor nastaví vlastnost s možností zápisu v aplikaci IoT Central, aplikace použije pro odeslání hodnoty do zařízení hodnotu, která je typu vlákna. Zařízení pak odpoví pomocí vlastnosti nedokončené hlášené v zařízení. Když IoT Central obdrží hodnotu hlášené vlastnosti, aktualizuje zobrazení vlastností se stavem **přijato**.

Následující zobrazení obsahuje vlastnosti, které lze zapisovat. Když zadáte hodnotu a **uložíte**, počáteční stav **čeká na vyřízení**. když zařízení změnu přijme, stav se změní na **přijato**.

![Stav čeká na vyřízení](./media/howto-use-properties/status-pending.png)

![Přijatá vlastnost](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak používat vlastnosti v aplikaci IoT Central v Azure, můžete zobrazit [datové části](concepts-telemetry-properties-commands.md) a [vytvořit a připojit klientskou aplikaci k vaší aplikaci azure IoT Central (Node.js)](tutorial-connect-device-nodejs.md).
