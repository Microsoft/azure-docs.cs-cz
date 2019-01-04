---
title: Zpracování dat a uživatelem definovaných funkcí s Dvojčaty digitální Azure | Dokumentace Microsoftu
description: Přehled zpracování dat, procesy pro hledání shody a uživatelem definovaných funkcí s Dvojčaty digitální Azure.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: alinast
ms.openlocfilehash: 915c57033209ff982946163c408cf8557515e2f5
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53999196"
---
# <a name="data-processing-and-user-defined-functions"></a>Zpracování dat a uživatelsky definované funkce

Azure digitální dvojče nabízí pokročilé kapacita výpočetních operací. Vývojáři můžou definovat a spouštějte vlastní funkce příchozí telemetrická data zprávy k odeslání události do předdefinovaných koncových bodů.

## <a name="data-processing-flow"></a>Zpracování dat toku

Po zařízení odesílají telemetrická data do Azure digitální dvojče, vývojáři dokáže zpracovat data ve čtyřech fázích: *ověření*, *odpovídat*, *compute*, a *odeslání* .

![Azure tok digitální dvojče zpracování dat][1]

1. Fáze ověřit transformuje příchozí zpráva telemetrie pro běžně používané [objekt pro přenos dat](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5) formátu. Tato fáze také provede ověření zařízení a senzorů.
1. Fáze shoda najde odpovídající uživatelem definované funkce ke spuštění. Předdefinované procesy pro hledání shody najít uživatelem definované funkce na základě zařízení, senzorů a místo informace z příchozích telemetrické zprávy.
1. Výpočetní fáze spuštění uživatelem definované funkce, shoda v předchozí fáze. Tyto funkce může číst a aktualizovat počítané hodnoty na prostorový graf uzly a může vysílat vlastní oznámení.
1. Fáze odeslání směruje vlastní oznámení pro koncové body definované v grafu fáze výpočetní prostředky.

## <a name="data-processing-objects"></a>Zpracování dat objektů

Zpracování dat v Azure digitální dvojče spočívá v definování tři objekty: *procesy pro hledání shody*, *uživatelem definované funkce*, a *přiřazení rolí*.

![Azure digitální dvojče zpracování dat objektů][2]

<div id="matcher"></div>

### <a name="matchers"></a>Procesy pro hledání shody

Procesy pro hledání shody definují sadu podmínek, které vyhodnotit, jaké akce proběhla podle příchozích telemetrických dat ze senzorů. Podmínky k určení shody může obsahovat vlastnosti od senzor senzoru nadřazené zařízení a místa nadřazené senzoru. Podmínky jsou vyjádřeny jako porovnání proti [cestu JSON](http://jsonpath.com/) jak je uvedeno v tomto příkladu:

- Všechny senzory datový typ **teploty** reprezentovaný řídicí hodnotu řetězce `\"Temperature\"`
- S `01` v jejich portu
- Který patří do zařízení s rozšířenou vlastnost klíče **výrobce** nastavena na hodnotu řetězce uvozený uvozovacím znakem `\"GoodCorp\"`
- Které patří prostory typ určený v řetězci uvozený uvozovacím znakem `\"Venue\"`
- Které jsou potomky nadřazené **SpaceId** `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`

```JSON
{
  "SpaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Name": "My custom matcher",
  "Description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value GoodCorp and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"GoodCorp\"",
      "comparison": "Equals"
    },
    {
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - Cesty JSON jsou malá a velká písmena.
> - Datová část JSON je stejný jako datovou část, která je vrácena:
>   - `/sensors/{id}?includes=properties,types` senzoru.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes` pro zařízení nadřazené senzoru.
>   - `/spaces/{id}?includes=properties,types,location,timezone` nadřazené místo senzoru.
> - Porovnání jsou malá a velká písmena.

### <a name="user-defined-functions"></a>Uživatelem definované funkce

Uživatelem definované funkce je provést v izolovaném prostředí Azure digitální dvojče vlastní funkci. Uživatelem definované funkce mají přístup k nezpracované senzor telemetrické zprávy získá přijetí. Uživatelem definované funkce také mají přístup ke službě prostorový graf a dispečerem. Po registraci v grafu, předávaný uživatelem definované funkce (podrobné [nad](#matcher)) musí být vytvořený zadat při provádění funkce. Například když Azure digitální dvojče obdrží nová telemetrická data z daného senzor, odpovídající uživatelem definovanou funkci můžete vypočítat klouzavý průměr v posledních několika údajů snímačů přes.

Uživatelem definované funkce může být napsán v jazyce JavaScript. Pomocné metody interakci s grafem v prostředí pro spuštění uživatelem definované. Vývojáři mohou spouštět vlastní fragmenty kódu proti senzor telemetrické zprávy. Příklady obsahují:

- Nastavte senzor čtení přímo na objekt ze senzorů v grafu.
- Proveďte akci podle údajů různých snímačů přes v rámci místo v grafu.
- Vytvořte oznámení při splnění určitých podmínek příchozí senzoru čtení.
- Připojte grafu metadat do senzoru čtení před odesláním oznámení.

Další informace najdete v tématu [použití uživatelem definovaných funkcí](./how-to-user-defined-functions.md).

### <a name="role-assignment"></a>Přiřazení role

Uživatelem definované funkce akce jsou v souladu s Azure digitální dvojče [řízení přístupu na základě rolí](./security-role-based-access-control.md) k zabezpečení dat v rámci služby. Přiřazení rolí definovat, které uživatelem definované funkce mít správná oprávnění k interakci s prostorový graf a jeho entit. Uživatelem definované funkce může mít například schopnost a oprávnění k *vytvořit*, *čtení*, *aktualizace*, nebo *odstranit* dat grafu v rámci dané místo. Uživatelem definované funkce úroveň přístupu je zaškrtnuté políčko při uživatelem definovanou funkci vyzve k zadání dat grafu nebo před pokusy akci. Další informace najdete v tématu [řízení přístupu na základě rolí](./security-create-manage-role-assignments.md).

Je možné, předávaný k aktivaci uživatelem definovanou funkci, která nemá žádná přiřazení role. V takovém případě uživatelem definovanou funkci nemůže načíst žádná data z grafu.

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak směrování událostí a telemetrie zprávy k jiným službám Azure, [trasy události a zprávy](./concepts-events-routing.md).

- Další informace o tom, jak vytvořit procesy pro hledání shody, uživatelem definované funkce a přiřazení rolí, [Průvodce používáním uživatelem definované funkce](./how-to-user-defined-functions.md).

- Zkontrolujte [uživatelem definovanou funkci klientské knihovny referenční dokumentaci](./reference-user-defined-functions-client-library.md).

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png
