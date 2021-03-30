---
title: Nastavení a Správa zásad neměnnosti pro úložiště objektů blob – Azure Storage
description: Přečtěte si, jak používat ČERVa (Write jednou, číst mnoho) pro úložiště objektů BLOB (Object) k ukládání dat do neerasableho stavu, který není upravitelný, v zadaném intervalu.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: a09dbd7d778a4f7ea2a9aac9ca07b9e6d06bc1ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "84463599"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>Nastavení a Správa zásad neměnnosti pro úložiště objektů BLOB

Neměnné úložiště pro úložiště objektů BLOB v Azure umožňuje uživatelům ukládat datové objekty kritické pro podnikání do ČERVa (psát jednou, číst mnoho). Tento stav zpřístupňuje data, která nejsou Erasable a není upravitelná pro interval zadaný uživatelem. Po dobu trvání intervalu uchování dat je možné objekty blob vytvořit a číst, ale nelze je upravit ani odstranit. K dispozici je neměnné úložiště pro účty pro obecné účely v2 a BLOB Storage ve všech oblastech Azure.

Tento článek popisuje, jak pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure nastavovat a spravovat zásady neměnnosti a právní prostory pro data v úložišti objektů BLOB. Další informace o neměnných úložištích najdete v tématu [ukládání důležitých podnikových dat objektů BLOB s neměnném úložištěm](storage-blob-immutable-storage.md).

## <a name="set-retention-policies-and-legal-holds"></a>Nastavit zásady uchovávání informací a právní blokování

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Vytvořte nový kontejner nebo vyberte stávající kontejner pro uložení objektů blob, které je potřeba zachovat v neměnném stavu. Kontejner musí být v účtu úložiště pro obecné účely v2 nebo BLOB.

2. V nastavení kontejneru vyberte **zásady přístupu** . Pak vyberte **Přidat zásadu** v části **neměnné úložiště objektů BLOB**.

    ![Nastavení kontejneru na portálu](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. Pokud chcete povolit uchovávání na základě času, v rozevírací nabídce vyberte **časovou dobu uchování** .

    !["Doba uchovávání na základě času" vybraná v části "typ zásad"](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. Zadejte interval uchování ve dnech (přijatelné hodnoty jsou 1 až 146000 dní).

    ![Pole aktualizovat dobu uchování na](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    Počáteční stav zásady je odemčený, což vám umožní otestovat tuto funkci a před tím, než ji uzamknete, provést změny zásad. Uzamčení zásad je nezbytné pro dodržování předpisů s předpisy, jako je například 17a – 4.

5. Zásady uzamknout. Klikněte pravým tlačítkem myši na tři tečky (**...**) a zobrazí se následující nabídka s dalšími akcemi:

    !["Zásada zámku" v nabídce](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. Vyberte **zásady uzamčení** a potvrďte zámek. Zásada je nyní uzamčena a nelze ji odstranit, bude povolena pouze přípona intervalu uchování. Odstranění a přepsání objektu BLOB nejsou povoleny. 

    ![V nabídce potvrďte zásady uzamčení.](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. Pokud chcete povolit právní blokování, vyberte **Přidat zásadu**. Z rozevírací nabídky vyberte **právní blokování** .

    !["Právní blokování" v nabídce v části "typ zásad"](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. Vytvořte právní blokování s jednou nebo více značkami.

    ![Pole "název značky" pod položkou Typ zásady](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. Chcete-li odstranit právní blokování, odeberte značku identifikátoru používaného právního blokování.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Tato funkce je zahrnutá v následujících skupinách příkazů: `az storage container immutability-policy`  a `az storage container legal-hold` . Spusťte `-h` na nich, aby se zobrazily příkazy.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Modul AZ. Storage podporuje neměnné úložiště.  Pokud chcete tuto funkci povolit, postupujte takto:

1. Ujistěte se, že máte nainstalovanou nejnovější verzi PowerShellGet: `Install-Module PowerShellGet –Repository PSGallery –Force` .
2. Odeberte všechny předchozí instalace Azure PowerShell.
3. Nainstalovat Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber` .

Následující vzorový skript PowerShellu je určen pro referenci. Tento skript vytvoří nový účet úložiště a kontejner. Pak se dozvíte, jak nastavit a vymazat právní omezení, vytvořit a uzamknout zásady uchovávání informací založené na čase (označované také jako zásady neměnnosti) a rozšíří interval uchovávání informací.

Nejdřív vytvořte účet Azure Storage:

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
$account = New-AzStorageAccount -ResourceGroupName $resourceGroup -StorageAccountName `
    $storageAccount -SkuName Standard_ZRS -Location $location -Kind StorageV2

# Create a new container using the context
$container = New-AzStorageContainer -Name $container -Context $account.Context

# List the containers in the account
Get-AzStorageContainer -Context $account.Context

# Remove a container
Remove-AzStorageContainer -Name $container -Context $account.Context
```

Nastavit a vymazat právní blokování:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag1>,<tag2>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag3>
```

Vytvořit nebo aktualizovat zásady neměnnosti založené na čase:

```powershell
# Create a time-based immutability policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

Načíst zásady neměnnosti:

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

Uzamknout zásady neměnnosti (přidat `-Force` a zavřít výzvu):

```powershell
# Lock immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container
Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container `
    -Etag $policy.Etag
```

Rozšířené zásady neměnnosti:

```powershell
# Extend immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy
```

Odeberte odemčenou zásadu neměnnosti (přidejte `-Force` a zavřete tak výzvu):

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="enabling-allow-protected-append-blobs-writes"></a>Povoluje se povolit zápisy chráněných objektů BLOB

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

![Povolení dalších připojovat zápisů](media/storage-blob-immutability-policies-manage/immutable-allow-additional-append-writes.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Tato funkce je zahrnutá v následujících skupinách příkazů: `az storage container immutability-policy`  a `az storage container legal-hold` . Spusťte `-h` na nich, aby se zobrazily příkazy.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Create an immutability policy with appends allowed
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10 -AllowProtectedAppendWrite $true
```

---

## <a name="next-steps"></a>Další kroky

[Ukládání důležitých podnikových dat objektů blob s využitím neměnného úložiště](storage-blob-immutable-storage.md)
