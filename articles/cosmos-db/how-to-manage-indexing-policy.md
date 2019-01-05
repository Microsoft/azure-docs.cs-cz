---
title: Informace o správě účtů databáze ve službě Azure Cosmos DB
description: Informace o správě účtů databáze ve službě Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: c27cee4842c0e65e1737f100a215cff82a0fd439
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033090"
---
# <a name="manage-indexing-in-azure-cosmos-db"></a>Správa indexování ve službě Azure Cosmos DB

Ve službě Azure Cosmos DB můžete, zda chcete kontejner pro automaticky indexuje všechny položky, nebo ne. Ve výchozím nastavení jsou všechny položky v kontejneru Azure Cosmos automaticky indexovány, ale můžete vypnout automatické indexování. Když je vypnutý indexování, položky budou přístupné prostřednictvím jejich odkazů na sebe sama nebo prostřednictvím dotazů s použitím ID položky, jako je například id dokumentu. Můžete explicitně požádat o poskytovat výsledky bez použití indexu předáním **x-ms-documentdb-enable kontroly** záhlaví v rozhraní REST API nebo **EnableScanInQuery** vyžádejte možnost. NET SDK.

Pomocí automatické indexování, vypnutý, můžete stále selektivně přidat konkrétní položky do indexu. Naopak můžete nechat zapnuté automatické indexování, abyste si zvolit vyloučit konkrétní položky. Indexování zapnout nebo vypnout konfigurace jsou užitečné, když máte podmnožině položek, které je potřeba se měl posílat dotaz.  

Zápisy na jednotky propustnosti a požadavek se úměrný počtu hodnot, které je třeba indexovat, který je určený sady zahrnuté v zásady indexování. Pokud budete mít dostatečné povědomí o vzory dotazů, můžete explicitně zahrnout/vyloučit podmnožinu cesty ke zvýšení propustnosti zápisu.

## <a name="manage-indexing-using-azure-portal"></a>Správa indexování s využitím webu Azure portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vytvořit nový účet Azure Cosmos, nebo vyberte existující účet.

3. Otevřít **Průzkumník dat** podokně.

4. Vybrat existující kontejner, rozbalte ho a upravte následující hodnoty:

   * Otevřít **škálování a nastavení** okna.
   * Změna **indexingMode** z *konzistentní* k *žádný* nebo zahrnout nebo vyloučit určité cesty ke složkám indexování.
   * Klikněte na tlačítko **OK** a uložte změny.

   ![Správa indexování pomocí webu Azure portal](./media/how-to-manage-indexing/how-to-manage-indexing-portal.png)

## <a name="manage-indexing-using-azure-sdks"></a>Správa indexování s využitím sady Azure SDK

### <a id="dotnet"></a>.NET SDK

Následující příklad ukazuje, jak vložit položku explicitně pomocí [SQL SDK pro .NET API](sql-api-sdk-dotnet.md) a [RequestOptions.IndexingDirective](/dotnet/api/microsoft.azure.documents.client.requestoptions.indexingdirective) vlastnost.

```csharp
// To override the default behavior to exclude (or include) a document in indexing,
// use the RequestOptions.IndexingDirective property.
client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new { id = "myDocumentId", isRegistered = true },
    new RequestOptions { IndexingDirective = IndexingDirective.Include });
```

## <a name="next-steps"></a>Další postup

Další informace o indexování v následujících článcích:

* [Indexování – přehled](index-overview.md)
* [Zásady indexování](index-policy.md)
* [Index typy](index-types.md)
* [Index cesty](index-paths.md)
