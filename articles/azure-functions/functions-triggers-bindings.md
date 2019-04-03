---
title: Triggery a vazby ve službě Azure Functions
description: Zjistěte, jak používat triggery a vazby pro připojení vaši funkci Azure Functions k online události a cloudové služby.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 3865f748a9ca2fe09660d6454542d64f73a8e3c1
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "56736953"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Aktivace Azure Functions a vazby koncepty

V tomto článku se dozvíte základními koncepty okolo funkce triggery a vazby.

Aktivační události představují co způsobit funkce ke spuštění. Aktivační událost definuje, jak je vyvolána funkce a funkce musí mít přesně jeden trigger. Aktivační události mají související data, která je často jako datovou část funkce. 

Vazba funkce představuje způsob deklarativně jiný prostředek připojení k funkci. vazby může být připojen jako *vstupních vazeb*, *výstupních vazeb*, nebo obojí. Data z vazby je k dispozici funkce jako parametry.

Můžete kombinovat a párovat různých vazby tak, aby odpovídala vašim potřebám. Vazby jsou volitelné a funkce může mít jednu nebo více vstupní nebo výstupní vazbu.

Triggery a vazby umožňují vyhnout hardcoding přístup k jiným službám. Funkce přijímá data (například obsah zprávy fronty) v parametry funkce. Odeslat data (například k vytvoření zprávy fronty) pomocí návratová hodnota funkce. 

Vezměte v úvahu následující příklady je implementace různých funkcí.

| Příklad scénáře | Trigger | Vstupní vazby | Výstupní vazby |
|-------------|---------|---------------|----------------|
| Po přijetí nové zprávy fronty okno, které spustí funkci k zápisu do jiné fronty. | fronty<sup>*</sup> | *Žádný* | fronty<sup>*</sup> |
|Naplánovaná úloha načítá obsah objektu Blob úložiště a vytvoří nový dokument služby Cosmos DB. | Časovač | Blob Storage | Databáze Cosmos |
|Event Grid se používá k načtení obrázku z úložiště objektů Blob a dokumentu ze služby Cosmos DB k odesílání e-mailu. | Event Grid | Úložiště objektů BLOB a Cosmos DB | SendGrid |
| Webhook, který využívá Microsoft Graphu k aktualizaci Excelový list. | HTTP | *Žádný* | Microsoft Graph |

<sup>\*</sup> Představuje různé fronty

Tyto příklady nepoužívejte jako vyčerpávající, ale jsou k dispozici pro ilustraci, jak můžete použít aktivační události a vazby společně.

###  <a name="trigger-and-binding-definitions"></a>Definice aktivační události a vazby

Triggery a vazby jsou definovány odlišně v závislosti na vývojový přístup.

| Platforma | Triggery a vazby jsou nakonfigurované podle... |
|-------------|--------------------------------------------|
| C#Knihovna tříd | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Upravení metody a parametry s C# atributy |
| Všechny ostatní (včetně webu Azure portal) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Aktualizuje se [function.json](./functions-reference.md) ([schématu](http://json.schemastore.org/function)) |

Portál poskytuje uživatelské rozhraní pro tuto konfiguraci, ale můžete upravit přímo otevřením souboru **Rozšířený editor** k dispozici prostřednictvím **integrace** kartu vaší funkce.

Typ parametru v .NET, definuje datový typ pro vstupní data. Například použijte `string` vytvořit vazbu na text aktivační událost fronty, pole bajtů ke čtení jako binární a vlastní typ deserializovat do objektu.

Pro jazyky, které jsou dynamicky typované jako je JavaScript, použijte `dataType` vlastnost *function.json* souboru. Například chcete-li číst obsah požadavku HTTP v binárním formátu, nastavte `dataType` k `binary`:

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

Všechny aktivační události a vazby mít `direction` vlastnost [function.json](./functions-reference.md) souboru:

- Pro aktivační události je vždy směru `in`
- Používat vstupní a výstupní vazby `in` a `out`
- Některé vazby podporují speciální směr `inout`. Pokud používáte `inout`, pouze **Rozšířený editor** je k dispozici prostřednictvím **integrace** karta na portálu.

Při použití [atributy v knihovně tříd](functions-dotnet-class-library.md) konfigurace triggerů a vazeb, směr zadaný v konstruktoru atributu nebo odvozen od typu parametru.

## <a name="supported-bindings"></a>Podporované vazby

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Informace o tom, které jsou ve verzi preview vazby, nebo jsou schválené pro použití v produkčním prostředí, najdete v části [podporované jazyky](supported-languages.md).

## <a name="resources"></a>Zdroje a prostředky
- [Výrazy vazeb a vzory](./functions-bindings-expressions-patterns.md)
- [Používat návratovou hodnotu funkce Azure](./functions-bindings-return-value.md)
- [Postup při registraci vazbového výrazu](./functions-bindings-register.md)
- Testování:
  - [Strategie pro testování kódu ve službě Azure Functions](functions-test-a-function.md)
  - [Ruční spuštění funkce neaktivované protokolem HTTP](functions-manually-run-non-http.md)
- [Zpracování chyb vazeb](./functions-bindings-errors.md)

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Registrace rozšíření vazby Azure Functions](./functions-bindings-register.md)
