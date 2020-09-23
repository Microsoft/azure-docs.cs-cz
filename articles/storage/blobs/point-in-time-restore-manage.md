---
title: Provedení obnovení k určitému bodu v čase u dat objektů blob bloku
titleSuffix: Azure Storage
description: Naučte se používat obnovení k určitému bodu v čase k obnovení sady objektů blob bloku do jejich předchozího stavu v daném časovém okamžiku.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/18/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 226e35452e4b266c3c0a698505d47ab9a53b9761
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90984385"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>Provedení obnovení k určitému bodu v čase u dat objektů blob bloku

Obnovení k určitému bodu v čase můžete použít k obnovení jedné nebo více sad objektů blob bloku do předchozího stavu. Tento článek popisuje, jak povolit obnovení k určitému bodu v čase pro účet úložiště a jak provést operaci obnovení.

Další informace o obnovení k bodu v čase najdete v tématu [obnovení k bodu v čase pro objekty blob bloku](point-in-time-restore-overview.md).

> [!CAUTION]
> Obnovení k bodu v čase podporuje pouze obnovení operací pouze pro objekty blob bloku. Operace na kontejnerech nelze obnovit. Pokud odstraníte kontejner z účtu úložiště voláním operace [odstranění kontejneru](/rest/api/storageservices/delete-container) , nelze tento kontejner obnovit pomocí operace obnovení. Místo odstranění kontejneru odstraňte jednotlivé objekty blob, pokud je budete chtít obnovit.

## <a name="enable-and-configure-point-in-time-restore"></a>Povolit a nakonfigurovat obnovení k časovému okamžiku

Než povolíte a nakonfigurujete obnovení k bodu v čase, povolte jeho požadavky pro účet úložiště: Obnovitelné odstranění, změna kanálu a správa verzí objektů BLOB. Další informace o povolení jednotlivých funkcí najdete v těchto článcích:

- [Povolení obnovitelného odstranění pro objekty blob](soft-delete-enable.md)
- [Povolení a zákaz kanálu změn](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Povolení a správa verzí objektů BLOB](versioning-enable.md)

> [!IMPORTANT]
> Povolení obnovitelného odstranění, změny kanálu a správy verzí objektů BLOB může mít za následek další poplatky. Další informace najdete v tématech [obnovitelné odstranění pro objekty blob](soft-delete-blob-overview.md), [Podpora změn kanálu v Azure Blob Storage](storage-blob-change-feed.md)a [Správa verzí objektů BLOB](versioning-overview.md).

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

Pokud chcete nakonfigurovat obnovení k bodu v čase pomocí PowerShellu, nejdřív nainstalujte modul [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) verze 2.6.0 nebo novější. Pak zavolejte příkaz Enable-AzStorageBlobRestorePolicy a povolte tak obnovení k bodu v čase pro účet úložiště.

Následující příklad umožňuje obnovitelné odstranění a nastavuje dobu uchování s mírným odstraněním, povoluje kanál změn a pak umožňuje obnovení k určitému bodu v čase. Před spuštěním tohoto příkladu použijte Azure Portal nebo šablonu Azure Resource Manager, abyste mohli taky povolit správu verzí objektů BLOB.

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

Obnoveny jsou pouze objekty blob bloku. Objekty blob stránky a doplňovací objekty blob nejsou zahrnuté do operace obnovení. Další informace o omezeních vztahujících se na doplňovací objekty blob najdete v tématu [obnovení k bodu v čase pro objekty blob bloku](point-in-time-restore-overview.md).

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

Pokud chcete obnovit všechny kontejnery a objekty BLOB v účtu úložiště pomocí PowerShellu, zavolejte příkaz **Restore-AzStorageBlobRange** . Ve výchozím nastavení se příkaz **Restore-AzStorageBlobRange** spustí asynchronně a vrátí objekt typu **PSBlobRestoreStatus** , který můžete použít ke kontrole stavu operace obnovení.

Následující příklad asynchronně obnoví kontejnery v účtu úložiště do stavu 12 hodin před současným okamžikem a zkontroluje některé vlastnosti operace obnovení:

```powershell
# Specify -TimeToRestore as a UTC value
$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the restore point in UTC time.
$restoreOperation.Parameters.TimeToRestore
```

Chcete-li spustit operaci obnovení synchronně, zahrňte do příkazu parametr **-WaitForComplete** . Když je přítomen parametr **-WaitForComplete** , PowerShell zobrazí zprávu, která obsahuje ID obnovení pro operaci, a pak zablokuje spuštění, dokud se nedokončí operace obnovení. Mějte na paměti, že doba potřebná pro operaci obnovení závisí na množství dat, která se mají obnovit, a dokončení velkých operací obnovení může trvat až hodinu.

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
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

Chcete-li obnovit jednu škálu objektů blob, zavolejte příkaz **Restore-AzStorageBlobRange** a zadejte lexicographical rozsah kontejnerů a názvů objektů BLOB pro tento `-BlobRestoreRange` parametr. Chcete-li například obnovit objekty BLOB v jednom kontejneru s názvem *container1*, můžete zadat rozsah, který začíná na *container1* a končí na *container2*. Neexistuje žádný požadavek na to, aby kontejnery s názvem v rozsahu začátek a konec existovaly. Vzhledem k tomu, že konec rozsahu je exkluzivní, i když účet úložiště obsahuje kontejner s názvem *container2*, obnoví se jenom kontejner s názvem *container1* :

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

Chcete-li určit podmnožinu objektů BLOB v kontejneru pro obnovení, použijte lomítko (/) k oddělení názvu kontejneru od vzoru předpony objektů BLOB. Například následující rozsah vybírá objekty BLOB v jednom kontejneru, jejichž názvy začínají písmeny *d* až *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

Dále zadejte rozsah příkazu **Restore-AzStorageBlobRange** . Zadejte bod obnovení zadáním hodnoty **DateTime** UTC pro `-TimeToRestore` parametr. Následující příklad obnoví objekty BLOB v zadaném rozsahu do jejich stavu 3 dny před současným okamžikem:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

Ve výchozím nastavení se příkaz **Restore-AzStorageBlobRange** spustí asynchronně. Když operaci obnovení inicializujete asynchronně, PowerShell okamžitě zobrazí tabulku vlastností pro tuto operaci:  

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

Chcete-li obnovit více rozsahů objektů blob bloku, zadejte pole rozsahů pro `-BlobRestoreRange` parametr. Následující příklad určuje dva rozsahy pro obnovení kompletního obsahu *container1* a *container4* před jejich stavem 24 hodin a výsledek uloží do proměnné:

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-24) `
    -BlobRestoreRange @($range1, $range2)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the blob ranges specified for the operation.
$restoreOperation.Parameters.BlobRanges
```

Chcete-li spustit operaci obnovení synchronně a zablokovat spuštění, dokud není dokončeno, do příkazu zadejte parametr **-WaitForComplete** .

---

## <a name="next-steps"></a>Další kroky

- [Obnovení bodu v čase pro objekty blob bloku](point-in-time-restore-overview.md)
- [Obnovitelné odstranění](soft-delete-overview.md)
- [Změnit kanál](storage-blob-change-feed.md)
- [Správa verzí objektů BLOB](versioning-overview.md)
