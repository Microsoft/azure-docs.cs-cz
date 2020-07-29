---
title: Povolte a spravujte obnovení k určitému bodu v čase pro objekty blob bloku (Preview).
titleSuffix: Azure Storage
description: Naučte se používat obnovení k určitému bodu v čase (Preview) k obnovení objektů blob bloku do stavu v dřívějším bodě v čase.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/11/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6948d4d786e918e5f3e32e6bdf2f7e23940f6815
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445436"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>Povolte a spravujte obnovení k určitému bodu v čase pro objekty blob bloku (Preview).

K obnovení objektů blob bloku do jejich stavu v dřívějším časovém okamžiku můžete použít obnovení k bodu v čase (Preview). Tento článek popisuje, jak povolit obnovení k určitému bodu v čase pro účet úložiště pomocí PowerShellu. Také ukazuje, jak provést operaci obnovení pomocí prostředí PowerShell.

Další informace a informace o tom, jak se zaregistrovat pro verzi Preview, najdete v tématu [obnovení k bodu v čase pro objekty blob bloku (Preview)](point-in-time-restore-overview.md).

> [!CAUTION]
> Obnovení k bodu v čase podporuje pouze obnovení operací pouze pro objekty blob bloku. Operace na kontejnerech nelze obnovit. Pokud odstraníte kontejner z účtu úložiště voláním operace [odstranění kontejneru](/rest/api/storageservices/delete-container) během obnovování k určitému bodu v čase, nelze tento kontejner obnovit pomocí operace obnovení. V rámci verze Preview místo odstranění kontejneru odstraňte jednotlivé objekty blob, pokud je budete chtít obnovit.

> [!IMPORTANT]
> Obnovení bodu v čase je ve verzi Preview určeno pouze pro neprodukční použití. Smlouvy o úrovni produkčních služeb (SLA) nejsou aktuálně k dispozici.

## <a name="install-the-preview-module"></a>Instalace modulu Preview

Pokud chcete nakonfigurovat Azure Point-in-Time Restore pomocí PowerShellu, nejdřív nainstalujte modul AZ. Storage Preview verze 1.14.1-Preview nebo novější. Doporučuje se použít nejnovější verzi Preview, ale obnovení k bodu v čase je podporované ve verzi 1.14.1-Preview a novější. Odeberte jakékoli jiné verze modulu AZ. Storage.

Následující příkaz nainstaluje AZ. Storage [2.0.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) Module:

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.0.1-preview -AllowPrerelease
```

K instalaci výše uvedeného příkazu je potřeba 2.2.4.1 verze PowerShellGet nebo novější. Postup určení verze, kterou jste právě načetli:
```powershell
Get-Module PowerShellGet
```
Další informace o instalaci Azure PowerShell najdete v tématu [instalace Azure PowerShell pomocí PowerShellGet](/powershell/azure/install-az-ps).

## <a name="enable-and-configure-point-in-time-restore"></a>Povolit a nakonfigurovat obnovení k časovému okamžiku

Než povolíte a nakonfigurujete obnovení k bodu v čase, povolte jeho požadavky pro účet úložiště: Obnovitelné odstranění, změna kanálu a správa verzí objektů BLOB. Další informace o povolení jednotlivých funkcí najdete v těchto článcích:

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

Chcete-li zahájit operaci obnovení, zavolejte příkaz **Restore-AzStorageBlobRange** a určete bod obnovení jako hodnotu **DateTime** UTC. Můžete zadat lexicographical rozsahy objektů blob, které se mají obnovit, nebo vynecháte rozsah pro obnovení všech objektů BLOB ve všech kontejnerech v účtu úložiště. Pro každou operaci obnovení se podporuje až 10 lexicographical rozsahů. Objekty blob stránky a doplňovací objekty blob nejsou zahrnuté do obnovení. Dokončení operace obnovení může trvat několik minut.

Při zadávání rozsahu objektů blob, které se mají obnovit, pamatujte na následující pravidla:

- Vzorek kontejneru zadaný pro počáteční rozsah a koncový rozsah musí obsahovat minimálně tři znaky. Lomítko (/), které se používá k oddělení názvu kontejneru z názvu objektu blob, se nepočítá směrem k tomuto minimu.
- Pro operaci obnovení lze zadat až 10 rozsahů.
- Zástupné znaky nejsou podporovány. Jsou považovány za standardní znaky.
- Objekty blob můžete obnovit v `$root` `$web` kontejnerech a tak, že je explicitně zadáte v rozsahu předaném do operace obnovení. `$root`Kontejnery a `$web` jsou obnoveny pouze v případě, že jsou výslovně určeny. Jiné systémové kontejnery nelze obnovit.

> [!IMPORTANT]
> Když provádíte operaci obnovení, Azure Storage blokuje operace s daty u objektů BLOB v rozsahu obnovování po dobu trvání operace. Operace čtení, zápisu a odstranění jsou v primárním umístění blokované. Z tohoto důvodu nemusí operace, jako je například výpis kontejnerů v Azure Portal, fungovat podle očekávání, zatímco probíhá operace obnovení.
>
> Operace čtení ze sekundárního umístění můžou během operace obnovení pokračovat, pokud je účet úložiště geograficky replikovaný.

### <a name="restore-all-containers-in-the-account"></a>Obnovit všechny kontejnery v účtu

Pokud chcete obnovit všechny kontejnery a objekty BLOB v účtu úložiště, zavolejte příkaz **Restore-AzStorageBlobRange** a vynechejte `-BlobRestoreRange` parametr. Následující příklad obnoví kontejnery v účtu úložiště do stavu 12 hodin před současným okamžikem:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>Obnovení jednoho rozsahu objektů blob bloku

Chcete-li obnovit rozsah objektů blob, zavolejte příkaz **Restore-AzStorageBlobRange** a zadejte lexicographical rozsah kontejnerů a názvů objektů BLOB pro `-BlobRestoreRange` parametr. Začátek rozsahu je v rozsahu včetně a konec rozsahu je exkluzivní.

Chcete-li například obnovit objekty BLOB v jednom kontejneru s názvem *Sample-Container*, můžete zadat rozsah, který začíná *vzorkem-Container* a končí na *Sample-container1*. Neexistuje žádný požadavek na to, aby kontejnery s názvem v rozsahu začátek a konec existovaly. Vzhledem k tomu, že konec rozsahu je exkluzivní, i když účet úložiště obsahuje kontejner s názvem *Sample-container1*, obnoví se jenom kontejner s názvem *Sample-Container* :

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

Chcete-li určit podmnožinu objektů BLOB v kontejneru pro obnovení, použijte lomítko (/) k oddělení názvu kontejneru od vzoru objektu BLOB. Například následující rozsah vybírá objekty BLOB v jednom kontejneru, jejichž názvy začínají písmeny *d* až *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

Dále zadejte rozsah příkazu **Restore-AzStorageBlobRange** . Zadejte bod obnovení zadáním hodnoty **DateTime** UTC pro `-TimeToRestore` parametr. Následující příklad obnoví objekty BLOB v zadaném rozsahu do jejich stavu 3 dny před současným okamžikem:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

### <a name="restore-multiple-ranges-of-block-blobs"></a>Obnovení více rozsahů objektů blob bloku

Chcete-li obnovit více rozsahů objektů blob bloku, zadejte pole rozsahů pro `-BlobRestoreRange` parametr. Na operaci obnovení se podporuje až 10 rozsahů. Následující příklad určuje dva rozsahy pro obnovení kompletního obsahu *container1* a *container4*:

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

### <a name="restore-block-blobs-asynchronously"></a>Asynchronní obnovení objektů blob bloku

Chcete-li spustit operaci obnovení asynchronně, přidejte do `-AsJob` volání metody **Restore-AzStorageBlobRange** parametr a výsledek volání uložte do proměnné. Příkaz **Restore-AzStorageBlobRange** vrátí objekt typu **AzureLongRunningJob**. Můžete ověřit vlastnost **stav** tohoto objektu a zjistit, zda byla operace obnovení dokončena. Hodnota vlastnosti **State** může být **spuštěná** nebo **Dokončená**.

Následující příklad ukazuje, jak asynchronní volání operace obnovení:

```powershell
$job = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-5) `
    -AsJob

# Check the state of the job.
$job.State
```

Chcete-li počkat na dokončení operace obnovení po spuštění, zavolejte příkaz [čekací úlohy](/powershell/module/microsoft.powershell.core/wait-job) , jak je znázorněno v následujícím příkladu:

```powershell
$job | Wait-Job
```

## <a name="known-issues"></a>Známé problémy
- Pro podmnožinu obnovení, kde jsou k dispozici doplňovací objekty blob, obnovení se nezdaří. Prozatím prosím neprovádějte obnovení, pokud se v účtu nacházejí doplňovací objekty blob.

## <a name="next-steps"></a>Další kroky

- [Obnovení bodu v čase pro objekty blob bloku (Preview)](point-in-time-restore-overview.md)
- [Obnovitelné odstranění](soft-delete-overview.md)
- [Změnit kanál (Preview)](storage-blob-change-feed.md)
- [Správa verzí objektů BLOB (Preview)](versioning-overview.md)
