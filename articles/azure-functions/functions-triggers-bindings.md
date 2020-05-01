---
title: Aktivační události a vazby v Azure Functions
description: Naučte se používat triggery a vazby k připojení funkce Azure k online událostem a cloudovým službám.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79276501"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions triggery a koncepty vazeb

V tomto článku se seznámíte s koncepty a možnostmi triggerů funkcí a vazeb na nejvyšší úrovni.

Aktivační události jsou tím, co způsobí spuštění funkce. Aktivační událost definuje způsob volání funkce a funkce musí mít právě jednu aktivační událost. Aktivační události obsahují související data, která se často poskytují jako datová část funkce. 

Vazba na funkci je způsob deklarativního připojení jiného prostředku ke funkci; vazby mohou být propojeny jako *vstupní vazby*, *výstupní vazby*nebo obojí. Data z vazeb jsou k dispozici funkci jako parametry.

Můžete kombinovat a párovat různé vazby tak, aby odpovídaly vašim potřebám. Vazby jsou volitelné a funkce může mít jednu nebo více vstupních nebo výstupních vazeb.

Triggery a vazby umožňují vyhnout se zakódujeme přístupu k jiným službám. Vaše funkce přijímá data (například obsah zprávy fronty) v parametrech funkce. Vy posíláte data (například k vytvoření zprávy fronty) pomocí návratové hodnoty funkce. 

Vezměte v úvahu následující příklady, jak můžete implementovat různé funkce.

| Ukázkový scénář | Trigger | Vstupní vazba | Výstupní vazba |
|-------------|---------|---------------|----------------|
| Dojde k doručení nové zprávy fronty, která spustí funkci pro zápis do jiné fronty. | Provedených<sup>*</sup> | *Žádné* | Provedených<sup>*</sup> |
|Naplánovaná úloha načte Blob Storage obsah a vytvoří nový dokument Cosmos DB. | Časovač | Blob Storage | Databáze Cosmos |
|Event Grid slouží ke čtení obrázku z Blob Storage a dokumentu z Cosmos DB k odeslání e-mailu. | Event Grid | Blob Storage a Cosmos DB | SendGrid |
| Webhook, který používá Microsoft Graph k aktualizaci excelového listu. | HTTP | *Žádné* | Microsoft Graph |

<sup>\*</sup>Představuje různé fronty.

Tyto příklady nejsou určeny k vyčerpávajícímu, ale jsou k dispozici pro ilustraci, jak můžete použít triggery a vazby dohromady.

###  <a name="trigger-and-binding-definitions"></a>Triggery a definice vazeb

Triggery a vazby jsou definovány odlišně v závislosti na vývoji přístupu.

| Platforma | Aktivační události a vazby jsou konfigurovány pomocí... |
|-------------|--------------------------------------------|
| Knihovna tříd C# | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;upravení metody a parametry s atributy jazyka C# |
| Všichni ostatní (včetně Azure Portal) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;aktualizuje se [Function. JSON](./functions-reference.md) ([schéma](http://json.schemastore.org/function)). |

Portál poskytuje uživatelské rozhraní pro tuto konfiguraci, ale můžete ho upravovat přímo otevřením **pokročilého editoru** dostupného prostřednictvím karty **integrace** vaší funkce.

V rozhraní .NET typ parametru definuje datový typ pro vstupní data. Například použijte `string` k vytvoření vazby na text triggeru fronty, bajtové pole pro čtení jako binární a vlastní typ pro deserializaci objektu.

Pro jazyky, které jsou dynamicky typované, jako je například JavaScript `dataType` , použijte vlastnost v souboru *Function. JSON* . Například pro čtení obsahu požadavku HTTP v binárním formátu nastavte `dataType` na: `binary`

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Další možnosti pro `dataType` jsou `stream` a `string`.

## <a name="binding-direction"></a>Směr vazby

Všechny aktivační události a vazby mají `direction` vlastnost v souboru [Function. JSON](./functions-reference.md) :

- Pro aktivační události je směr vždy`in`
- Vstupní a výstupní vazby používají `in` a`out`
- Některé vazby podporují zvláštní směr `inout`. Pokud použijete `inout`, je k dispozici pouze **Rozšířený editor** prostřednictvím karty **integrace** na portálu.

Použijete-li [atributy v knihovně tříd](functions-dotnet-class-library.md) ke konfiguraci triggerů a vazeb, je směr poskytován v konstruktoru atributu nebo odvozen z typu parametru.

## <a name="supported-bindings"></a>Podporované vazby

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Informace o tom, které vazby jsou ve verzi Preview nebo které jsou schválené pro použití v produkčním prostředí, najdete v tématu [podporované jazyky](supported-languages.md).

## <a name="resources"></a>Zdroje a prostředky
- [Výrazy a vzory vazby](./functions-bindings-expressions-patterns.md)
- [Použití návratové hodnoty funkce Azure Functions](./functions-bindings-return-value.md)
- [Postup registrace výrazu vazby](./functions-bindings-register.md)
- Zkouší
  - [Strategie testování kódu ve službě Azure Functions](functions-test-a-function.md)
  - [Ruční spuštění funkce neaktivované protokolem HTTP](functions-manually-run-non-http.md)
- [Zpracování chyb vazeb](./functions-bindings-errors.md)

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Registrovat rozšíření vazby Azure Functions](./functions-bindings-register.md)
