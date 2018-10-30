---
title: Zpracování dat a uživatelem definovaných funkcí s Dvojčaty digitální Azure | Dokumentace Microsoftu
description: Přehled zpracování dat, procesy pro hledání shody a uživatelem definovaných funkcí s Dvojčaty digitální Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: a45f82b142ee4f4c9c88ea755607b88323feaae5
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50210121"
---
# <a name="data-processing-and-user-defined-functions"></a>Zpracování dat a uživatelsky definované funkce

Azure digitální dvojče nabízí pokročilé kapacita výpočetních operací. Vývojáři můžou definovat a spouštějte vlastní funkce příchozí telemetrická data zprávy k odeslání události do předdefinovaných koncových bodů.

## <a name="data-processing-flow"></a>Zpracování dat toku

Jakmile zařízení odesílají telemetrická data do digitální dvojče, vývojáři dokáže zpracovat data ve čtyřech fázích: _ověření_, _odpovídat_, _compute_, a _odeslání_:

![Tok digitální dvojče zpracování dat][1]

1. _Ověření_ fáze transformuje příchozí zpráva telemetrie pro běžně používané [ **objekt pro přenos dat** ](https://en.wikipedia.org/wiki/Data_transfer_object) formátu. Tato fáze také provede ověření zařízení a senzorů.
1. _Odpovídat_ fáze najde odpovídající uživatelsky definovaná funkce ke spuštění. Předdefinované procesy pro hledání shody zjistí, že uživatelsky definovaná funkce na základě zařízení, senzorů a místo informace z příchozích telemetrické zprávy.
1. _Compute_ fáze spuštění uživatelem definované funkce, shoda v předchozí fáze. Tyto funkce může číst a aktualizovat počítané hodnoty na prostorový graf uzly a může generovat vlastní oznámení.
1. _Odeslání_ fáze směruje vlastní oznámení pro koncové body definované v grafu fáze výpočetní prostředky.

## <a name="data-processing-objects"></a>Zpracování dat objektů

Zpracování dat v Azure digitální dvojče spočívá v definování tři objekty: _procesy pro hledání shody_, _uživatelem definované funkce_, a _přiřazení rolí_:

![Tok digitální dvojče zpracování dat][2]

### <a name="matchers"></a>Procesy pro hledání shody

_Procesy pro hledání shody_ definují sadu podmínek, které vyhodnotit, jaké akce bude probíhat podle příchozích telemetrických dat ze senzorů. Tyto podmínky k určení shody může obsahovat vlastnosti od senzor senzoru nadřazené zařízení a místa nadřazené senzoru. Podmínky jsou vyjádřeny jako porovnání proti [cestu JSON](http://jsonpath.com/) jak je uvedeno v následujícím příkladu:

- Všechny senzory datový typ **teploty**.
- S `01` v jejich portu.
- Který patří do zařízení s rozšířenou vlastnost klíče **výrobce** nastaven na hodnotu `"GoodCorp"`.
- Které patří prostory typu `"Venue"`.
- Které jsou potomky nadřazené **SpaceId** `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`.

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
> - Datová část JSON je stejný jako datovou část, která by vrátila:
>   - `/sensors/{id}?includes=properties,types` senzoru.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes` pro zařízení nadřazené senzoru.
>   - `/spaces/{id}?includes=properties,types,location,timezone` nadřazené místo senzoru.
> - Porovnání jsou malá a velká písmena.

### <a name="user-defined-functions"></a>Uživatelem definované funkce

A _uživatelem definovanou funkci_, nebo _UDF_, je vlastní funkce, který běží v izolovaném prostředí v Azure digitální dvojče. UDF mít přístup ke i nezpracované senzor telemetrické zprávy, jako byla přijata, stejně jako službu prostorový graf a dispečerem. Jakmile se v grafu není zaregistrována UDF, musí být vytvořený předávaný (podrobně popsané výše) určete, kdy ke spuštění UDF. Když digitální dvojče obdrží nová telemetrická data z daného senzor, odpovídající UDF můžete vypočítat klouzavý průměr poslední několik odečty snímačů, třeba.

Uživatelem definovanými funkcemi je možné psát v jazyce JavaScript a vývojářům umožňuje spustit vlastní fragmenty kódu proti senzor telemetrické zprávy. Existují také pomocné metody pro interakci s grafem v prostředí pro spuštění uživatelem definované. S uživatelem definovanou FUNKCI mohou vývojáři:

- Nastavte senzor čtení přímo na objekt ze senzorů v grafu.
- Proveďte akci podle údajů různých snímačů přes v rámci místo v grafu.
- Vytvořte oznámení při splnění určitých podmínek příchozí senzoru čtení.
- Připojte grafu metadat do senzoru čtení před odesláním oznámení.

Odkazovat na [jak funkce User User-Defined](how-to-user-defined-functions.md) další podrobnosti.

### <a name="role-assignment"></a>Přiřazení role

Akce UDF jsou v souladu s digitální dvojče řízení přístupu na základě rolí k zabezpečení dat v rámci služby. Přiřazení rolí zkontrolujte, zda daný UDF má správná oprávnění k interakci s prostorový graf. Například uživatelem definovanou FUNKCI se může pokusit vytvářet, číst, aktualizovat nebo odstranit data grafu v rámci dané místo. Úroveň přístupu UDF proběhne při UDF vyzve k zadání dat grafu nebo před pokusy akci. Další informace najdete v tématu [řízení přístupu na základě Role](security-create-manage-role-assignments.md).

Je možné, předávaný ke spuštění UDF, který nemá žádná přiřazení role. UDF by v tomto případě nelze načíst žádná data z grafu.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak směrování událostí a telemetrie zprávy k jiným službám Azure, [směrování události a zprávy](concepts-events-routing.md).

Další informace o tom, jak vytvořit procesy pro hledání shody, uživatelem definované funkce a přiřazení rolí, [Průvodce používáním uživatelem definované funkce](how-to-user-defined-functions.md).

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png
