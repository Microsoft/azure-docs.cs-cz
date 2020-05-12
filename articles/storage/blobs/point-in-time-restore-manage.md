---
title: Povolte a spravujte obnovení k určitému bodu v čase pro objekty blob bloku (Preview).
titleSuffix: Azure Storage
description: Naučte se používat obnovení k určitému bodu v čase (Preview) k obnovení objektů blob bloku do stavu v dřívějším bodě v čase.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: cbfc5667fb35b8f807a3a806dda4647af10e9392
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118205"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>Povolte a spravujte obnovení k určitému bodu v čase pro objekty blob bloku (Preview).

K obnovení objektů blob bloku do jejich stavu v dřívějším časovém okamžiku můžete použít obnovení k bodu v čase (Preview). Tento článek popisuje, jak povolit obnovení k určitému bodu v čase pro účet úložiště pomocí PowerShellu. Také ukazuje, jak provést operaci obnovení pomocí prostředí PowerShell.

Další informace a informace o tom, jak se zaregistrovat pro verzi Preview, najdete v tématu [obnovení k bodu v čase pro objekty blob bloku (Preview)](point-in-time-restore-overview.md).

> [!CAUTION]
> Obnovení k bodu v čase podporuje pouze obnovení operací pouze pro objekty blob bloku. Operace na kontejnerech nelze obnovit. Pokud odstraníte kontejner z účtu úložiště voláním operace [odstranění kontejneru](/rest/api/storageservices/delete-container) během obnovování k určitému bodu v čase, nelze tento kontejner obnovit pomocí operace obnovení. V rámci verze Preview místo odstranění kontejneru odstraňte jednotlivé objekty blob, pokud je budete chtít obnovit.

> [!IMPORTANT]
> Obnovení bodu v čase je ve verzi Preview určeno pouze pro neprodukční použití. Smlouvy o úrovni produkčních služeb (SLA) nejsou aktuálně k dispozici.

## <a name="install-the-preview-module"></a>Instalace modulu Preview

Pokud chcete nakonfigurovat službu Azure Point-in-Time Restore pomocí PowerShellu, nejdřív nainstalujte verzi [1.14.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.14.1-preview) modulu PowerShellu AZ. Storage. Pomocí těchto kroků nainstalujete modul verze Preview:

1. Odinstalujte všechny předchozí instalace Azure PowerShell ze systému Windows pomocí nastavení **aplikace & funkce** v části **Nastavení**.

1. Ujistěte se, že máte nainstalovanou nejnovější verzi PowerShellGet. Otevřete okno prostředí Windows PowerShell a spuštěním následujícího příkazu nainstalujte nejnovější verzi:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Po instalaci PowerShellGet zavřete a znovu otevřete okno PowerShellu.

1. Nainstalujte nejnovější verzi Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Instalace modulu AZ. Storage Preview:

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.14.1-preview -AllowPrerelease -AllowClobber -Force
    ```

Další informace o instalaci Azure PowerShell najdete v tématu [instalace Azure PowerShell pomocí PowerShellGet](/powershell/azure/install-az-ps).

## <a name="enable-and-configure-point-in-time-restore"></a>Povolit a nakonfigurovat obnovení k časovému okamžiku

Než povolíte a nakonfigurujete obnovení k bodu v čase, povolte jeho požadavky: Obnovitelné odstranění, změna kanálu a správa verzí objektů BLOB. Další informace o povolení jednotlivých funkcí najdete v těchto článcích:

- [Povolit obnovitelné odstranění pro objekty blob](soft-delete-enable.md)
- [Povolení a zákaz kanálu změn](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Povolení a správa verzí objektů BLOB](versioning-enable.md)

Pokud chcete nakonfigurovat Azure Point-in-Time Restore pomocí PowerShellu, zavolejte příkaz Enable-AzStorageBlobRestorePolicy. Následující příklad umožňuje obnovitelné odstranění a nastavuje dobu uchování s mírným odstraněním, povoluje kanál změn a pak umožňuje obnovení k určitému bodu v čase. Před spuštěním tohoto příkladu použijte Azure Portal nebo šablonu Azure Resource Manager, abyste mohli taky povolit správu verzí objektů BLOB.

Při spuštění tohoto příkladu Nezapomeňte nahradit hodnoty v lomených závorkách vlastními hodnotami:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 6 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 6

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

# Enable point-in-time restore with a retention period of 5 days.
# The retention period for point-in-time restore must be at least one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 5

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

## <a name="perform-a-restore-operation"></a>Provést operaci obnovení

Chcete-li zahájit operaci obnovení, zavolejte příkaz Restore-AzStorageBlobRange a určete bod obnovení jako hodnotu **DateTime** UTC. Můžete určit jeden nebo více lexicographical rozsahů objektů blob, které se mají obnovit, nebo vynecháte rozsah pro obnovení všech objektů BLOB ve všech kontejnerech v účtu úložiště. Dokončení operace obnovení může trvat několik minut.

Při zadávání rozsahu objektů blob, které se mají obnovit, pamatujte na následující pravidla:

- Vzorek kontejneru zadaný pro počáteční rozsah a koncový rozsah musí obsahovat minimálně tři znaky. Lomítko (/), které se používá k oddělení názvu kontejneru z názvu objektu blob, se nepočítá směrem k tomuto minimu.
- Pro operaci obnovení lze zadat pouze jeden rozsah.
- Zástupné znaky nejsou podporovány. Jsou považovány za standardní znaky.
- Objekty blob můžete obnovit v `$root` `$web` kontejnerech a tak, že je explicitně zadáte v rozsahu předaném do operace obnovení. `$root`Kontejnery a `$web` jsou obnoveny pouze v případě, že jsou výslovně určeny. Jiné systémové kontejnery nelze obnovit.

### <a name="restore-all-containers-in-the-account"></a>Obnovit všechny kontejnery v účtu

Pokud chcete obnovit všechny kontejnery a objekty BLOB v účtu úložiště, zavolejte příkaz Restore-AzStorageBlobRange a vynechejte `-BlobRestoreRange` parametr. Následující příklad obnoví kontejnery v účtu úložiště do stavu 12 hodin před současným okamžikem:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>Obnovení jednoho rozsahu objektů blob bloku

Chcete-li obnovit rozsah objektů blob, zavolejte příkaz Restore-AzStorageBlobRange a zadejte lexicographical rozsah kontejnerů a názvů objektů BLOB pro `-BlobRestoreRange` parametr. Začátek rozsahu je v rozsahu včetně a konec rozsahu je exkluzivní.

Chcete-li například obnovit objekty BLOB v jednom kontejneru s názvem *Sample-Container*, můžete zadat rozsah, který začíná *vzorkem-Container* a končí na *Sample-container1*. Neexistuje žádný požadavek na to, aby kontejnery s názvem v rozsahu začátek a konec existovaly. Vzhledem k tomu, že konec rozsahu je exkluzivní, i když účet úložiště obsahuje kontejner s názvem *Sample-container1*, obnoví se jenom kontejner s názvem *Sample-Container* :

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

Chcete-li určit podmnožinu objektů BLOB v kontejneru pro obnovení, použijte lomítko (/) k oddělení názvu kontejneru od vzoru objektu BLOB. Například následující rozsah vybírá objekty BLOB v jednom kontejneru, jejichž názvy začínají písmeny *d* až *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

Dále zadejte rozsah příkazu RESTORE-AzStorageBlobRange. Zadejte bod obnovení zadáním hodnoty **DateTime** UTC pro `-TimeToRestore` parametr. Následující příklad obnoví objekty BLOB v zadaném rozsahu do jejich stavu 3 dny před současným okamžikem:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

### <a name="restore-multiple-ranges-of-block-blobs"></a>Obnovení více rozsahů objektů blob bloku

Chcete-li obnovit více rozsahů objektů blob bloku, zadejte pole rozsahů pro `-BlobRestoreRange` parametr. Následující příklad obnoví úplný obsah *container1* a *container4*:

```powershell
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

## <a name="next-steps"></a>Další kroky

- [Obnovení bodu v čase pro objekty blob bloku (Preview)](point-in-time-restore-overview.md)
- [Obnovitelné odstranění](soft-delete-overview.md)
- [Změnit kanál (Preview)](storage-blob-change-feed.md)
- [Správa verzí objektů BLOB (Preview)](versioning-overview.md)
