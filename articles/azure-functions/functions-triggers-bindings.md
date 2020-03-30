---
title: Aktivační události a vazby ve funkcích Azure
description: Naučte se používat aktivační události a vazby k připojení funkce Azure k online událostem a cloudovým službám.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276501"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions spouští a vázací koncepty

V tomto článku se dozvíte koncepty na vysoké úrovni okolní funkce aktivační události a vazby.

Aktivační události jsou příčinou spuštění funkce. Aktivační událost definuje, jak je funkce vyvolána a funkce musí mít přesně jednu aktivační událost. Aktivační události obsahují související data, která se často poskytují jako datová část funkce. 

Vazba na funkci je způsob deklarativně připojení jiného prostředku k funkci; vazby mohou být připojeny jako *vstupní vazby*, *výstupní vazby*nebo obojí. Data z vazeb jsou k dispozici funkci jako parametry.

Můžete kombinovat a párovat různé vazby tak, aby odpovídaly vašim potřebám. Vazby jsou volitelné a funkce může mít jednu nebo více vstupních nebo výstupních vazeb.

Aktivační události a vazby umožňují vyhnout se pevnému kódování přístup u jiných služeb. Vaše funkce přijímá data (například obsah zprávy fronty) v parametrech funkce. Vy posíláte data (například k vytvoření zprávy fronty) pomocí návratové hodnoty funkce. 

Zvažte následující příklady, jak můžete implementovat různé funkce.

| Příklad scénáře | Trigger | Vstupní vazba | Výstupní vazba |
|-------------|---------|---------------|----------------|
| Přijde nová zpráva fronty, která spustí funkci pro zápis do jiné fronty. | Fronty<sup>*</sup> | *Žádné* | Fronty<sup>*</sup> |
|Naplánovaná úloha přečte obsah úložiště objektů blob a vytvoří nový dokument Cosmos DB. | Časovač | Blob Storage | Cosmos DB |
|Event Grid se používá ke čtení obrázku z úložiště objektů Blob a dokumentu z Cosmos DB k odeslání e-mailu. | Event Grid | Úložiště objektů blob a databáze Cosmos | SendGrid |
| Webový hák, který používá microsoft graph k aktualizaci listu aplikace Excel. | HTTP | *Žádné* | Microsoft Graph |

<sup>\*</sup>Představuje různé fronty.

Tyto příklady nejsou určeny k vyčerpávající, ale jsou k dispozici pro ilustraci, jak můžete použít aktivační události a vazby společně.

###  <a name="trigger-and-binding-definitions"></a>Definice aktivačních událostí a vazeb

Aktivační události a vazby jsou definovány odlišně v závislosti na vývoji přístupu.

| Platforma | Aktivační události a vazby jsou konfigurovány... |
|-------------|--------------------------------------------|
| Knihovna tříd C# | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;metody zdobení a parametry s c# atributy |
| Všechny ostatní (včetně portálu Azure) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;aktualizace [function.json](./functions-reference.md) ([schéma](http://json.schemastore.org/function)) |

Portál poskytuje ui pro tuto konfiguraci, ale můžete upravit soubor přímo otevřením **rozšířeného editoru** k dispozici prostřednictvím **integrovat** kartu vaší funkce.

V rozhraní .NET definuje typ parametru datový typ pro vstupní data. Například slouží `string` k vazbě na text fronty aktivační události, bajtové pole číst jako binární a vlastní typ de-serializovat na objekt.

Pro jazyky, které jsou dynamicky zadávány, například JavaScript, použijte `dataType` vlastnost v souboru *function.json.* Chcete-li například číst obsah požadavku HTTP v `dataType` `binary`binárním formátu, nastavte na :

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Další možnosti `stream` `string`pro `dataType` jsou a .

## <a name="binding-direction"></a>Směr vazby

Všechny aktivační události a `direction` vazby mají vlastnost v souboru [function.json:](./functions-reference.md)

- U spouštěčů je směr vždy`in`
- Vstupní a výstupní `in` vazby používají a`out`
- Některá vázání podporují `inout`zvláštní směr . Pokud používáte `inout`, pouze **rozšířený editor** je k dispozici prostřednictvím **integrovat** kartu na portálu.

Při použití [atributů v knihovně tříd](functions-dotnet-class-library.md) ke konfiguraci aktivačních událostí a vazeb, směr je k dispozici v konstruktoru atributu nebo odvozenz typu parametru.

## <a name="supported-bindings"></a>Podporovaná vazby

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Informace o tom, které vazby jsou ve verzi preview nebo jsou schváleny pro produkční použití, naleznete v [tématu Podporované jazyky](supported-languages.md).

## <a name="resources"></a>Prostředky
- [Vazby výrazy a vzory](./functions-bindings-expressions-patterns.md)
- [Použití vrácené hodnoty funkce Azure](./functions-bindings-return-value.md)
- [Jak zaregistrovat výraz vazby](./functions-bindings-register.md)
- Testování:
  - [Strategie testování kódu ve službě Azure Functions](functions-test-a-function.md)
  - [Ruční spuštění funkce neaktivované protokolem HTTP](functions-manually-run-non-http.md)
- [Zpracování chyb vazby](./functions-bindings-errors.md)

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Registrace rozšíření vazby Azure Functions](./functions-bindings-register.md)
