---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: e8c5bf8e3c4cd63b7eec278c480527e95455140d
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166210"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Správa entit tabulky

Teď, když máte tabulku, Podívejme se na tom, jak spravovat entity nebo řádky v tabulce. 

Entita může mít maximálně 255 vlastnosti, včetně 3 Vlastnosti systému: **PartitionKey**, **RowKey**, a **časové razítko**. Zodpovídáte za vkládání a aktualizacích hodnoty **PartitionKey** a **RowKey**. Server spravuje hodnotu **časové razítko**, která nelze upravit. Společně **PartitionKey** a **RowKey** jednoznačně identifikovat každou entitu v tabulce.

* **PartitionKey**: Určuje oddíl, který entitu je uložen v.
* **RowKey**: jednoznačně identifikují entitu v rámci oddílu.

Můžete definovat až 252 vlastností vlastní entity. 

### <a name="add-table-entities"></a>Přidání tabulky entit

Přidání entity do tabulky pomocí **přidat StorageTableRow**. Tyto příklady používají klíče oddílů s hodnotami "Oddíl1" a "Oddíl2" a klíče řádku rovna zkratky států. Vlastnosti v každé entity jsou uživatelské jméno a ID uživatele. 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>Dotaz na tabulku entity

Existuje několik různých způsobů dotazování entity v tabulce.

#### <a name="retrieve-all-entities"></a>Načtení všech entit

Pokud chcete načíst všechny entity, použijte **Get-AzureStorageTableRowAll**.

```powershell
Get-AzureStorageTableRowAll -table $storageTable | ft
```

Tento příkaz vrátí výsledky, podobně jako v následující tabulce:

| ID uživatele | uživatelské jméno | oddíl | rowkey |
|----|---------|---------------|----|
| 1 | Chris | Oddíl1 | CA |
| 3 | Jana | Oddíl1 | WA |
| 2 | Jessie | Oddíl2 | NM |
| 4 | Steven | Oddíl2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Načtení entit u konkrétního oddílu

Pokud chcete načíst všechny entity v konkrétního oddílu, použijte **Get-AzureStorageTableRowByPartitionKey**.

```powershell
Get-AzureStorageTableRowByPartitionKey -table $storageTable -partitionKey $partitionKey1 | ft
```
Výsledky vypadat podobně jako v následující tabulce:

| ID uživatele | uživatelské jméno | oddíl | rowkey |
|----|---------|---------------|----|
| 1 | Chris | Oddíl1 | CA |
| 3 | Jana | Oddíl1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Načtení entit pro určitou hodnotu v konkrétním sloupci

Pro načtení entit, kde se hodnota v konkrétním sloupci rovná určitou hodnotu, použijte **Get-AzureStorageTableRowByColumnName**.

```powershell
Get-AzureStorageTableRowByColumnName -table $storageTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Tento dotaz načte jeden záznam.

|Pole|hodnota|
|----|----|
| ID uživatele | 1 |
| uživatelské jméno | Chris |
| PartitionKey | Oddíl1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Načtení entity pomocí vlastního filtru 

Chcete-li načíst entity pomocí vlastního filtru, použijte **Get-AzureStorageTableRowByCustomFilter**.

```powershell
Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter "(userid eq 1)"
```

Tento dotaz načte jeden záznam.

|Pole|hodnota|
|----|----|
| ID uživatele | 1 |
| uživatelské jméno | Chris |
| PartitionKey | Oddíl1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Aktualizace entit 

Existují tři kroky pro aktualizaci entity. Nejdřív načtěte entitu, chcete-li změnit. Za druhé proveďte požadovanou změnu. Třetí, potvrzení změn pomocí **aktualizace AzureStorageTableRow**.

Aktualizovat entitu s uživatelským jménem = "Jessie" mít uživatelské jméno = "Jessie2". Tento příklad také uvádí dalším způsobem, jak vytvořit vlastní filtr pomocí typy .NET. 

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2" 

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzureStorageTableRow -table $storageTable 

# To see the new record, query the table.
Get-AzureStorageTableRowByCustomFilter -table $storageTable `
    -customFilter "(username eq 'Jessie2')"
```

Ve výsledcích zobrazí Jessie2 záznamu.

|Pole|hodnota|
|----|----|
| ID uživatele | 2 |
| uživatelské jméno | Jessie2 |
| PartitionKey | Oddíl2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Odstraňuje se tabulka entity

Můžete odstranit jednu entitu nebo pro všechny entity v tabulce.

#### <a name="deleting-one-entity"></a>Odstranění entit

Pokud chcete odstranit jednu entitu, získáte odkaz na dané entitě a zřetězit ho do **odebrat AzureStorageTableRow**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter $filter
$userToDelete | Remove-AzureStorageTableRow -table $storageTable 

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzureStorageTableRowAll -table $storageTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>Odstranit všechny entity v tabulce 

Pokud chcete odstranit všechny entity v tabulce, načítat a kanálem výsledky do rutiny remove. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzureStorageTableRowAll `
    -table $storageTable | Remove-AzureStorageTableRow -table $storageTable 

# List entities in the table (there won't be any).
Get-AzureStorageTableRowAll -table $storageTable | ft
```
