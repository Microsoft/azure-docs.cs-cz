---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f30518c3bfc9876cbddaf8295ff9e8b667a70200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74014557"
---
## <a name="overview"></a>Přehled
Azure Storage poskytuje možnost pořizovat snímky objektů BLOB. Snímky zachycují stav objektu blob v tomto okamžiku. V tomto článku popisujeme scénář, ve kterém můžete udržovat zálohy disků virtuálních počítačů pomocí snímků. Tuto metodiku můžete použít, pokud se rozhodnete nepoužívat službu Azure Backup and Recovery Service a chcete vytvořit vlastní strategii zálohování pro disky virtuálních počítačů.

Disky virtuálních počítačů Azure se ve službě Azure Storage ukládají jako objekty BLOB stránky. Vzhledem k tomu, že popisujeme strategii zálohování pro disky virtuálních počítačů v tomto článku, odkazujeme na snímky v kontextu objektů BLOB stránky. Další informace o snímky, naleznete v části [Vytvoření snímek objektu blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

## <a name="what-is-a-snapshot"></a>Co je snímek?
Snímek objektu blob je verze objektu blob jen pro čtení, který je zachycen v určitém okamžiku. Po vytvoření snímku jej lze číst, kopírovat nebo odstraňovat, ale nelze jej upravovat. Snímky poskytují způsob, jak zálohovat objekt blob, jak se zobrazí v okamžiku v čase. Až do rest verze 2015-04-05, měli jste možnost kopírovat úplné snímky. S rest verze 2015-07-08 a vyšší, můžete také kopírovat přírůstkové snímky.

## <a name="full-snapshot-copy"></a>Úplná kopie snímku
Snímky lze zkopírovat do jiného účtu úložiště jako objekt blob zachovat zálohy základní objekt blob. Můžete také zkopírovat snímek přes jeho základní objekt blob, což je jako obnovení objektu blob na starší verzi. Když snímek je zkopírován z jednoho účtu úložiště do jiného, zabírá stejné místo jako objekt blob základní stránky. Proto kopírování celé snímky z jednoho účtu úložiště do jiného je pomalé a spotřebovává mnoho místa v účtu cílovéúložiště.

> [!NOTE]
> Pokud zkopírujete základní objekt blob do jiného cíle, snímky objektu blob nejsou zkopírovány spolu s ním. Podobně pokud přepíšete základní objekt blob kopií, snímky přidružené k základnímu objektu blob nebudou ovlivněny a zůstanou neporušené pod názvem základního objektu blob.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Zálohování disků pomocí snímků
Jako strategie zálohování disků virtuálních počítačů můžete pořizovat periodické snímky objektu blob disku nebo stránky a kopírovat je do jiného účtu úložiště pomocí nástrojů, jako je kopírování operace [blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) nebo [AzCopy](../articles/storage/common/storage-use-azcopy.md). Snímek můžete zkopírovat do objektu blob cílové stránky s jiným názvem. Výsledný objekt blob cílové stránky je objekt blob zapisovatelné stránky a není snímek. Dále v tomto článku popisujeme kroky k zálohování disků virtuálních počítačů pomocí snímků.

### <a name="restore-disks-using-snapshots"></a>Obnovení disků pomocí snímků
Když je čas obnovit disk na stabilní verzi, která byla dříve zachycena v jednom ze snímků zálohy, můžete zkopírovat snímek přes objekt blob základní stránky. Po snímek je povýšen na základní objekt blob stránky, snímek zůstane, ale jeho zdroj je přepsán a kopie, která může být čtení i zápis. Dále v tomto článku popisujeme kroky k obnovení předchozí verze disku z jeho snímku.

### <a name="implementing-full-snapshot-copy"></a>Implementace kopie celého snímku
Můžete implementovat celý snímek kopie pomocí následujících,

* Nejprve pořizovat snímek základní objekt blob pomocí operace [objektu blob snímek.](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob)
* Potom zkopírujte snímek do cílového účtu úložiště pomocí [kopírovat objekt blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob).
* Tento postup opakujte, chcete-li zachovat záložní kopie základního objektu blob.

## <a name="incremental-snapshot-copy"></a>Přírůstková kopie snímku
Nová funkce v rozhraní [API GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) poskytuje mnohem lepší způsob, jak zálohovat snímky objektů BLOB stránky nebo disků. Rozhraní API vrátí seznam změn mezi základním objektem blob a snímky, což snižuje množství úložného prostoru používaného v účtu zálohování. Rozhraní API podporuje objekty BLOB stránky v úložišti Premium i standard storage. Pomocí tohoto rozhraní API můžete vytvářet rychlejší a efektivnější řešení zálohování pro virtuální počítače Azure. Toto rozhraní API bude k dispozici s verzí REST 2015-07-08 a vyšší.

Přírůstková kopie snímků umožňuje kopírovat z jednoho účtu úložiště do druhého rozdíl mezi,

* Základní objekt blob a jeho snímek NEBO
* Všechny dva snímky základního objektu blob

Za předpokladu, že jsou splněny následující podmínky,

* Objekt blob byl vytvořen na Jan-1-2016 nebo novější.
* Objekt blob nebyl přepsán [objektem blob PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) nebo [Kopírovat](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) mezi dvěma snímky.

**Poznámka:** Tato funkce je k dispozici pro objekty Blobs stránky Premium a Standard Azure.

Pokud máte vlastní strategii zálohování pomocí snímků, kopírování snímků z jednoho účtu úložiště do jiného může být pomalé a může spotřebovat mnoho úložného prostoru. Namísto kopírování celého snímku do účtu úložiště zálohy můžete zapsat rozdíl mezi po sobě jdoucími snímky do objektu blob stránky zálohy. Tímto způsobem je podstatně zkrácenčas kopírování a prostor pro ukládání záloh.

### <a name="implementing-incremental-snapshot-copy"></a>Implementace přírůstkové kopie snímku
Přírůstkovou kopii snímku můžete implementovat následujícím způsobem,

* Pořizovat snímek základní objekt blob pomocí [snímek objektu blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob).
* Zkopírujte snímek do cílového účtu úložiště zálohy ve stejné nebo jiné oblasti Azure pomocí [kopírovat objekt blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob). Toto je objekt blob záložní stránky. Pořizovat snímek objektu blob stránky zálohování a uložit jej do účtu zálohování.
* Pořizovat další snímek základní objekt blob pomocí objektu blob snímek.
* Získejte rozdíl mezi prvním a druhým snímky základního objektu blob pomocí [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges). Pomocí nového parametru **prevsnapshot**, chcete-li zadat DateTime hodnotu snímek, který chcete získat rozdíl s. Pokud je tento parametr k dispozici, odpověď REST obsahuje pouze stránky, které byly změněny mezi cílový snímek a předchozí snímek včetně vymazat stránky.
* Pomocí [putpage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) použít tyto změny na objekt blob záložní stránky.
* Nakonec pořizovat snímek objektu blob stránky zálohování a uložit jej do účtu úložiště zálohování.

V další části podrobněji popíšeme, jak můžete udržovat zálohy disků pomocí přírůstkové kopie snímků

## <a name="scenario"></a>Scénář
V této části popisujeme scénář, který zahrnuje vlastní strategii zálohování pro disky virtuálních počítačů pomocí snímků.

Zvažte virtuální počítač Azure řady DS s připojeným diskem P30 s prémiovým úložištěm. Disk P30 s názvem *mypremiumdisk* je uložen v účtu premium storage s názvem *mypremiumaccount*. Standardní účet úložiště s názvem *mybackupstdaccount* se používá pro ukládání zálohy *mypremiumdisk*. Rádi bychom, aby snímek *mypremiumdisk* každých 12 hodin.

Další informace o vytvoření účtu úložiště najdete [v tématu Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

Další informace o zálohování virtuálních počítačů Azure najdete v [plánu zálohování virtuálních počítačů Azure](../articles/backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Postup údržby záloh disku pomocí přírůstkových snímků
Následující kroky popisují, jak pořizovat snímky *mypremiumdisk* a udržovat zálohy v *mybackupstdaccount*. Záloha je standardní objekt blob stránky s názvem *mybackupstdpageblob*. Objekt blob stránky zálohy vždy odráží stejný stav jako poslední snímek *mypremiumdisk*.

1. Vytvořte objekt blob stránky zálohování pro disk úložiště premium tak, že pořizovat snímek *mypremiumdisk* s názvem *mypremiumdisk_ss1*.
2. Zkopírujte tento snímek do mybackupstdaccount jako objekt blob stránky s názvem *mybackupstdpageblob*.
3. Pořizovat snímek *mybackupstdpageblob* s názvem *mybackupstdpageblob_ss1*, pomocí [snímek blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) a uložit jej do *mybackupstdaccount*.
4. Během okna zálohování vytvořte další snímek *mypremiumdisk*, řekněme *mypremiumdisk_ss2*a uložte jej do *mypremiumaccount*.
5. Získejte přírůstkové změny mezi dvěma snímky, *mypremiumdisk_ss2* a *mypremiumdisk_ss1*, pomocí [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) na *mypremiumdisk_ss2* s **parametrem prevsnapshot** nastaveným na časové razítko *mypremiumdisk_ss1*. Tyto přírůstkové změny do objektu blob stránky zálohování *mybackupstdpageblob* v *mybackupstdaccount*. Pokud jsou odstraněny rozsahy v přírůstkové změny, musí být vymazány z objektu blob záložní stránky. Pomocí [služby PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) zapisujte přírůstkové změny do objektu blob záložní stránky.
6. Pořizovat snímek objektu blob stránky zálohování *mybackupstdpageblob*, volal *mybackupstdpageblob_ss2*. Odstraňte předchozí snímek *mypremiumdisk_ss1* z účtu úložiště premium.
7. Opakujte kroky 4-6 každé okno zálohování. Tímto způsobem můžete udržovat zálohy *mypremiumdisk* ve standardním účtu úložiště.

![Zálohování disku pomocí přírůstkových snímků](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Postup obnovení disku ze snímků
Následující kroky popisují, jak obnovit disk premium, *mypremiumdisk* na dřívější snímek z účtu úložiště zálohování *mybackupstdaccount*.

1. Určete bod v čase, do kterého chcete obnovit disk premium. Řekněme, že je snímek *mybackupstdpageblob_ss2*, který je uložen v účtu úložiště zálohování *mybackupstdaccount*.
2. V mybackupstdaccount, podporovat snímek *mybackupstdpageblob_ss2* jako nový objekt blob základní zálohy *mybackupstdpageblobrestored*.
3. Pořizovat snímek tohoto obnoveného objektu blob stránky zálohování, který se nazývá *mybackupstdpageblobrestored_ss1*.
4. Zkopírujte obnovenou stránku blob *mybackupstdpageblobrestored* z *mybackupstdaccount* na *mypremiumaccount* jako nový disk premium *mypremiumdiskobnovena*.
5. Pořiďte snímek *mypremiumdiskrestored*, volal *mypremiumdiskrestored_ss1* pro vytváření budoucích přírůstkové zálohy.
6. Najedovat virtuální ms řady DS na obnovený disk *mypremiumdiskrestored* a odpojit starý *mypremiumdisk* z virtuálního počítače.
7. Začněte proces zálohování popsané v předchozí části pro obnovený disk *mypremiumdiskrestored*, pomocí *mybackupstdpageblobreuložena* jako objekt blob záložní stránky.

![Obnovení disku ze snímků](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Další kroky
Pomocí následujících odkazů se dozvíte další informace o vytváření snímků objektu blob a plánování infrastruktury zálohování virtuálních počítačích.

* [Vytvoření snímku objektu blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [Plánování infrastruktury zálohování virtuálních počítačů](../articles/backup/backup-azure-vms-introduction.md)

