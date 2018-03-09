---
title: "Začínáme s Azure Table storage pomocí Python | Microsoft Docs"
description: "Ukládejte si strukturovaná data v cloudu pomocí Azure Table Storage, úložiště dat typu NoSQL."
services: cosmos-db
documentationcenter: python
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: 7ddb9f3e-4e6d-4103-96e6-f0351d69a17b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/08/2018
ms.author: mimig
ms.openlocfilehash: 455479c9eb77093dd5611263fe5bdcf699b9d026
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="get-started-with-azure-table-storage-using-python"></a>Začínáme s Azure Table storage pomocí Python

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Azure Table Storage je služba, která ukládá strukturovaná data NoSQL do cloudu a poskytuje úložiště klíčů/atributů s návrhem bez použití schématu. Vzhledem k tomu, že je Table Storage bez schématu, je snadné data přizpůsobovat měnícím se potřebám vaší aplikace. Přístup k datům Table Storage je pro mnoho typů aplikací rychlý a nákladově efektivní a pro podobné objemy dat obvykle znamená nižší náklady než tradiční SQL.

Table Storage můžete používat k ukládání flexibilních datových sad, například uživatelských dat pro webové aplikace, adresářů, informací o zařízení nebo dalších typů metadat, které vaše služba vyžaduje. V tabulce můžete uložit libovolný počet entit a účet úložiště může obsahovat libovolný počet tabulek, až do limitu kapacity účtu úložiště.

### <a name="about-this-tutorial"></a>O tomto kurzu
V tomto kurzu se dozvíte, jak používat [Azure Cosmos DB tabulky SDK pro jazyk Python](https://pypi.python.org/pypi/azure-cosmosdb-table/) v běžné scénáře Azure Table storage. Název sady SDK označuje je pro použití s Azure Cosmos DB, ale funguje s obou Azure DB Cosmos a úložiště tabulek Azure, každá služba právě má jedinečný koncový bod. Tyto scénáře jsou prozkoumali Python příklady, které ilustrují použití postup:
* Vytváření a odstraňování tabulek
* Vložení a dotaz entity
* Upravit entity

Při práci prostřednictvím scénáře v tomto kurzu, můžete chtít odkazovat [Azure Cosmos DB SDK pro Python API – referenční informace](https://azure.github.io/azure-cosmosdb-python/).

## <a name="prerequisites"></a>Požadavky

Pro úspěšné absolvování tohoto kurzu potřebujete následující položky:

- [Python](https://www.python.org/downloads/) 2.7, 3.3, 3.4, 3.5 nebo 3.6
- [Tabulky Azure Cosmos DB sady SDK pro jazyk Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Tato sada SDK se připojí pomocí Azure Table storage a rozhraní API služby Azure DB Cosmos tabulky.
- [Účet služby Azure Storage](https://docs.microsoft.com/en-us/azure/storage/common/storage-create-storage-account#create-a-storage-account) nebo [účet Azure Cosmos DB](https://azure.microsoft.com/en-us/try/cosmosdb/)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="create-an-azure-service-account"></a>Vytvoření účtu služby Azure

Můžete pracovat s tabulkami pomocí Azure Table storage nebo Azure Cosmos DB. Další informace o rozdílech mezi službami načtením [tabulky nabídky](table-introduction.md#table-offerings). Budete muset vytvořit účet služby, že se chystáte použít. 

### <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure
Nejjednodušší způsob, jak vytvořit první účet úložiště Azure, je pomocí [webu Azure Portal](https://portal.azure.com). Další informace najdete v tématu [Vytvoření účtu úložiště](../storage/common/storage-create-storage-account.md#create-a-storage-account).

Účet úložiště Azure můžete vytvořit také pomocí [prostředí Azure PowerShell](../storage/common/storage-powershell-guide-full.md) nebo [rozhraní příkazového řádku Azure](../storage/common/storage-azure-cli.md).

Pokud teď nechcete vytvářet účet úložiště, můžete také pomocí emulátoru úložiště Azure spustit a otestovat kód v místním prostředí. Další informace najdete v článku [Použití emulátoru úložiště Azure pro vývoj a testování](../storage/common/storage-use-emulator.md).

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Vytvoření účtu Azure Cosmos DB tabulky rozhraní API

Pokyny k vytvoření účtu Azure Cosmos DB tabulky API najdete v tématu [vytvořit účet rozhraní API tabulky](create-table-dotnet.md#create-a-database-account).

## <a name="install-the-azure-cosmos-db-table-sdk-for-python"></a>Nainstalovat Azure Cosmos Databázové tabulce SDK pro Python

Po vytvoření účtu úložiště, je dalším krokem k instalaci [Microsoft Azure DB Cosmos tabulky SDK pro jazyk Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Podrobnosti o instalaci sady SDK najdete [README.rst](https://github.com/Azure/azure-cosmosdb-python/blob/master/azure-cosmosdb-table/README.rst) souborů v sadě SDK tabulky DB Cosmos Python úložiště na Githubu.

## <a name="import-the-tableservice-and-entity-classes"></a>Importovat třídy TableService a entit

Chcete-li pracovat s entity ve službě Azure Table v Python, použijte [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) a [Entity] [ py_Entity] třídy. Přidejte tento kód v horní Python soubor k importu obě:

```python
from azure.cosmosdb.table.tableservice import TableService
from azure.cosmosdb.table.models import Entity
```

## <a name="connect-to-azure-table-service"></a>Připojení ke službě Azure Table

Pro připojení ke službě Azure Storage Table, vytvoření [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) objektu a předat klíč účet a název účtu úložiště. Nahraďte `myaccount` a `mykey` s názvem účtu a klíč.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')
```

## <a name="connect-to-azure-cosmos-db"></a>Připojení ke službě Azure Cosmos DB

Připojit k databázi Azure Cosmos, zkopírujte primární připojovací řetězec z portálu Azure a vytvořte [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) pomocí zkopírovaný připojovací řetězec:

```python
table_service = TableService(connection_string='DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;TableEndpoint=myendpoint;)
```

## <a name="create-a-table"></a>Vytvoření tabulky

Volání [create_table] [ py_create_table] pro vytvoření tabulky.

```python
table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky

Pokud chcete přidat entitu, nejprve vytvořit objekt, který představuje vaše entitu a předat objekt, který má [TableService.insert_entity metoda][py_TableService]. Objekt entity může být slovník nebo objekt typu [Entity][py_Entity]a definuje vlastnost názvy a hodnoty vaší entity. Každou entitu musíte zahrnout požadované [PartitionKey a RowKey](#partitionkey-and-rowkey) vlastnosti kromě všechny vlastnosti, které definujete pro entitu.

Tento příklad vytvoří objekt Slovník reprezentující entitu, potom předává jej do [insert_entity] [ py_insert_entity] metody přidat do tabulky:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the trash', 'priority' : 200}
table_service.insert_entity('tasktable', task)
```

Tento příklad vytvoří [Entity] [ py_Entity] objekt a potom předává jej do [insert_entity] [ py_insert_entity] metody přidat do tabulky:

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>PartitionKey a RowKey

Musíte zadat oba **PartitionKey** a **RowKey** vlastnosti pro každou entitu. Toto jsou jedinečné identifikátory entity, jako společně tvoří primární klíč entity. Pomocí těchto hodnot mnohem rychlejší než dalších vlastností entity můžete dotazovat, protože pouze tyto vlastnosti jsou indexované se můžete dotazovat.

Použití služby Table **PartitionKey** inteligentně entity tabulky distribuovat mezi uzly úložiště. Entity, které mají stejnou **PartitionKey** jsou uložené na stejném uzlu. **RowKey** je jedinečný Identifikátor entity v oddílu patří do.

## <a name="update-an-entity"></a>Aktualizace entity

Chcete-li aktualizovat všechny hodnoty vlastností entity, zavolejte [update_entity] [ py_update_entity] metoda. Tento příklad ukazuje, jak nahradit stávající entity aktualizovaná verze:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage', 'priority' : 250}
table_service.update_entity('tasktable', task)
```

Pokud ještě neexistuje typ entity, která se právě aktualizuje, se nezdaří operace aktualizace. Pokud chcete uložit entity toho, zda existuje nebo Ne, použít [insert_or_replace_entity][py_insert_or_replace_entity]. V následujícím příkladu první volání nahradí stávající entity. Druhé volání bude vložit novou entitu, protože žádná entita s zadaný klíč oddílu a RowKey existuje v tabulce.

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage again', 'priority' : 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003', 'description' : 'Buy detergent', 'priority' : 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> [Update_entity] [ py_update_entity] metoda nahrazuje všechny vlastnosti a hodnoty existující entity, který můžete také použít k odebrání vlastnosti z existující entity. Můžete použít [merge_entity] [ py_merge_entity] metodu za účelem aktualizace stávající entity s hodnotami vlastností nové nebo upravené bez úplného nahrazení entity.

## <a name="modify-multiple-entities"></a>Úprava více entit

Aby atomic zpracování požadavku ve službě Table, můžete odeslat více operací společně v dávce. Nejprve pomocí [TableBatch] [ py_TableBatch] třídu a přidejte k jedné dávkové operace více. Pak zavolejte [TableService][py_TableService].[ commit_batch] [ py_commit_batch] k odeslání operace v atomické operace. Všechny entity má být změněn ve službě batch musí být ve stejném oddílu.

Tento příklad přidá dvě entity společně v dávce:

```python
from azure.cosmosdb.table.tablebatch import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004', 'description' : 'Go grocery shopping', 'priority' : 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005', 'description' : 'Clean the bathroom', 'priority' : 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

Dávky můžete použít taky s syntaxe kontextu správce:

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006', 'description' : 'Go grocery shopping', 'priority' : 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007', 'description' : 'Clean the bathroom', 'priority' : 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>Dotaz pro entitu

Dotaz pro entitu v tabulce, předat jeho PartitionKey a RowKey k [TableService][py_TableService].[ get_entity] [ py_get_entity] metoda.

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Dotaz na sadu entit

Můžete zadat dotaz pro sadu entit zadáním řetězec filtru s **filtru** parametr. Tento příklad vyhledá všechny úkoly v Praze použitím filtru na PartitionKey:

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>Dotaz na podmnožinu vlastností entity

Můžete taky omezit vlastnosti, které se vrátí pro každou entitu v dotazu. Tento postup volá *projekce*, omezuje šířku pásma a může zlepšit výkon dotazů, hlavně pro velké entit nebo sad výsledků dotazu. Použití **vyberte** parametr a předejte jí názvy vlastností, které chcete vrácen do klienta.

Dotaz v následujícím kódu vrátí pouze popisy entit v tabulce.

> [!NOTE]
> Následující fragment kódu funguje pouze s Azure Storage. Emulátor úložiště není podporován.

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Odstranění entity

Odstranění entity předáním jeho **PartitionKey** a **RowKey** k [delete_entity] [ py_delete_entity] metoda.

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Odstranění tabulky

Pokud již nepotřebujete pro tabulku nebo některé z entit v něm, zavolejte [delete_table] [ py_delete_table] metoda trvale odstranit z úložiště Azure v tabulce.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>Další postup

* [Časté otázky – vývoj s tabulkou rozhraní API](https://docs.microsoft.com/en-us/azure/cosmos-db/faq#develop-with-the-table-api)
* [Azure SDK DB Cosmos pro referenční dokumentace rozhraní API jazyka Python](https://azure.github.io/azure-cosmosdb-python/)
* [Středisko pro vývojáře programující v Pythonu](https://azure.microsoft.com/develop/python/)
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md): bezplatná aplikace a platformy pro vizuální práci s daty Azure Storage ve Windows, systému macOS a Linux.
* [Práce s Python v sadě Visual Studio (Windows)](https://docs.microsoft.com/en-us/visualstudio/python/overview-of-python-tools-for-visual-studio)

[py_commit_batch]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_create_table]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_delete_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_delete_table]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_Entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.models.html
[py_get_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_insert_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_insert_or_replace_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_TableService]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_TableBatch]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tablebatch.html
[py_merge_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_update_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
