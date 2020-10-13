---
title: Použití vlastností v řešení Azure IoT Central
description: Naučte se používat vlastnosti jen pro čtení a zapisovatelné vlastnosti v řešení Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a750a98c27fd62288993b2203acc2032ccf39d71
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999754"
---
# <a name="use-properties-in-an-azure-iot-central-solution"></a>Použití vlastností v řešení Azure IoT Central

V tomto článku se dozvíte, jak používat vlastnosti zařízení, které jsou definované v šabloně zařízení v aplikaci Azure IoT Central.

Vlastnosti znázorňují hodnoty bodu v čase. Zařízení může například pomocí vlastnosti nahlásit cílovou teplotu, se kterou se snaží dosáhnout. Vlastnosti také umožňují synchronizovat stav mezi zařízením a aplikací Azure IoT Central. Můžete nastavit vlastnosti s možností zápisu z Azure IoT Central.

Můžete také definovat vlastnosti cloudu v aplikaci Azure IoT Central. Hodnoty vlastností cloudu se nikdy nevyměňují pomocí zařízení a nejsou v oboru pro tento článek.

## <a name="define-your-properties"></a>Definovat vlastnosti

Vlastnosti jsou datová pole, která představují stav vašeho zařízení. Použijte vlastnosti, které reprezentují trvalý stav zařízení, například stav zapnuto/vypnuto zařízení. Vlastnosti mohou také představovat základní vlastnosti zařízení, jako je například verze softwaru zařízení. Vlastnosti můžete deklarovat jako jen pro čtení nebo zapisovatelné.

Následující snímek obrazovky ukazuje definici vlastnosti v aplikaci Azure IoT Central.

![Snímek obrazovky, který zobrazuje definici vlastnosti v aplikaci Azure IoT Central.](./media/howto-use-properties/property-definition.png)

Následující tabulka ukazuje nastavení konfigurace pro schopnost vlastnosti.

| Pole           | Description                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Zobrazované jméno    | Zobrazovaný název hodnoty vlastnosti používané na řídicích panelech a formulářích.                                                                                                                                                              |
| Name            | Název vlastnosti Azure IoT Central vygeneruje hodnotu pro toto pole ze zobrazovaného názvu, v případě potřeby ale můžete zvolit vlastní hodnotu. Toto pole musí obsahovat alfanumerické znaky.                                                 |
| Typ schopnosti | Majetek.                                                                                                                                                                                                                          |
| Sémantický typ   | Sémantický typ vlastnosti, jako je například teplota, stav nebo událost. Volba sémantického typu Určuje, která z následujících polí je k dispozici.                                                                       |
| Schéma          | Datový typ vlastnosti, například Double, String nebo Vector. Dostupné možnosti určují sémantický typ. Schéma není k dispozici pro sémantické typy události a stavu.                                               |
| Writeable       | Pokud vlastnost není zapisovatelná, může zařízení nahlásit hodnoty vlastností do Azure IoT Central. Pokud je vlastnost zapisovatelná, může zařízení nahlásit hodnoty vlastností do Azure IoT Central. Pak může Azure IoT Central odeslat do zařízení aktualizace vlastností. |
| Závažnost        | K dispozici pouze pro sémantický typ události. Závažnosti jsou **chyby**, **informace**nebo **Upozornění**.                                                                                                                         |
| Hodnoty stavu    | K dispozici pouze pro sémantický typ State. Definujte hodnoty možných stavů, z nichž každá má zobrazované jméno, název, Výčtový typ a hodnotu.                                                                                   |
| Jednotka            | Jednotka pro hodnotu vlastnosti, například **mph**, **%** nebo ** &deg; C**.                                                                                                                                                              |
| Zobrazit jednotku    | Zobrazovací jednotka pro použití na řídicích panelech a formulářích.                                                                                                                                                                                    |
| Komentář         | Jakékoli komentáře k funkci vlastnosti.                                                                                                                                                                                        |
| Description     | Popis schopnosti vlastnosti.                                                                                                                                                                                          |

Vlastnosti lze také definovat v rozhraní v šabloně zařízení, jak je znázorněno zde:

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

Tento příklad ukazuje pět vlastností. Tyto vlastnosti mohou souviset s definicí vlastnosti v uživatelském rozhraní, jak je znázorněno zde:

* `@type` Chcete-li určit typ funkce: `Property`
* `name` pro hodnotu vlastnosti.
* `schema` Zadejte datový typ pro vlastnost. Tato hodnota může být primitivní typ, například Double, Integer, Boolean nebo String. Podporují se také komplexní typy objektů, pole a mapy.
* `writable` Ve výchozím nastavení jsou vlastnosti jen pro čtení. Pomocí tohoto pole můžete označit vlastnost jako zapisovatelnou.

Volitelná pole, jako je zobrazované jméno a popis, umožňují přidat další podrobnosti k rozhraní a funkcím.

Při vytváření vlastnosti můžete určit komplexní typy **schémat** , jako je například Object a Enum.

![Snímek obrazovky, který ukazuje, jak přidat schopnost](./media/howto-use-properties/property.png)

Když vyberete složité **schéma**, například **objekt**, je nutné definovat také objekt.

![Snímek obrazovky, který ukazuje, jak definovat objekt.](./media/howto-use-properties/object.png)

Následující kód ukazuje definici typu vlastnosti objektu. Tento objekt má dvě pole s typy String a Integer.

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

Ve výchozím nastavení jsou vlastnosti jen pro čtení. Vlastnosti jen pro čtení znamenají, že aktualizace hodnoty vlastnosti zařízení se aktualizuje do vaší aplikace Azure IoT Central. Vaše aplikace Azure IoT Central nemůže nastavit hodnotu vlastnosti jen pro čtení.

Azure IoT Central k synchronizaci hodnot vlastností mezi zařízením a aplikací Azure IoT Central používá vlákna zařízení. Hodnoty vlastností zařízení používají nedokončené hlášené vlastnosti zařízení. Další informace najdete v tématu [vlákna zařízení](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins).

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

Zařízení do Azure IoT Central odesílá vlastnosti jen pro čtení. Vlastnosti se odesílají jako datová část JSON. Další informace najdete v tématu [datové části](./concepts-telemetry-properties-commands.md).

K odeslání aktualizace vlastností do aplikace Azure IoT Central můžete použít sadu SDK pro zařízení Azure IoT.

Vlastnosti, které jsou v zařízení, se dají odeslat do aplikace Azure IoT Central pomocí této funkce:

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

Tento článek používá Node.js pro jednoduchost. Úplné informace o příkladech aplikací pro zařízení najdete v následujících kurzech:

* [Vytvoření a připojení klientské aplikace k aplikaci Azure IoT Central (Node.js)](tutorial-connect-device-nodejs.md)
* [Vytvoření a připojení klientské aplikace k aplikaci Azure IoT Central (Python)](tutorial-connect-device-python.md)

Následující zobrazení v aplikaci Azure IoT Central zobrazuje vlastnosti, které vidíte. Zobrazení automaticky nastaví vlastnost **model zařízení** na _vlastnost zařízení jen pro čtení_.

![Snímek obrazovky, který zobrazuje zobrazení vlastnosti jen pro čtení.](./media/howto-use-properties/read-only.png)

## <a name="implement-writable-properties"></a>Implementovat vlastnosti s možností zápisu

Vlastnosti s možností zápisu jsou nastaveny pomocí operátoru v aplikaci Azure IoT Central ve formuláři. Azure IoT Central odesílá vlastnost do zařízení. Azure IoT Central očekává potvrzení ze zařízení.

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

K definování a zpracování zapisovatelných vlastností, na které zařízení reaguje, můžete použít následující kód:

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

Zpráva odpovědi by měla zahrnovat `ac` pole a `av` . Pole `ad` je nepovinné. Příklady naleznete v následujících fragmentech kódu:

* `ac` je číselné pole, které používá hodnoty v následující tabulce.
* `av` je číslo verze odesílané do zařízení.
* `ad` je popis řetězce možností.

| Hodnota | Popisek | Description |
| ----- | ----- | ----------- |
| `'ac': 200` | Dokončeno | Operace změny vlastnosti byla úspěšně dokončena. |
| `'ac': 202` nebo `'ac': 201` | Čekající | Operace změny vlastnosti čeká na vyřízení nebo probíhá. |
| `'ac': 4xx` | Chyba | Požadovaná změna vlastnosti nebyla platná nebo došlo k chybě. |
| `'ac': 5xx` | Chyba | U zařízení došlo k neočekávané chybě při zpracování požadované změny. |


Další informace o nevlákenách zařízení najdete v tématu [Konfigurace zařízení z back-endové služby](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins).

Když operátor nastaví vlastnost s možností zápisu v aplikaci Azure IoT Central, aplikace použije pro odeslání hodnoty do zařízení hodnotu, která je typu vlákna. Zařízení pak odpoví pomocí vlastnosti nedokončené hlášené v zařízení. Když Azure IoT Central obdrží hodnotu hlášené vlastnosti, aktualizuje zobrazení vlastností se stavem **přijato**.

Následující zobrazení obsahuje vlastnosti, které lze zapisovat. Když zadáte hodnotu a vyberete **Uložit**, počáteční stav **čeká na vyřízení**. Když zařízení změnu přijme, stav se změní na **přijato**.

![Snímek obrazovky, který zobrazuje stav čeká na vyřízení.](./media/howto-use-properties/status-pending.png)

![Snímek obrazovky, který zobrazuje přijatou vlastnost](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak používat vlastnosti v aplikaci IoT Central v Azure, najdete v těchto tématech:

* [Datových částí](concepts-telemetry-properties-commands.md)
* [Vytvoření a připojení klientské aplikace k aplikaci Azure IoT Central (Node.js)](tutorial-connect-device-nodejs.md)
