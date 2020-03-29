---
title: Nastavení a správa zásad neměnnosti pro úložiště objektů Blob – Azure Storage
description: Naučte se používat podporu WORM (Write Once, Read Many) pro úložiště objektů Blob (objekt) k ukládání dat v nesmazatelném, neupravitelném stavu po zadaný interval.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 05a155584f0cb69191883cb82b3db0af435ccc12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970100"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>Nastavení a správa zásad neměnnosti pro úložiště objektů Blob

Neměnné úložiště pro úložiště objektů blob Azure umožňuje uživatelům ukládat důležité podnikové datové objekty ve stavu WORM (Write Once, Read Many). Tento stav umožňuje data nesympatizatelné a neupravitelné pro interval zadaný uživatelem. Po dobu trvání intervalu uchování objekty BLOB lze vytvořit a číst, ale nelze je změnit ani odstranit. Neměnné úložiště je k dispozici pro účty úložiště pro obecné účely v2 a Blob ve všech oblastech Azure.

Tento článek ukazuje, jak nastavit a spravovat zásady neměnnosti a právní blokování pro data v úložišti objektů Blob pomocí portálu Azure, PowerShellu nebo Azure CLI. Další informace o neměnné úložiště najdete v [tématu Ukládání důležitých důležitých dat objektu blob s neměnným úložištěm](storage-blob-immutable-storage.md).

## <a name="set-retention-policies-and-legal-holds"></a>Nastavení zásad uchovávání informací a právních blokování

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Vytvořte nový kontejner nebo vyberte stávající kontejner pro uložení objektů blob, které je potřeba zachovat v neměnném stavu. Kontejner musí být v účtu úložiště pro obecné účely v2 nebo Blob.

2. V nastavení kontejneru vyberte **Zásady přístupu.** Pak vyberte **Přidat zásady** v části **Úložiště objektů blob Neměnné**.

    ![Nastavení kontejneru na portálu](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. Chcete-li povolit uchovávání informací podle času, vyberte v rozevírací nabídce **možnost Uchovávání podle času.**

    !["Uchovávání informací podle času" vybrané v části "Typ zásady"](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. Zadejte interval uchování ve dnech (přijatelné hodnoty jsou 1 až 146000 dní).

    ![Pole Aktualizovat dobu uchování na](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    Počáteční stav zásady je odemčen, což umožňuje otestovat funkci a provést změny zásady před uzamčením. Uzamčení politiky je nezbytné pro dodržování předpisů, jako je SEC 17a-4.

5. Uzamkněte zásady. Klepněte pravým tlačítkem myši na tři tečky (**...**) a zobrazí se následující nabídka s dalšími akcemi:

    !["Zamknout zásady" v nabídce](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. Vyberte **Zamknout zásady** a potvrďte zámek. Zásada je nyní uzamčena a nelze ji odstranit, budou povolena pouze rozšíření intervalu uchování. Odstranění objektu blob a přepsání nejsou povoleny. 

    ![Potvrzení "Zamknout zásady" v nabídce](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. Chcete-li povolit blokování z právních předpisů, vyberte **možnost Přidat zásady**. V rozevírací nabídce vyberte **Možnost Legální blokování.**

    !["Blokování z právního nastavení" v nabídce v části "Typ zásady"](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. Vytvořte blokování s jednou nebo více značkami.

    ![Pole "Název značky" pod typem zásady](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. Chcete-li zrušit blokování, odeberte použitou značku identifikátoru blokování.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Tato funkce je zahrnuta v `az storage container immutability-policy` `az storage container legal-hold`následujících skupinách příkazů: a . Běh `-h` na ně vidět příkazy.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Modul Az.Storage podporuje neměnné úložiště.  Chcete-li tuto funkci povolit, postupujte takto:

1. Ujistěte se, že máte nainstalovanou `Install-Module PowerShellGet –Repository PSGallery –Force`nejnovější verzi prostředí PowerShellGet: .
2. Odeberte všechny předchozí instalace Azure PowerShellu.
3. Instalace Azure PowerShellu: `Install-Module Az –Repository PSGallery –AllowClobber`.

Následující ukázkový skript prostředí PowerShell je pro referenci. Tento skript vytvoří nový účet úložiště a kontejner. Potom ukazuje, jak nastavit a vymazat právní blokování, vytvořit a uzamknout zásady uchovávání informací založené na čase (označované také jako zásady neměnnosti) a prodloužit interval uchovávání informací.

Nejprve vytvořte účet Azure Storage:

```powershell
$resourceGroup = "<Enter your resource group>"
$storageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$location = "<Enter the storage account location>"

# Log in to Azure
Connect-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create your Azure storage account
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup -StorageAccountName `
    $storageAccount -SkuName Standard_ZRS -Location $location -Kind StorageV2

# Create a new container using the context
$container = New-AzStorageContainer -Name $container -Context $account.Context

# List the containers in the account
Get-AzStorageContainer -Context $account.Context

# Remove a container
Remove-AzStorageContainer -Name $container -Context $account.Context
```

Nastavení a jasné právní blokování:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag1>,<tag2>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag3>
```

Vytvořte nebo aktualizujte zásady neměnnosti založené na čase:

```powershell
# Create a time-based immutablity policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

Načíst zásady neměnnosti:

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

Zamknout zásady neměnnosti (přidáním `-Force` výzvy k zavření):

```powershell
# Lock immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container
Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container `
    -Etag $policy.Etag
```

Rozšířit zásady neměnnosti:

```powershell
# Extend immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy
```

Odebrání zásady neměnnosti odemknuté (přidáním `-Force` výzvy k zavření):

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="enabling-allow-protected-append-blobs-writes"></a>Povolení zápisů objektů BLOB s chráněným připojením

### <a name="portal"></a>[Portál](#tab/azure-portal)

![Povolit další zápisy připojit](media/storage-blob-immutability-policies-manage/immutable-allow-additional-append-writes.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Tato funkce je zahrnuta v `az storage container immutability-policy` `az storage container legal-hold`následujících skupinách příkazů: a . Běh `-h` na ně vidět příkazy.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Create an immutablity policy with appends allowed
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10 -AllowProtectedAppendWrite $true
```

---

## <a name="next-steps"></a>Další kroky

[Ukládání důležitých dat objektů blob s neměnným úložištěm](storage-blob-immutable-storage.md)
