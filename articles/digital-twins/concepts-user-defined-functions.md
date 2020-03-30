---
title: Zpracování dat a uživatelem definované funkce – Azure Digital Twins| Dokumenty společnosti Microsoft
description: Přehled zpracování dat, matchery a uživatelem definované funkce s Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 75ed2029582438ede43687addfd54c0a187e0120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265178"
---
# <a name="data-processing-and-user-defined-functions"></a>Zpracování dat a uživatelsky definované funkce

Azure Digital Twins nabízí pokročilé výpočetní funkce. Vývojáři mohou definovat a spouštět vlastní funkce proti příchozí telemetrické zprávy k odesílání událostí do předdefinovaných koncových bodů.

## <a name="data-processing-flow"></a>Tok zpracování dat

Poté, co zařízení odesílají telemetrická data do Azure Digital Twins, mohou vývojáři zpracovávat data ve čtyřech fázích: *ověření*, *shoda*, *výpočetní prostředky*a *odeslání*.

[![Tok zpracování dat Azure Digital Twins](media/concepts/digital-twins-data-processing-flow.png)](media/concepts/digital-twins-data-processing-flow.png#lightbox)

1. Fáze ověření transformuje příchozí telemetrickou zprávu na běžně srozumitelný formát [objektu přenosu dat.](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5) Tato fáze také provádí ověření zařízení a senzoru.
1. Fáze shody vyhledá příslušné uživatelem definované funkce, které mají být spuštěny. Předdefinované matchery naleznou uživatelem definované funkce založené na informacích o zařízení, senzoru a prostoru z příchozí telemetrické zprávy.
1. Výpočetní fáze spustí uživatelem definované funkce odpovídající v předchozí fázi. Tyto funkce může číst a aktualizovat vypočítané hodnoty na uzlech prostorového grafu a může vyzařovat vlastní oznámení.
1. Fáze odeslání směruje všechna vlastní oznámení z fáze výpočtu do koncových bodů definovaných v grafu.

## <a name="data-processing-objects"></a>Objekty zpracování dat

Zpracování dat v Azure Digital Twins se skládá z definování tří objektů: *matchers*, *uživatelem definované funkce*a *přiřazení rolí*.

[![Objekty pro zpracování dat Azure Digital Twins](media/concepts/digital-twins-user-defined-functions.png)](media/concepts/digital-twins-user-defined-functions.png#lightbox)

### <a name="matchers"></a>Matchers

Matchers definovat sadu podmínek, které vyhodnocují, jaké akce se uskuteční na základě příchozí ho senzoru telemetrie. Podmínky pro určení shody mohou zahrnovat vlastnosti ze senzoru, nadřazeného zařízení senzoru a nadřazeného prostoru senzoru. Podmínky jsou vyjádřeny jako porovnání s [cestou JSON,](https://jsonpath.com/) jak je uvedeno v tomto příkladu:

- Všechny senzory datového typu **Teplota** reprezentovaná uvozenou hodnotou String`\"Temperature\"`
- Mít `01` ve svém přístavu
- Které patří do zařízení s rozšířeným klíčem vlastnosti **Výrobce** nastavenna na hodnotu uvozený řetězec`\"Contoso\"`
- Které patří do mezer typu určeného uvozeným řetězcem`\"Venue\"`
- Které jsou potomky nadřazené **SpaceId**`DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`

```JSON
{
  "id": "23535afafd-f39b-46c0-9b0c-0dd3892a1c30",
  "name": "My custom matcher",
  "spaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value Contoso and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "conditions": [
    {
      "id": "43898sg43-e15a-4e9c-abb8-2gw464364",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "id": "wt3th44-e15a-35sg-seg3-235wf3ga463",
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "id": "735hs33-e15a-37jj-23532-db901d550af5",
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"Contoso\"",
      "comparison": "Equals"
    },
    {
      "id": "222325-e15a-49fg-5744-463643644",
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - Cesty JSON rozlišují malá a velká písmena.
> - Datová část JSON je stejná jako datová část, která je vrácena:
>   - `/sensors/{id}?includes=properties,types`pro snímač.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes`pro nadřazené zařízení senzoru.
>   - `/spaces/{id}?includes=properties,types,location,timezone`pro nadřazený prostor senzoru.
> - Porovnání jsou malá a velká písmena.

### <a name="user-defined-functions"></a>Uživatelsky definované funkce

Uživatelem definovaná funkce je vlastní funkce spuštěná v izolovaném prostředí Azure Digital Twins. Uživatelem definované funkce mají přístup k nezpracovaná telemetrická zpráva senzoru, jakmile je přijata. Uživatelem definované funkce mají také přístup ke službě prostorového grafu a dispečera. Po registraci uživatelem definované funkce v grafu musí být vytvořen matcher (podrobně [popsaný výše),](#matchers)který určí, kdy je funkce spuštěna. Například když Azure Digital Twins obdrží novou telemetrickou z daného senzoru, odpovídající uživatelem definovaná funkce můžete vypočítat klouzavý průměr z posledních několika údajů senzoru.

Uživatelem definované funkce lze zapsat do JavaScriptu. Pomocné metody interagují s grafem v prostředí spuštění definované uživatelem. Vývojáři mohou spouštět vlastní fragmenty kódu proti telemetrickým zprávám senzoru. Příklady obsahují:

- Nastavte údaje senzoru přímo na objekt senzoru v grafu.
- Proveďte akci založenou na různých odečtech senzorů v rámci prostoru v grafu.
- Vytvořte oznámení, pokud jsou splněny určité podmínky pro příchozí údaje senzoru.
- Před odesláním oznámení připojte metadata grafu ke čtení senzoru.

Další informace naleznete v článku [Použití uživatelem definovaných funkcí](./how-to-user-defined-functions.md).

#### <a name="examples"></a>Příklady

Úložiště [GitHub pro ukázku Digital Twins C#](https://github.com/Azure-Samples/digital-twins-samples-csharp/) obsahuje několik příkladů uživatelem definovaných funkcí:
- [Tato funkce](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) hledá hodnoty oxidu uhličitého, pohybu a teploty, aby určila, zda je místnost s těmito hodnotami v rozsahu k dispozici. [Kurzy pro digitální dvojčata](tutorial-facilities-udf.md) prozkoumat tuto funkci podrobněji. 
- [Tato funkce](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js) hledá data z více snímačů pohybu a určuje, že prostor je k dispozici, pokud žádný z nich nedetekuje žádný pohyb. Uživatelem definovanou funkci použitou v [rychlém startu](quickstart-view-occupancy-dotnet.md)nebo [v kurzech](tutorial-facilities-setup.md)můžete snadno nahradit provedením změn uvedených v části komentáře souboru. 

### <a name="role-assignment"></a>Přiřazení role

Akce uživatelem definované funkce podléhají [řízení přístupu na základě rolí](./security-role-based-access-control.md) Azure Digital Twins pro zabezpečená data v rámci služby. Přiřazení rolí definují, které uživatelem definované funkce mají správná oprávnění k interakci s prostorovým grafem a jeho entitami. Uživatelem definovaná funkce může mít například možnost a oprávnění k *vytváření*, *čtení*, *aktualizaci*nebo *odstraňování* dat grafu v daném prostoru. Úroveň přístupu definované uživatelem je kontrolována, když uživatelem definovaná funkce požádá graf o data nebo se pokusí o akci. Další informace naleznete v části [Řízení přístupu na základě rolí](./security-create-manage-role-assignments.md).

Je možné, že matcher spustí uživatelem definovanou funkci, která nemá žádná přiřazení rolí. V tomto případě uživatelem definovaná funkce nečte žádná data z grafu.

## <a name="next-steps"></a>Další kroky

- Další informace o směrování událostí a telemetrických zpráv do jiných služeb Azure najdete v článek [Trasy událostí a zpráv](./concepts-events-routing.md).

- Další informace o vytváření matcherů, uživatelem definovaných funkcí a přiřazení rolí naleznete [v příručce pro používání uživatelem definovaných funkcí](./how-to-user-defined-functions.md).

- Projděte si [uživatelsky definovanou dokumentaci ke knihovně klienta s funkcí](./reference-user-defined-functions-client-library.md).
