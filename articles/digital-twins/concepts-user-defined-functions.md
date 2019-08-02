---
title: Zpracování dat a uživatelsky definované funkce pomocí digitálních vláken Azure | Microsoft Docs
description: Přehled zpracování dat, odpovídajících uživatelů a uživatelsky definovaných funkcí s využitím digitálních vláken Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: alinast
ms.openlocfilehash: f4aa7e6660e3febdca6e0e5b1ad9f11bebaa48ea
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638459"
---
# <a name="data-processing-and-user-defined-functions"></a>Zpracování dat a uživatelsky definované funkce

Digitální vlákna Azure nabízí pokročilé výpočetní funkce. Vývojáři mohou definovat a spouštět vlastní funkce pro příchozí zprávy telemetrie pro odesílání událostí do předdefinovaných koncových bodů.

## <a name="data-processing-flow"></a>Tok zpracování dat

Jakmile zařízení odešlou data telemetrie do digitálních vláken Azure, můžou vývojáři zpracovávat data ve čtyřech fázích: *ověřování*, *Shoda*, *výpočty*a *odeslání*.

![Tok zpracování dat digitálních vláken Azure][1]

1. Fáze Validate transformuje příchozí zprávu telemetrie na běžně srozumitelný formát [objektu pro přenos dat](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5) . Tato fáze také provádí ověřování zařízení a senzorů.
1. Fáze porovnávání vyhledá příslušné uživatelsky definované funkce, které se mají spustit. Předdefinované shody hledají uživatelsky definované funkce na základě informací o zařízení, snímači a prostoru z příchozí zprávy telemetrie.
1. Výpočetní fáze spouští uživatelsky definované funkce, které odpovídají v předchozí fázi. Tyto funkce mohou číst a aktualizovat počítané hodnoty na uzlech prostorového grafu a mohou generovat vlastní oznámení.
1. Fáze odeslání směruje všechna vlastní oznámení z výpočetní fáze do koncových bodů definovaných v grafu.

## <a name="data-processing-objects"></a>Objekty zpracování dat

Zpracování dat v digitálních proobjektech Azure se skládá z definování tří objektů: *shody*, *uživatelsky definovaných funkcí*a *přiřazení rolí*.

![Objekty pro zpracování dat z digitálního vlákna Azure][2]

<div id="matcher"></div>

### <a name="matchers"></a>Párování

Koshodě definují sadu podmínek, které vyhodnocují, jaké akce probíhají na základě telemetrie pro příchozí senzory. Podmínky pro určení shody mohou zahrnovat vlastnosti ze senzoru, nadřazeného zařízení snímače a nadřazeného prostoru snímače. Podmínky jsou vyjádřeny jako porovnání s [cestou JSON](https://jsonpath.com/) , jak je uvedeno v tomto příkladu:

- Všechny snímače **teploty** datového typu reprezentované řídicí hodnotou řetězce`\"Temperature\"`
- Má `01` na svém portu
- Které patří do zařízení s rozšířeným **výrobcem** klíče vlastnosti nastaveným na hodnotu řetězce s řídicími znaky`\"GoodCorp\"`
- Který patří do mezer typu určeného řídicím řetězcem`\"Venue\"`
- Které jsou následníky nadřazeného **SpaceId**`DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`

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
> - U cest JSON se rozlišují velká a malá písmena.
> - Datová část JSON je shodná s datovou částí, kterou vrací:
>   - `/sensors/{id}?includes=properties,types`pro senzor.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes`pro nadřazené zařízení snímače.
>   - `/spaces/{id}?includes=properties,types,location,timezone`pro nadřazený prostor senzoru.
> - Při porovnávání se nerozlišují malá a velká písmena.

### <a name="user-defined-functions"></a>Uživatelsky definované funkce

Uživatelsky definovaná funkce je vlastní funkce spuštěná v izolovaném prostředí Azure Digital revláken. Uživatelsky definované funkce mají přístup ke zprávě telemetrie nezpracovaného senzoru, jak se obdrží. Uživatelsky definované funkce také mají přístup ke službě prostorového grafu a dispečera. Po zaregistrování uživatelsky definované funkce v grafu je nutné vytvořit shodu (podrobná [výše](#matcher)) pro určení, kdy je funkce spuštěna. Například když digitální vlákna Azure obdrží novou telemetrii od daného senzoru, odpovídající uživatelsky definovaná funkce může vypočítat klouzavý průměr za posledních několik čtených senzorů.

Uživatelsky definované funkce lze zapsat v JavaScriptu. Pomocné metody komunikují s grafem v uživatelsky definovaném spouštěcím prostředí. Vývojáři můžou spouštět vlastní fragmenty kódu pro zprávy telemetrie senzorů. Příklady obsahují:

- Nastavte senzor pro čtení přímo do objektu snímače v grafu.
- Provede akci založenou na různých čteních senzorů v rámci prostoru v grafu.
- Vytvoří oznámení v případě, že jsou splněny určité podmínky pro čtení příchozího senzoru.
- Než odešlete oznámení, připojte metadata grafu ke senzoru, který čte.

Další informace najdete v tématu [Jak používat uživatelsky definované funkce](./how-to-user-defined-functions.md).


#### <a name="examples"></a>Příklady

[Úložiště GitHub pro C# ukázku digitálních vláken](https://github.com/Azure-Samples/digital-twins-samples-csharp/) obsahuje několik příkladů uživatelsky definovaných funkcí:
- [Tato funkce](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) vyhledá oxid uhličitý, pohyb a teplotní hodnoty a určí, zda je místnost k dispozici s těmito hodnotami v rozsahu. [Kurzy pro digitální vlákna](tutorial-facilities-udf.md) probírají tuto funkci podrobněji. 
- [Tato funkce](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js) vyhledá data z více snímačů pohybu a určí, zda je prostor k dispozici, pokud žádný z nich nedetekuje žádné pohyby. Můžete snadno nahradit uživatelsky definovanou funkci použitou v rychlém startu [](quickstart-view-occupancy-dotnet.md)nebo v kurzech [](tutorial-facilities-setup.md)tím, že provedete změny uvedené v části komentáře v souboru. 



### <a name="role-assignment"></a>Přiřazení role

Akce uživatelsky definované funkce se řídí [řízením přístupu na základě role](./security-role-based-access-control.md) v rámci služby Azure Digital probíhají k zabezpečení dat ve službě. Přiřazení rolí definují, které uživatelsky definované funkce mají správná oprávnění k interakci s prostorovým grafem a jeho entitami. Například uživatelsky definovaná funkce může mít možnost a oprávnění k *vytváření*, *čtení*, *aktualizaci*nebo *odstraňování* dat grafu v daném prostoru. Úroveň přístupu uživatelsky definované funkce je kontrolována, když uživatelsky definovaná funkce požádá graf o data nebo se pokusí o akci. Další informace najdete v tématu [řízení přístupu na základě role](./security-create-manage-role-assignments.md).

Ke spuštění uživatelsky definované funkce, která nemá žádné přiřazení rolí, je možné, aby se shodovala. V takovém případě funkce definovaná uživatelem nenačte žádná data z grafu.

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak směrovat události a zprávy telemetrie do jiných služeb Azure, najdete v tématu [události a zprávy směrování](./concepts-events-routing.md).

- Pokud chcete získat další informace o tom, jak vytvořit párování, uživatelsky definované funkce a přiřazení rolí, přečtěte si [příručku k používání uživatelsky definovaných funkcí](./how-to-user-defined-functions.md).

- Přečtěte si [referenční dokumentaci k klientské knihovně funkcí definovaných uživatelem](./reference-user-defined-functions-client-library.md).

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png
