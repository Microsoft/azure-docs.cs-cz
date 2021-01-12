---
title: Použití dat umístění v řešení Azure IoT Central
description: Naučte se používat data o poloze odesílaná ze zařízení připojeného k vaší IoT Central aplikaci. Vykreslovat data o poloze na mapě nebo vytvořit pravidla monitorování geografických zón.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c909fd1438488e3625f3674dd26f959cf6fad79f
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128005"
---
# <a name="use-location-data-in-an-azure-iot-central-solution"></a>Použití dat umístění v řešení Azure IoT Central

V tomto článku se dozvíte, jak používat data o poloze v aplikaci IoT Central. Zařízení připojené k IoT Central může odesílat data umístění jako datový proud telemetrie nebo použít vlastnost zařízení k nahlášení dat umístění.

Tvůrce řešení může použít data umístění k těmto akcím:

* Vykreslit nahlášené umístění na mapě
* Vykreslí objekt historie umístění telemetrie na mapu.
* Vytvoření pravidel monitorování geografických zón pro oznamování operátoru, když zařízení zadá nebo opustí určitou oblast.

## <a name="add-location-capabilities-to-a-device-template"></a>Přidání možností umístění do šablony zařízení

Následující snímek obrazovky ukazuje šablonu zařízení s příklady vlastností zařízení a typu telemetrie, které používají data umístění. Definice používají sémantický typ **umístění** a typ schématu **geografického umístění** :

:::image type="content" source="media/howto-use-location-data/location-device-template.png" alt-text="Snímek obrazovky znázorňující definici vlastnosti umístění v šabloně zařízení":::

Pro referenci odkazuje definice [DTDL (digitální vlákna definice jazyka)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) pro tyto funkce jako následující fragment kódu:

```json
{
  "@type": [
    "Property",
    "Location"
  ],
  "displayName": {
    "en": "DeviceLocation"
  },
  "name": "DeviceLocation",
  "schema": "geopoint",
  "writable": false
},
{
  "@type": [
    "Telemetry",
    "Location"
  ],
  "displayName": {
    "en": "Tracking"
  },
  "name": "Tracking",
  "schema": "geopoint"
}
```

> [!NOTE]
> Typ schématu **inbodový** není součástí [specifikace DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). IoT Central aktuálně podporuje typ schématu **geografického** **bodu** a sémantický typ umístění pro zpětnou kompatibilitu.

## <a name="send-location-data-from-a-device"></a>Odeslat data o poloze ze zařízení

Když zařízení pošle data pro vlastnost **DeviceLocation** zobrazenou v předchozí části, vypadá datová část jako následující fragment kódu JSON:

```json
{
  "DeviceLocation": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Když zařízení pošle data pro **sledovací** telemetrii zobrazená v předchozí části, vypadá datová část jako následující fragment kódu JSON:

```json
{
  "Tracking": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

## <a name="display-device-location"></a>Zobrazit umístění zařízení

Data o poloze můžete zobrazit na více místech aplikace IoT Central. Například u zobrazení přidružených k individuálnímu zařízení nebo řídicím panelům.

Když vytvoříte zobrazení pro zařízení, můžete se rozhodnout vykreslit umístění na mapě nebo zobrazit jednotlivé hodnoty:

:::image type="content" source="media/howto-use-location-data/location-views.png" alt-text="Snímek obrazovky znázorňující ukázkové zobrazení s daty o poloze":::

Můžete přidat dlaždice mapy na řídicí panel a vykreslit umístění jednoho nebo více zařízení. Když přidáte dlaždici mapy pro zobrazení telemetrie umístění, můžete umístění vykreslit za časové období. Na následujícím snímku obrazovky vidíte umístění hlášené simulovaným zařízením za posledních 30 minut:

:::image type="content" source="media/howto-use-location-data/location-dashboard.png" alt-text="Snímek obrazovky s ukázkovým řídicím panelem s daty o poloze":::

## <a name="create-a-geofencing-rule"></a>Vytvoření pravidla pro monitorování geografických zón

Pomocí telemetrie poloha můžete vytvořit pravidlo pro monitorování geografických zón, které vygeneruje výstrahu, když se zařízení přesune do obdélníkové oblasti nebo z ní. Následující snímek obrazovky ukazuje pravidlo, které používá čtyři podmínky k definování obdélníkové oblasti s použitím hodnot zeměpisné šířky a délky. Pravidlo vygeneruje e-mail, když se zařízení přesune do obdélníkové oblasti:

:::image type="content" source="media/howto-use-location-data/geofence-rule.png" alt-text="Snímek obrazovky zobrazující definici pravidla pro monitorování geografických zón":::

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak používat vlastnosti v aplikaci IoT Central v Azure, najdete v těchto tématech:

* [Datových částí](concepts-telemetry-properties-commands.md)
* [Vytvoření a připojení klientské aplikace k aplikaci Azure IoT Central](tutorial-connect-device.md)