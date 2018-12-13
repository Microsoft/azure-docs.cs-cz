---
title: Jak používat službu Azure Table Storage a rozhraní Table API služby Azure Cosmos DB pomocí Ruby
description: Ukládejte si strukturovaná data v cloudu pomocí služby Azure Table Storage nebo rozhraní Table API služby Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: ruby
ms.topic: sample
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: 7ddf9c3ef848537cb68dce043bb22680439e9cd5
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877901"
---
# <a name="how-to-use-azure-table-storage-and-the-azure-cosmos-db-table-api-with-ruby"></a>Jak používat službu Azure Table Storage a rozhraní Table API služby Azure Cosmos DB pomocí Ruby
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

## <a name="overview"></a>Přehled
Tato příručka ukazuje, jak provádět běžné scénáře pomocí služby Azure Table Storage a rozhraní Table API služby Azure Cosmos DB. Ukázky jsou napsané v Ruby a využívají [klientskou knihovnu služby Azure Table Storage pro Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Popsané scénáře zahrnují **vytvoření a odstranění tabulky a vkládání a dotazování entit v tabulce**.

## <a name="create-an-azure-service-account"></a>Vytvoření účtu služby Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-account"></a>Vytvoření účtu služby Azure Cosmos DB
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="add-access-to-storage-or-azure-cosmos-db"></a>Přidání přístupu ke službě Storage nebo Azure Cosmos DB
Pokud chcete používat službu Azure Storage nebo Azure Cosmos DB, musíte stáhnout a použít balíček Azure pro Ruby, který obsahuje sadu knihoven usnadňujících práci a komunikujících se službami REST služby Table Storage.

### <a name="use-rubygems-to-obtain-the-package"></a>Získání balíčku pomocí RubyGems
1. Použijte rozhraní příkazového řádku, jako je **PowerShell** (Windows), **Terminál** (Mac) nebo **Bash** (Unix).
2. Zadáním příkazu **gem install azure-storage-table** do příkazového okna nainstalujte gem a závislosti.

### <a name="import-the-package"></a>Import balíčku
Pomocí oblíbeného textového editoru přidejte následující řádek na začátek souboru Ruby, ve kterém chcete službu Storage používat:

```ruby
require "azure/storage/table"
```

## <a name="add-an-azure-storage-connection"></a>Přidání připojení ke službě Azure Storage
Modul Azure Storage načte informace potřebné pro připojení k účtu služby Azure Storage z proměnných prostředí **AZURE_STORAGE_ACCOUNT** a **AZURE_STORAGE_ACCESS_KEY**. Pokud tyto proměnné prostředí nejsou nastavené, je potřeba zadat informace o účtu ještě před použitím objektu **Azure::Storage::Table::TableService** pomocí následujícího kódu:

```ruby
Azure.config.storage_account_name = "<your Azure Storage account>"
Azure.config.storage_access_key = "<your Azure Storage access key>"
```

Získání těchto hodnot z klasického účtu úložiště nebo účtu úložiště Resource Manageru na webu Azure Portal:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte do účtu služby Storage, který chcete použít.
3. V okně Nastavení na pravé straně klikněte na **Přístupové klíče**.
4. V okně Přístupové klíče, které se zobrazí, uvidíte přístupový klíč 1 a přístupový klíč 2. Můžete použít libovolný z nich.
5. Kliknutím na ikonu kopírování zkopírujte klíč do schránky.

## <a name="add-an-azure-cosmos-db-connection"></a>Přidání připojení ke službě Azure Cosmos DB
Pokud se chcete připojit ke službě Azure Cosmos DB, zkopírujte z webu Azure Portal primární připojovací řetězec a s jeho použitím vytvořte objekt **Client**. Objekt **Client** můžete předat při vytváření objektu **TableService**:

```ruby
common_client = Azure::Storage::Common::Client.create(storage_account_name:'myaccount', storage_access_key:'mykey', storage_table_host:'mycosmosdb_endpoint')
table_client = Azure::Storage::Table::TableService.new(client: common_client)
```

## <a name="create-a-table"></a>Vytvoření tabulky
Objekt **Azure::Storage::Table::TableService** umožňuje pracovat s tabulkami a entitami. Pokud chcete vytvořit tabulku, použijte metodu **create_table()**. Následující příklad vytvoří tabulku nebo vypíše chybu, pokud tabulka již existuje.

```ruby
azure_table_service = Azure::Storage::Table::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky
Pokud chcete přidat entitu, nejprve vytvořte objekt hash definující vlastnosti entity. Nezapomeňte, že pro každou entitu musíte zadat **PartitionKey** a **RowKey**. Jedná se o jedinečné identifikátory entit a jsou to hodnoty, které je možné dotazovat mnohem rychleji než ostatní vlastnosti. Azure Storage používá **PartitionKey** k automatické distribuci entit tabulky do mnoha uzlů úložiště. Entity se stejnou hodnotou **PartitionKey** se ukládají na stejném uzlu. **RowKey** je jedinečné ID entity v rámci oddílu, do kterého patří.

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>Aktualizace entity
Existující entitu můžete aktualizovat několika metodami:

* **update_entity():** Aktualizuje existující entitu tím, že ji nahradí.
* **merge_entity():** Aktualizuje existující entitu tím, že s ní sloučí nové hodnoty vlastností.
* **insert_or_merge_entity():** Aktualizuje existující entitu tím, že ji nahradí. Pokud žádná entita neexistuje, vloží se nová entita:
* **insert_or_replace_entity():** Aktualizuje existující entitu tím, že s ní sloučí nové hodnoty vlastností. Pokud žádná entita neexistuje, vloží se nová entita.

Následující příklad ukazuje aktualizaci entity pomocí metody **update_entity()**:

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

V případě metod **update_entity()** a **merge_entity()** platí, že pokud aktualizovaná entita neexistuje, operace aktualizace selže. Proto pokud chcete entitu uložit bez ohledu na to, jestli již existuje, měli byste místo toho použít metodu **insert_or_replace_entity()** nebo **insert_or_merge_entity()**.

## <a name="work-with-groups-of-entities"></a>Práce se skupinami entit
Někdy má smysl odeslat více operací společně v dávce, aby se zajistilo jejich atomické zpracování serverem. Uděláte to tak, že nejprve vytvoříte objekt **Batch** a pak použijete metodu **execute_batch()** pro objekt **TableService**. Následující příklad ukazuje odeslání dvou entit s RowKey 2 a 3 v dávce. Poznámka: Tento přístup funguje pouze pro entity se stejnou hodnotou PartitionKey.

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>Dotaz na entitu
Pokud chcete zadat dotaz na entitu v tabulce, použijte metodu **get_entity()** a předejte do ní název tabulky, **PartitionKey** a **RowKey**.

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>Dotaz na sadu entit
Pokud chcete zadat dotaz na sadu entit v tabulce, vytvořte objekt hash dotazu a použijte metodu **query_entities()**. Následující příklad ukazuje získání všech entit se stejnou hodnotou **PartitionKey**:

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> Pokud je sada výsledků dotazu příliš velká na to, aby ji vrátil jeden dotaz, vrátí se token pro pokračování, pomocí kterého můžete načíst další stránky.
>
>

## <a name="query-a-subset-of-entity-properties"></a>Dotaz na podmnožinu vlastností entity
Dotaz na tabulku dokáže z entity načíst pouze několik vlastností. Tato technika, označovaná jako projekce, snižuje šířku pásma a může zlepšit výkon dotazů, zejména u velkých entit. Použijte klauzuli select a předejte názvy vlastností, které chcete přenést do klienta.

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>Odstranění entity
Pokud chcete odstranit entitu, použijte metodu **delete_entity()**. Předejte do ní název tabulky, která entitu obsahuje, a PartitionKey a RowKey entity.

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>Odstranění tabulky
Pokud chcete odstranit tabulku, použijte metodu **delete_table()** a předejte do ní název tabulky, kterou chcete odstranit.

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>Další postup

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.
* [Středisko pro vývojáře Ruby](https://azure.microsoft.com/develop/ruby/)
* [Klientská knihovna služby Microsoft Azure Table Storage pro Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table) 

