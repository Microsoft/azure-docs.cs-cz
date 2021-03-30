---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "67174682"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Správa entit tabulky

Teď, když máte tabulku, se podívejme na to, jak spravovat entity nebo řádky v tabulce. 

Entity můžou mít až 255 vlastností, včetně tří systémových vlastností: **PartitionKey**, **RowKey** a **timestamp**. Zodpovídáte za vkládání a aktualizaci hodnot **PartitionKey** a **RowKey**. Server spravuje hodnotu **časového razítka**, kterou nelze upravit. Společně **PartitionKey** a **RowKey** jednoznačně identifikují každou entitu v tabulce.

* **PartitionKey**: Určuje oddíl, ve kterém je entita uložená.
* **RowKey**: jednoznačně identifikuje entitu v rámci oddílu.

Pro entitu můžete definovat až 252 vlastních vlastností. 

### <a name="add-table-entities"></a>Přidat entity tabulky

Přidejte do tabulky entity pomocí **Add-AzTableRow**. V těchto příkladech se používají klíče oddílů s hodnotami `partition1` a a `partition2` klíče řádku rovny zkratce State. Vlastnosti v každé entitě jsou `username` a `userid` . 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>Dotazování entit tabulky

Pomocí příkazu **Get-AzTableRow** můžete zadávat dotazy na entity v tabulce.

> [!NOTE]
> Rutiny **Get-AzureStorageTableRowAll**, **Get-AzureStorageTableRowByPartitionKey**, **Get-AzureStorageTableRowByColumnName** a **Get-AzureStorageTableRowByCustomFilter** jsou zastaralé a v budoucí aktualizaci verze se odeberou.

#### <a name="retrieve-all-entities"></a>Načíst všechny entity

```powershell
Get-AzTableRow -table $cloudTable | ft
```

Tento příkaz vrátí výsledky podobné následující tabulce:

| UserID | username | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | PARTITION2 | NM |
| 4 | Steven | PARTITION2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Načtení entit pro určitý oddíl

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

Výsledky vypadají podobně jako v následující tabulce:

| UserID | username | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Načtení entit pro konkrétní hodnotu v konkrétním sloupci

```powershell
Get-AzTableRow -table $cloudTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Tento dotaz načte jeden záznam.

|pole|hodnota|
|----|----|
| UserID | 1 |
| username | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Načtení entit pomocí vlastního filtru 

```powershell
Get-AzTableRow `
    -table $cloudTable `
    -customFilter "(userid eq 1)"
```

Tento dotaz načte jeden záznam.

|pole|hodnota|
|----|----|
| UserID | 1 |
| username | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Aktualizace entit 

Existují tři kroky pro aktualizaci entit. Nejdřív načtěte entitu, která se má změnit. Za druhé proveďte změnu. Třetí, potvrďte změnu pomocí **Update-AzTableRow**.

Aktualizujte entitu s username = ' Jessie ' tak, aby měla username = ' Jessie2 '. Tento příklad také ukazuje jiný způsob, jak vytvořit vlastní filtr pomocí typů .NET.

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2"

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzTableRow -table $cloudTable

# To see the new record, query the table.
Get-AzTableRow -table $cloudTable `
    -customFilter "(username eq 'Jessie2')"
```

Ve výsledcích se zobrazí záznam Jessie2.

|pole|hodnota|
|----|----|
| UserID | 2 |
| username | Jessie2 |
| PartitionKey | PARTITION2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Odstraňují se entity tabulky

V tabulce můžete odstranit jednu entitu nebo všechny entity.

#### <a name="deleting-one-entity"></a>Odstraňuje se jedna entita.

Pokud chcete odstranit jednu entitu, získejte odkaz na tuto entitu a přesměrujte ji na **Remove-AzTableRow**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter
$userToDelete | Remove-AzTableRow -table $cloudTable

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzTableRow -table $cloudTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>Odstraní všechny entity v tabulce.

Chcete-li odstranit všechny entity v tabulce, načtěte je a kanálu výsledky do rutiny Remove. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
