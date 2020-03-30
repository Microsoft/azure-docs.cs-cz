---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174682"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Správa entit tabulky

Teď, když máte tabulku, podívejme se na to, jak spravovat entity nebo řádky v tabulce. 

Entity mohou mít až 255 vlastností, včetně tří vlastností systému: **PartitionKey**, **RowKey**a **Timestamp**. Jste zodpovědní za vkládání a aktualizaci hodnot **PartitionKey** a **RowKey**. Server spravuje hodnotu **časového razítka**, kterou nelze změnit. Společně **PartitionKey** a **RowKey** jednoznačně identifikovat každou entitu v rámci tabulky.

* **PartitionKey**: Určuje oddíl, ve které je entita uložena.
* **RowKey**: Jednoznačně identifikuje entitu v rámci oddílu.

Pro entitu můžete definovat až 252 vlastních vlastností. 

### <a name="add-table-entities"></a>Přidání entit tabulky

Přidejte entity do tabulky pomocí **add-AzTableRow**. Tyto příklady používají klíče `partition1` `partition2`oddílů s hodnotami a klávesy řádků, které se rovnají zkratkám stavu. Vlastnosti v každé `username` `userid`entitě jsou a . 

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

### <a name="query-the-table-entities"></a>Dotaz na entity tabulky

Entity v tabulce můžete dotazovat pomocí příkazu **Get-AzTableRow.**

> [!NOTE]
> Rutiny **Get-AzureStorageTableRowAll**, **Get-AzureStorageTableRowByPartitionKey**, **Get-AzureStorageTableRowByColumnName**a **Get-AzureStorageTableRowByCustomFilter** jsou zastaralé a budou odebrány v budoucí aktualizaci verze.

#### <a name="retrieve-all-entities"></a>Načíst všechny entity

```powershell
Get-AzTableRow -table $cloudTable | ft
```

Tento příkaz poskytuje výsledky podobné následující tabulce:

| Userid | uživatelské jméno | partition | řádek |
|----|---------|---------------|----|
| 1 | Chris | oddíl1 | CA |
| 3 | Christine | oddíl1 | WA |
| 2 | Jessie | oddíl2 | NM |
| 4 | Steven | oddíl2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Načtení entit pro konkrétní oddíl

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

Výsledky vypadají podobně jako v následující tabulce:

| Userid | uživatelské jméno | partition | řádek |
|----|---------|---------------|----|
| 1 | Chris | oddíl1 | CA |
| 3 | Christine | oddíl1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Načtení entit pro určitou hodnotu v určitém sloupci

```powershell
Get-AzTableRow -table $cloudTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Tento dotaz načte jeden záznam.

|pole|value|
|----|----|
| Userid | 1 |
| uživatelské jméno | Chris |
| PartitionKey | oddíl1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Načtení entit pomocí vlastního filtru 

```powershell
Get-AzTableRow `
    -table $cloudTable `
    -customFilter "(userid eq 1)"
```

Tento dotaz načte jeden záznam.

|pole|value|
|----|----|
| Userid | 1 |
| uživatelské jméno | Chris |
| PartitionKey | oddíl1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Aktualizace entit 

Existují tři kroky pro aktualizaci entit. Nejprve načtěte entitu změnit. Za druhé, proveďte změnu. Za třetí, potvrďte změnu pomocí **Update-AzTableRow**.

Aktualizujte entitu uživatelským jménem = 'Jessie' mít uživatelské jméno = 'Jessie2'. Tento příklad také ukazuje jiný způsob, jak vytvořit vlastní filtr pomocí typů .NET.

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

Výsledky ukazují rekord Jessie2.

|pole|value|
|----|----|
| Userid | 2 |
| uživatelské jméno | Jessie2 |
| PartitionKey | oddíl2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Odstranění entit tabulky

Můžete odstranit jednu entitu nebo všechny entity v tabulce.

#### <a name="deleting-one-entity"></a>Odstranění jedné entity

Chcete-li odstranit jednu entitu, získejte odkaz na tuto entitu a přepojijte ji do **příkazu Remove-AzTableRow**.

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

#### <a name="delete-all-entities-in-the-table"></a>Odstranění všech entit v tabulce

Chcete-li odstranit všechny entity v tabulce, načtěte je a výsledky natápějte do odebrané rutiny. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
