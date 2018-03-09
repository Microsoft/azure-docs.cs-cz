---
title: "Jak používat Azure Table Storage a rozhraní API služby Azure Cosmos DB tabulky s Ruby | Microsoft Docs"
description: "Ukládejte si strukturovaná data v cloudu pomocí Azure Table Storage, úložiště dat typu NoSQL."
services: cosmos-db
documentationcenter: ruby
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 047cd9ff-17d3-4c15-9284-1b5cc61a3224
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 02/27/2018
ms.author: mimig
ms.openlocfilehash: 104d793826116462f71e4889386906256b2df8f8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-ruby"></a>Jak používat Azure Table Storage a rozhraní API služby Azure Cosmos DB tabulky s Ruby
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Přehled
Tento průvodce vám ukáže, jak provádět běžné scénáře s využitím služby Azure Table a rozhraní API služby Azure Cosmos DB tabulky. Ukázky jsou napsané v Ruby a použít [Azure Table Klientská knihovna pro úložiště pro Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Pokryté scénáře zahrnují **vytváření a odstraňování tabulek a vkládání a dotazování entity v tabulce**.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="add-access-to-storage-or-azure-cosmos-db"></a>Přidělte oprávnění úložišť nebo databázi Cosmos Azure
Pokud chcete použít Azure Storage nebo Azure Cosmos DB, musíte stáhnout a použít Ruby Azure balíček, který obsahuje sadu pohodlí knihoven, které komunikují se službami tabulky REST.

### <a name="use-rubygems-to-obtain-the-package"></a>Použití RubyGems získat balíček
1. Pomocí rozhraní příkazového řádku, jako například **prostředí PowerShell** (Windows), **Terminálové** (Mac), nebo **Bash** (Unix).
2. Typ **gem nainstalovat tabulku úložiště azure** v příkazovém okně instalace gem a závislostí.

### <a name="import-the-package"></a>Import balíčku
Použít svém oblíbeném textovém editoru, přidejte na začátek souboru Ruby, kde máte v úmyslu používat úložiště následující:

```ruby
require "azure/storage/table"
```

## <a name="add-an-azure-storage-connection"></a>Přidat připojení k Azure Storage
Modul Azure Storage čte proměnné prostředí **AZURE_STORAGE_ACCOUNT** a **AZURE_STORAGE_ACCESS_KEY** informace požadované pro připojení k účtu úložiště Azure. Pokud nejsou nastavené těchto proměnných prostředí, je nutné zadat informace o účtu před použitím **Azure::Storage::Table::TableService** následujícím kódem:

```ruby
Azure.config.storage_account_name = "<your Azure Storage account>"
Azure.config.storage_access_key = "<your Azure Storage access key>"
```

K získání těchto hodnot z klasický nebo účet správce prostředků úložiště na portálu Azure:

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. Přejděte na účet úložiště, který chcete použít.
3. V okně nastavení na pravé straně klikněte na tlačítko **přístupové klíče**.
4. V okně klíče přístup, který se zobrazí uvidíte přístupový klíč 1 a 2 přístupový klíč. Můžete použít kteroukoli z nich.
5. Kliknutím na ikonu kopírování do schránky zkopírujte klíč.

## <a name="add-an-azure-cosmos-db-connection"></a>Přidat připojení k databázi Azure Cosmos
Připojit k databázi Azure Cosmos, zkopírujte primární připojovací řetězec z portálu Azure a vytvořte **klienta** pomocí zkopírovaný připojovací řetězec. Můžete předat **klienta** objektu při vytváření **TableService** objektu:

```ruby
common_client = Azure::Storage::Common::Client.create(storage_account_name:'myaccount', storage_access_key:'mykey', storage_table_host:'mycosmosdb_endpoint')
table_client = Azure::Storage::Table::TableService.new(client: common_client)
```

## <a name="create-a-table"></a>Vytvoření tabulky
**Azure::Storage::Table::TableService** objekt vám umožňuje spolupracovat s tabulkami a entity. Chcete-li vytvořit tabulku, použijte **create_table()** metoda. Následující příklad vytvoří tabulku nebo vytiskne chybu, pokud existuje.

```ruby
azure_table_service = Azure::Storage::Table::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky
Chcete-li přidat entitu, nejprve vytvořte objekt algoritmu hash, která definuje vlastnosti vaší entity. Všimněte si, že pro každou entitu musíte zadat **PartitionKey** a **RowKey**. Jsou jedinečné identifikátory vaší entity a jsou hodnoty, které může být dotazován mnohem rychleji než vaše jiných vlastností. Azure Storage používá **PartitionKey** automaticky distribuovat entity v tabulce přes mnoho uzly úložiště. Entity se stejným **PartitionKey** jsou uložené na stejném uzlu. **RowKey** je jedinečný Identifikátor entity v oddílu patří do.

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>Aktualizace entity
Existuje několik metod, které jsou k dispozici pro aktualizace stávající entity:

* **update_entity():** aktualizace stávající entity nahrazením ho.
* **merge_entity():** aktualizace stávající entity sloučením nové hodnoty vlastností do stávající entity.
* **insert_or_merge_entity():** aktualizace stávající entity podle jeho nahrazení. Pokud žádná entita existuje, bude vložen nový:
* **insert_or_replace_entity():** aktualizace stávající entity sloučením nové hodnoty vlastností do stávající entity. Pokud existuje žádné entity, se vloží novou.

Následující příklad ukazuje, aktualizuje entitu s využitím **update_entity()**:

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

S **update_entity()** a **merge_entity()**, pokud typ entity, která jsou aktualizace neexistuje, operace aktualizace se nezdaří. Proto, pokud chcete uložit entity bez ohledu na to, jestli už existuje, měli byste místo toho použít **insert_or_replace_entity()** nebo **insert_or_merge_entity()**.

## <a name="work-with-groups-of-entities"></a>Práce se skupinami entit
Někdy má smysl odeslat více operací společně v dávce zajistit atomic zpracování serverem. To provést, je třeba nejprve vytvořit **Batch** objekt a potom pomocí **execute_batch()** metodu **TableService**. Následující příklad ukazuje, odesílání dvě entity s RowKey 2 a 3 v dávce. Všimněte si, že funguje pouze u entit s stejný klíč oddílu.

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>Dotaz pro entitu
Chcete-li prohledávat entitu v tabulce, použijte **get_entity()** metoda předáním názvu tabulky **PartitionKey** a **RowKey**.

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>Dotaz na sadu entit
Chcete-li prohledávat sady entit v tabulce, vytvoření objektu hash dotazu a použijte **query_entities()** metoda. Následující příklad ukazuje získání všech entit se stejným **PartitionKey**:

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> Pokud sadu výsledků dotazu je příliš velký pro jediný dotaz vrátit, vrátí se token pokračování, můžete použít k načtení další stránky.
>
>

## <a name="query-a-subset-of-entity-properties"></a>Dotaz na podmnožinu vlastností entity
Dotaz na tabulku můžete načíst jenom několik z entity. Tímto způsobem, nazývá "projekce," omezuje šířku pásma a může zlepšit výkon dotazů, hlavně pro velké entity. Použijte klauzuli select a předejte názvy vlastností, které chcete převést klientovi.

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>Odstranění entity
Pokud chcete odstranit entitu, použijte **delete_entity()** metoda. Předání názvu tabulky, která obsahuje entity, PartitionKey a RowKey entity.

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>Odstranění tabulky
Pokud chcete odstranit tabulku, použijte **delete_table()** metoda a předejte jí název tabulky, které chcete odstranit.

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>Další postup

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.
* [Středisko pro vývojáře Ruby](https://azure.microsoft.com/develop/ruby/)
* [Microsoft Azure Table Klientská knihovna pro úložiště pro Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table) 

