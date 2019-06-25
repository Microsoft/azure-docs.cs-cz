---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174682"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Správa entit tabulky

Teď, když máte tabulku, Podívejme se na tom, jak spravovat entity nebo řádky v tabulce. 

Entity můžete mít až 255 vlastnosti, včetně tři systémové vlastnosti: **PartitionKey**, **RowKey**, a **časové razítko**. Zodpovídáte za vkládání a aktualizacích hodnoty **PartitionKey** a **RowKey**. Server spravuje hodnotu **časové razítko**, která nelze upravit. Společně **PartitionKey** a **RowKey** jednoznačně identifikovat každou entitu v tabulce.

* **PartitionKey**: Určuje oddíl, který entitu je uložen v.
* **RowKey**: Jednoznačně identifikuje entitu v rámci oddílu.

Můžete definovat až 252 vlastností vlastní entity. 

### <a name="add-table-entities"></a>Přidání tabulky entit

Přidání entity do tabulky pomocí **přidat AzTableRow**. Tyto příklady používají klíče oddílů s hodnotami `partition1` a `partition2`, a klíče řádku rovno zkratky států. Vlastnosti v každé entity jsou `username` a `userid`. 

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

### <a name="query-the-table-entities"></a>Dotaz na tabulku entity

Entity v tabulce můžete dotazovat pomocí **Get-AzTableRow** příkazu.

> [!NOTE]
> Rutiny **Get-AzureStorageTableRowAll**, **Get-AzureStorageTableRowByPartitionKey**, **Get-AzureStorageTableRowByColumnName**, a  **Get-AzureStorageTableRowByCustomFilter** jsou zastaralé a bude v aktualizace budoucí verzi odebrána.

#### <a name="retrieve-all-entities"></a>Načtení všech entit

```powershell
Get-AzTableRow -table $cloudTable | ft
```

Tento příkaz vrátí výsledky, podobně jako v následující tabulce:

| userid | username jméno | oddíl | rowkey |
|----|---------|---------------|----|
| 1 | Chris | Oddíl1 | CA |
| 3 | Jana | Oddíl1 | WA |
| 2 | Jessie | Oddíl2 | NM |
| 4 | Steven | Oddíl2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Načtení entit u konkrétního oddílu

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

Výsledky vypadat podobně jako v následující tabulce:

| userid | username jméno | oddíl | rowkey |
|----|---------|---------------|----|
| 1 | Chris | Oddíl1 | CA |
| 3 | Jana | Oddíl1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Načtení entit pro určitou hodnotu v konkrétním sloupci

```powershell
Get-AzTableRow -table $cloudTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Tento dotaz načte jeden záznam.

|Pole|value|
|----|----|
| userid | 1 |
| username jméno | Chris |
| PartitionKey | Oddíl1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Načtení entity pomocí vlastního filtru 

```powershell
Get-AzTableRow `
    -table $cloudTable `
    -customFilter "(userid eq 1)"
```

Tento dotaz načte jeden záznam.

|Pole|value|
|----|----|
| userid | 1 |
| username jméno | Chris |
| PartitionKey | Oddíl1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Aktualizace entit 

Existují tři kroky pro aktualizaci entity. Nejdřív načtěte entitu, chcete-li změnit. Za druhé proveďte požadovanou změnu. Třetí, potvrzení změn pomocí **aktualizace AzTableRow**.

Aktualizovat entitu s uživatelským jménem = "Jessie" mít uživatelské jméno = "Jessie2". Tento příklad také uvádí dalším způsobem, jak vytvořit vlastní filtr pomocí typy .NET.

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

Ve výsledcích zobrazí Jessie2 záznamu.

|Pole|value|
|----|----|
| userid | 2 |
| username jméno | Jessie2 |
| PartitionKey | Oddíl2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Odstraňuje se tabulka entity

Můžete odstranit jednu entitu nebo pro všechny entity v tabulce.

#### <a name="deleting-one-entity"></a>Odstranění entit

Pokud chcete odstranit jednu entitu, získáte odkaz na dané entitě a zřetězit ho do **odebrat AzTableRow**.

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

#### <a name="delete-all-entities-in-the-table"></a>Odstranit všechny entity v tabulce

Pokud chcete odstranit všechny entity v tabulce, načítat a kanálem výsledky do rutiny remove. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
