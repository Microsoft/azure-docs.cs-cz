---
title: Ukládání nestrukturovaných dat pomocí Azure Cosmos DB a funkcí
description: Ukládání nestrukturovaných dat pomocí Azure Functions a databáze Cosmos DB
ms.topic: quickstart
ms.date: 10/01/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 5bc3895cb219338acde492b871dce806db70622b
ms.sourcegitcommit: 487a9f5272300d60df2622c3d13e794d54680f90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91661155"
---
# <a name="store-unstructured-data-using-azure-functions-and-azure-cosmos-db"></a>Ukládání nestrukturovaných dat pomocí služeb Azure Functions a Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) nabízí skvělou možnost pro ukládání nestrukturovaných dat a dat JSON. Spolu s Azure Functions urychluje a zjednodušuje ukládání dat – ve srovnání s ukládáním dat v relační databáze budete potřebovat méně kódování.

> [!NOTE]
> V současnosti aktivační události Azure Cosmos DB a vstupní a výstupní vazby fungují jenom s účty SQL API a Graph API.

Ve službě Azure Functions poskytují vstupní a výstupní vazby deklarativní způsob připojení k datům externí služby z funkce. V tomto článku se dozvíte, jak aktualizovat stávající funkci a přidat výstupní vazbu, která ukládá nestrukturovaná data v dokumentu Azure Cosmos DB.

## <a name="prerequisites"></a>Požadované součásti

Pro absolvování tohoto kurzu potřebujete:

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

## <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB

Před vytvořením výstupní vazby potřebujete účet služby Azure Cosmos DB, který používá rozhraní SQL API.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-an-output-binding"></a>Přidání výstupní vazby

1. V Azure Portal přejděte na a vyberte aplikaci funkcí, kterou jste vytvořili dříve.

1. Vyberte **funkce**a pak vyberte funkci HttpTrigger.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-select-http-function.png" alt-text="V Azure Portal vyberte svou funkci http." border="true":::

1. Vyberte **integrace** a **+ Přidat výstup**.

     :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-add-output-binding.png" alt-text="V Azure Portal vyberte svou funkci http." border="true":::

1. Použijte nastavení **vytvořit výstup** , jak je uvedeno v tabulce:

     :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-configure-cosmosdb-binding.png" alt-text="V Azure Portal vyberte svou funkci http." border="true":::

    | Nastavení      | Navrhovaná hodnota  | Popis                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Typ vazby** | Azure Cosmos DB | Název typu vazby, který chcete vybrat k vytvoření výstupní vazby pro Azure Cosmos DB. |
    | **Název parametru dokumentu** | taskDocument | Název, který odkazuje na objekt Cosmos DB v kódu. |
    | **Název databáze** | taskDatabase | Název databáze pro uložení dokumentů. |
    | **Název kolekce** | Kolekci taskcollection | Název kolekce databáze. |
    | **Je-li nastavená hodnota true, vytvoří se databáze a kolekce Cosmos DB** | Yes | Kolekce ještě neexistuje, takže ji vytvořte. |
    | **Připojení účtu databáze Cosmos DB** | Nové nastavení | Vyberte **Nový**, pak vyberte **Azure Cosmos DB účet** a **databázový účet** , který jste vytvořili dříve, a pak vyberte **OK**. Vytvoří nastavení aplikace pro připojení k vašemu účtu. Toto nastavení vazba použije k připojení k databázi. |

1. Vyberte **OK** a vytvořte vazbu.

## <a name="update-the-function-code"></a>Aktualizace kódu funkce

Nahraďte stávající kód funkce následujícím kódem ve zvoleném jazyce:

# <a name="c"></a>[C#](#tab/csharp)

Nahraďte stávající funkci v C# následujícím kódem:

```csharp
#r "Newtonsoft.Json"

using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, out object taskDocument, ILogger log)
{
    string name = req.Query["name"];
    string task = req.Query["task"];
    string duedate = req.Query["duedate"];

    // We need both name and task parameters.
    if (!string.IsNullOrEmpty(name) && !string.IsNullOrEmpty(task))
    {
        taskDocument = new
        {
            name,
            duedate,
            task
        };

        return (ActionResult)new OkResult();
    }
    else
    {
        taskDocument = null;
        return (ActionResult)new BadRequestResult();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Nahraďte stávající funkci v JavaScriptu následujícím kódem:

```js
module.exports = async function (context, req) {

    // We need both name and task parameters.
    if (req.query.name && req.query.task) {

        // Set the output binding data from the query object.
        context.bindings.taskDocument = req.query;

        // Success.
        context.res = {
            status: 200
        };
    }
    else {
        context.res = {
            status: 400,
            body: "The query options 'name' and 'task' are required."
        };
    }
};
```
---

Tento vzorový kód přečte řetězce dotazů požadavků HTTP a přiřadí je do polí v objektu `taskDocument`. Vazba `taskDocument` odešle data objektu z tohoto parametru vazby k uložení ve vázané databázi dokumentů. Tato databáze se vytvoří při prvním spuštění funkce.

## <a name="test-the-function-and-database"></a>Testování funkce a databáze

1. Vyberte **test/spustit**. V části **dotaz**vyberte **+ Přidat parametr** a do řetězce dotazu přidejte následující parametry:

    + `name`
    + `task`
    + `duedate`

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function.png" alt-text="V Azure Portal vyberte svou funkci http." border="true":::


1. Vyberte **Spustit** a ověřte, že je vrácen stav 200.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function-output.png" alt-text="V Azure Portal vyberte svou funkci http." border="true":::


1. V Azure Portal vyhledejte a vyberte **Azure Cosmos DB**.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-search-cosmos-db.png" alt-text="V Azure Portal vyberte svou funkci http." border="true":::

1. Zvolte účet Azure Cosmos DB a pak vyberte  **Průzkumník dat**.

1. Rozbalte uzly **úlohacollection** , vyberte nový dokument a potvrďte, že dokument obsahuje vaše hodnoty řetězce dotazu spolu s dalšími metadaty.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-data-explorer-check-document.png" alt-text="V Azure Portal vyberte svou funkci http." border="true":::

Úspěšně jste přidali vazbu na váš trigger HTTP, který ukládá nestrukturovaná data ve službě Azure Cosmos DB.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky

Další informace o vazbách na databázi Cosmos DB najdete v tématu [Vazby Cosmos DB ve službě Azure Functions](functions-bindings-cosmosdb.md).

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps-2.md)]
