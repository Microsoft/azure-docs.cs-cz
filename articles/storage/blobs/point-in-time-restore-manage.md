---
title: Povolte a spravujte obnovení k určitému bodu v čase pro objekty blob bloku (Preview).
titleSuffix: Azure Storage
description: Naučte se používat obnovení k určitému bodu v čase (Preview) k obnovení sady objektů blob bloku do předchozího stavu.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/11/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 140e1203a29dcebec9d6483e73e906591b2213fb
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068477"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>Povolte a spravujte obnovení k určitému bodu v čase pro objekty blob bloku (Preview).

K obnovení sady objektů blob bloku do předchozího stavu můžete použít obnovení k určitému bodu v čase (Preview). Tento článek popisuje, jak povolit obnovení k určitému bodu v čase pro účet úložiště pomocí PowerShellu. Také ukazuje, jak provést operaci obnovení pomocí prostředí PowerShell.

Další informace a informace o tom, jak se zaregistrovat pro verzi Preview, najdete v tématu [obnovení k bodu v čase pro objekty blob bloku (Preview)](point-in-time-restore-overview.md).

> [!CAUTION]
> Obnovení k bodu v čase podporuje pouze obnovení operací pouze pro objekty blob bloku. Operace na kontejnerech nelze obnovit. Pokud odstraníte kontejner z účtu úložiště voláním operace [odstranění kontejneru](/rest/api/storageservices/delete-container) během obnovování k určitému bodu v čase, nelze tento kontejner obnovit pomocí operace obnovení. V rámci verze Preview místo odstranění kontejneru odstraňte jednotlivé objekty blob, pokud je budete chtít obnovit.

> [!IMPORTANT]
> Obnovení bodu v čase je ve verzi Preview určeno pouze pro neprodukční použití.

## <a name="enable-and-configure-point-in-time-restore"></a>Povolit a nakonfigurovat obnovení k časovému okamžiku

Než povolíte a nakonfigurujete obnovení k bodu v čase, povolte jeho požadavky pro účet úložiště: Obnovitelné odstranění, změna kanálu a správa verzí objektů BLOB. Další informace o povolení jednotlivých funkcí najdete v těchto článcích:

- [Povolení obnovitelného odstranění pro objekty blob](soft-delete-enable.md)
- [Povolení a zákaz kanálu změn](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Povolení a správa verzí objektů BLOB](versioning-enable.md)

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Pokud chcete nakonfigurovat obnovení k bodu v čase pomocí Azure Portal, postupujte takto:

1. Na webu Azure Portal přejděte na svůj účet úložiště.
1. V části **Nastavení**vyberte **Ochrana dat**.
1. Vyberte možnost **zapnout obnovení k bodu v čase** . Když vyberete tuto možnost, povolí se i obnovitelné odstraňování objektů blob, správy verzí a kanálu změn.
1. Nastavte maximální bod obnovení pro obnovení k časovému okamžiku ve dnech. Toto číslo musí být alespoň jeden den kratší než doba uchování zadaná pro obnovitelné odstranění objektu BLOB.
1. Uložte provedené změny.

Následující obrázek ukazuje účet úložiště nakonfigurovaný pro obnovení k určitému časovému okamžiku s bodem obnovení před sedmi dny a dobu uchování pro dočasné odstranění objektu BLOB 14 dní.

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="Snímek obrazovky, který ukazuje, jak nakonfigurovat obnovení k bodu v čase Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete nakonfigurovat obnovení k určitému bodu v čase pomocí PowerShellu, nejdřív nainstalujte modul příkaz AZ. Storage Preview verze 1.14.1-Preview nebo novější verze modulu Preview. Odeberte jakékoli jiné verze modulu AZ. Storage.

Ověřte, že máte nainstalovanou verzi 2.2.4.1 nebo novější z PowerShellGet. Pokud chcete zjistit, jakou verzi máte aktuálně nainstalovanou, spusťte následující příkaz:

```powershell
Get-InstalledModule PowerShellGet
```

Dále nainstalujte modul AZ. Storage Preview. Následující příkaz nainstaluje verzi [2.5.2-Preview](https://www.powershellgallery.com/packages/Az.Storage/2.5.2-preview) modulu AZ. Storage:

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.5.2-preview -AllowPrerelease
```

Další informace o instalaci Azure PowerShell najdete v tématu [instalace PowerShellGet](/powershell/scripting/gallery/installing-psget) a [instalace Azure PowerShell pomocí PowerShellGet](/powershell/azure/install-az-ps).

Pokud chcete nakonfigurovat Azure Point-in-Time Restore pomocí PowerShellu, zavolejte příkaz Enable-AzStorageBlobRestorePolicy. Následující příklad umožňuje obnovitelné odstranění a nastavuje dobu uchování s mírným odstraněním, povoluje kanál změn a pak umožňuje obnovení k určitému bodu v čase. Před spuštěním tohoto příkladu použijte Azure Portal nebo šablonu Azure Resource Manager, abyste mohli taky povolit správu verzí objektů BLOB.

Při spuštění tohoto příkladu Nezapomeňte nahradit hodnoty v lomených závorkách vlastními hodnotami:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 14 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 14

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

# Enable point-in-time restore with a retention period of 7 days.
# The retention period for point-in-time restore must be at least
# one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 7

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

---

## <a name="perform-a-restore-operation"></a>Provést operaci obnovení

Při provádění operace obnovení je nutné zadat bod obnovení jako hodnotu **DateTime** UTC. Kontejnery a objekty blob budou obnoveny do jejich stavu v daný den a čas. Dokončení operace obnovení může trvat několik minut.

Můžete obnovit všechny kontejnery v účtu úložiště, nebo můžete obnovit rozsah objektů BLOB v jednom nebo více kontejnerech. Rozsah objektů BLOB je definován lexikograficky, což znamená v pořadí slovníku. Na operaci obnovení se podporuje až deset rozsahů lexicographical. Začátek rozsahu je včetně a konec rozsahu je exkluzivní.

Vzorek kontejneru zadaný pro počáteční rozsah a koncový rozsah musí obsahovat minimálně tři znaky. Lomítko (/), které se používá k oddělení názvu kontejneru z názvu objektu blob, se nepočítá směrem k tomuto minimu.

Zástupné znaky nejsou podporovány v rozsahu lexicographical. Jakékoli zástupné znaky se považují za standardní znaky.

Objekty blob můžete obnovit v `$root` `$web` kontejnerech a tak, že je explicitně zadáte v rozsahu předaném do operace obnovení. `$root`Kontejnery a `$web` jsou obnoveny pouze v případě, že jsou výslovně určeny. Jiné systémové kontejnery nelze obnovit.

Obnoveny jsou pouze objekty blob bloku. Objekty blob stránky a doplňovací objekty blob nejsou zahrnuté do operace obnovení. Další informace o omezeních souvisejících s připojením objektů BLOB najdete v tématu [známé problémy](#known-issues).

> [!IMPORTANT]
> Když provádíte operaci obnovení, Azure Storage blokuje operace s daty u objektů BLOB v rozsahu obnovování po dobu trvání operace. Operace čtení, zápisu a odstranění jsou v primárním umístění blokované. Z tohoto důvodu nemusí operace, jako je například výpis kontejnerů v Azure Portal, fungovat podle očekávání, zatímco probíhá operace obnovení.
>
> Operace čtení ze sekundárního umístění můžou během operace obnovení pokračovat, pokud je účet úložiště geograficky replikovaný.

### <a name="restore-all-containers-in-the-account"></a>Obnovit všechny kontejnery v účtu

Můžete obnovit všechny kontejnery v účtu úložiště a vrátit je do předchozího stavu v daném časovém okamžiku.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Pro obnovení všech kontejnerů a objektů BLOB v účtu úložiště pomocí Azure Portal postupujte takto:

1. Přejděte do seznamu kontejnerů pro váš účet úložiště.
1. Na panelu nástrojů zvolte **obnovit kontejnery**a pak **Obnovit vše**.
1. V podokně **obnovit všechny kontejnery** určete bod obnovení zadáním data a času.
1. Zaškrtnutím políčka potvrďte, že chcete pokračovat.
1. Vyberte **obnovit** a spusťte operaci obnovení.

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="Snímek obrazovky ukazující obnovení všech kontejnerů do zadaného bodu obnovení":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete obnovit všechny kontejnery a objekty BLOB v účtu úložiště pomocí PowerShellu, zavolejte příkaz **Restore-AzStorageBlobRange** , který tento parametr vynechá `-BlobRestoreRange` . Následující příklad obnoví kontejnery v účtu úložiště do stavu 12 hodin před současným okamžikem:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

---

### <a name="restore-ranges-of-block-blobs"></a>Obnovit rozsahy objektů blob bloku

Můžete obnovit jeden nebo více lexicographical rozsahů objektů BLOB v jednom kontejneru nebo napříč více kontejnery, aby se tyto objekty blob vracely do předchozího stavu v daném časovém okamžiku.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Chcete-li obnovit rozsah objektů BLOB v jednom nebo více kontejnerech s Azure Portal, postupujte podle následujících kroků:

1. Přejděte do seznamu kontejnerů pro váš účet úložiště.
1. Vyberte kontejner nebo kontejnery, které chcete obnovit.
1. Na panelu nástrojů zvolte **obnovit kontejnery**a pak **Obnovit vybrané**.
1. V podokně **Obnovit vybrané kontejnery** určete bod obnovení zadáním data a času.
1. Zadejte rozsahy, které se mají obnovit. K vymezení názvu kontejneru z předpony objektu BLOB použijte lomítko (/).
1. Ve výchozím nastavení podokno **Obnovit vybrané kontejnery** určuje rozsah, který zahrnuje všechny objekty BLOB v kontejneru. Tento rozsah odstraňte, pokud nechcete obnovit celý kontejner. Výchozí rozsah je zobrazen na následujícím obrázku.

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="Snímek obrazovky s výchozím rozsahem objektů blob, který se má odstranit před zadáním vlastního rozsahu":::

1. Zaškrtnutím políčka potvrďte, že chcete pokračovat.
1. Vyberte **obnovit** a spusťte operaci obnovení.

Následující obrázek znázorňuje operaci obnovení v sadě rozsahů.

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="Snímek obrazovky, který ukazuje, jak obnovit rozsahy objektů BLOB v jednom nebo více kontejnerech":::

Operace obnovení zobrazená na obrázku provádí následující akce:

- Obnoví úplný obsah *container1*.
- Obnoví objekty BLOB v rozsahu lexicographical *blob1* prostřednictvím *blob5* v *container2*. Tento rozsah obnoví objekty BLOB s názvy, například *blob1*, *blob11*, *blob100*, *blob2*a tak dále. Vzhledem k tomu, že konec rozsahu je exkluzivní, obnoví objekty blob, jejichž názvy začínají na *blob4*, ale neobnoví objekty blob, jejichž názvy začínají na *blob5*.
- Obnoví všechny objekty BLOB v *container3* a *container4*. Vzhledem k tomu, že konec rozsahu je exkluzivní, tento rozsah neobnoví *container5*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Chcete-li obnovit jednu škálu objektů blob, zavolejte příkaz **Restore-AzStorageBlobRange** a zadejte lexicographical rozsah kontejnerů a názvů objektů BLOB pro tento `-BlobRestoreRange` parametr. Chcete-li například obnovit objekty BLOB v jednom kontejneru s názvem *Sample-Container*, můžete zadat rozsah, který začíná *vzorkem-Container* a končí na *Sample-container1*. Neexistuje žádný požadavek na to, aby kontejnery s názvem v rozsahu začátek a konec existovaly. Vzhledem k tomu, že konec rozsahu je exkluzivní, i když účet úložiště obsahuje kontejner s názvem *Sample-container1*, obnoví se jenom kontejner s názvem *Sample-Container* :

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container `
    -EndRange sample-container1
```

Chcete-li určit podmnožinu objektů BLOB v kontejneru pro obnovení, použijte lomítko (/) k oddělení názvu kontejneru od vzoru předpony objektů BLOB. Například následující rozsah vybírá objekty BLOB v jednom kontejneru, jejichž názvy začínají písmeny *d* až *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d `
    -EndRange sample-container/g
```

Dále zadejte rozsah příkazu **Restore-AzStorageBlobRange** . Zadejte bod obnovení zadáním hodnoty **DateTime** UTC pro `-TimeToRestore` parametr. Následující příklad obnoví objekty BLOB v zadaném rozsahu do jejich stavu 3 dny před současným okamžikem:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

Chcete-li obnovit více rozsahů objektů blob bloku, zadejte pole rozsahů pro `-BlobRestoreRange` parametr. Následující příklad určuje dva rozsahy pro obnovení kompletního obsahu *container1* a *container4*:

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

---

### <a name="restore-block-blobs-asynchronously-with-powershell"></a>Asynchronní obnovení objektů blob bloku pomocí PowerShellu

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

Pro podmnožinu operací obnovení, kde jsou k dispozici doplňovací objekty blob, operace obnovení selže. Microsoft doporučuje, abyste během období Preview neprováděli obnovení k určitému bodu v čase, pokud se v účtu nacházejí objekty blob pro přidávání.

## <a name="next-steps"></a>Další kroky

- [Obnovení bodu v čase pro objekty blob bloku (Preview)](point-in-time-restore-overview.md)
- [Obnovitelné odstranění](soft-delete-overview.md)
- [Změnit kanál (Preview)](storage-blob-change-feed.md)
- [Správa verzí objektů BLOB](versioning-overview.md)
