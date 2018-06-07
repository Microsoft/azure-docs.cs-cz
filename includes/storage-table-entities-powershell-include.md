<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Správa entity tabulky

Teď, když máte tabulku, podíváme se na tom, jak spravovat entity nebo řádky v tabulce. 

Entita může mít maximálně 255 vlastnosti, včetně 3 Vlastnosti systému: **PartitionKey**, **RowKey**, a **časové razítko**. Jste zodpovědní za vložení a aktualizace hodnoty **PartitionKey** a **RowKey**. Server spravuje hodnotu **časové razítko**, který nemůže být upraven. Společně **PartitionKey** a **RowKey** jednoznačně identifikovat každou entitu v tabulce.

* **PartitionKey**: Určuje uložených entity v oddílu.
* **RowKey**: jednoznačně identifikuje entity v oddílu.

Můžete definovat vlastní až 252 vlastností entity. 

### <a name="add-table-entities"></a>Přidání entity tabulky

Přidání entity do tabulky pomocí **přidat StorageTableRow**. Tyto příklady použití klíče oddílů s hodnotami "Oddíl1" a "Oddíl2" a řádek klíče rovna zkratky stavu. Vlastnosti v každé entity jsou uživatelské jméno a ID uživatele. 

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

### <a name="query-the-table-entities"></a>Dotaz na entity tabulky

Existuje několik různých způsobů dotazování entity v tabulce.

#### <a name="retrieve-all-entities"></a>Načtení všech entit

Pro načtení všech entit, použijte **Get-AzureStorageTableRowAll**.

```powershell
Get-AzureStorageTableRowAll -table $storageTable | ft
```

Tento příkaz dává výsledky podobně jako v následující tabulce:

| ID uživatele | uživatelské jméno | oddíl | rowkey |
|----|---------|---------------|----|
| 1 | Jan | Oddíl1 | CA |
| 3 | Jana | Oddíl1 | WA |
| 2 | Klára | Oddíl2 | NM |
| 4 | Steven | Oddíl2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Načtení entit u konkrétního oddílu

Pro načtení všech entit v konkrétní oddílu, použijte **Get-AzureStorageTableRowByPartitionKey**.

```powershell
Get-AzureStorageTableRowByPartitionKey -table $storageTable -partitionKey $partitionKey1 | ft
```
Výsledky vypadat podobně jako v následující tabulce:

| ID uživatele | uživatelské jméno | oddíl | rowkey |
|----|---------|---------------|----|
| 1 | Jan | Oddíl1 | CA |
| 3 | Jana | Oddíl1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Načtení entit pro konkrétní hodnoty v určitém sloupci

Pro načtení entit, kde hodnota ve sloupci konkrétní rovná určitou hodnotu, použijte **Get-AzureStorageTableRowByColumnName**.

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
| uživatelské jméno | Jan |
| PartitionKey | Oddíl1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Načtení entit pomocí vlastního filtru 

Pro načtení entit pomocí vlastního filtru, použijte **Get-AzureStorageTableRowByCustomFilter**.

```powershell
Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter "(userid eq 1)"
```

Tento dotaz načte jeden záznam.

|Pole|hodnota|
|----|----|
| ID uživatele | 1 |
| uživatelské jméno | Jan |
| PartitionKey | Oddíl1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Aktualizace entit 

Existují tři kroky pro aktualizaci entity. Nejdřív načtěte entity, která má změnit. Za druhé proveďte požadovanou změnu. Třetí, potvrzení změn pomocí **aktualizace AzureStorageTableRow**.

Aktualizace entity s uživatelským jménem = 'Klára' mít uživatelské jméno = 'Jessie2'. Tento příklad také ukazuje další způsob, jak vytvořit vlastní filtr pomocí typy .NET. 

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

Výsledky zobrazit Jessie2 záznamu.

|Pole|hodnota|
|----|----|
| ID uživatele | 2 |
| uživatelské jméno | Jessie2 |
| PartitionKey | Oddíl2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Odstranění entity tabulky

Můžete odstranit jednu entitu nebo všechny entity v tabulce.

#### <a name="deleting-one-entity"></a>Odstraňování jedné entity

Pokud chcete odstranit jedné entity, získat odkaz na dané entity a zřetězit ho do **odebrat AzureStorageTableRow**.

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

Pokud chcete odstranit všechny entity v tabulce, je znovu načíst a přesměrováním výsledky do rutiny odebrat. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzureStorageTableRowAll `
    -table $storageTable | Remove-AzureStorageTableRow -table $storageTable 

# List entities in the table (there won't be any).
Get-AzureStorageTableRowAll -table $storageTable | ft
```
