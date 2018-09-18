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
ms.openlocfilehash: 88a9348ea7d6282b7410d5a323fd482dc82416c6
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45979663"
---
# <a name="back-up-azure-unmanaged-vm-disks-with-incremental-snapshots"></a>Zálohování Azure nespravovaných disků virtuálních počítačů s přírůstkovými snímky
## <a name="overview"></a>Přehled
Azure Storage poskytuje možnost pořizovat snímky objektů BLOB. Snímky zachycují stav objektů blob v tomto okamžiku v čase. V tomto článku popisujeme scénář, ve kterém můžete udržovat zálohy disků virtuálních počítačů pomocí funkce vytváření snímků. Zvolit nepoužívání Azure Backup a obnovení služby, a chcete vytvořit vlastní strategii zálohování pro disky virtuálních počítačů můžete použít tuto metodu.

Disky virtuálních počítačů Azure se ukládají jako objekty BLOB stránky ve službě Azure Storage. Protože strategie zálohování pro disky virtuálních počítačů v tomto článku jsme se s popisem, označujeme snímky v souvislosti s objekty BLOB stránky. Další informace o snímcích, najdete v tématu [vytvoření snímku objektu Blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

## <a name="what-is-a-snapshot"></a>Co je snímek?
Snímek objektu blob je verze jen pro čtení objektu blob, která je zachycena v bodě v čase. Po vytvoření snímku, lze je číst, zkopírovat, nebo odstranit, ale nedojde ke změně. Snímky poskytují způsob, jak zálohovat objekt blob, jak se zobrazuje v okamžiku v čase. Až do verze 2015-04-05 REST máte možnost Kopírovat úplnou snímky. S využitím REST verze 2015-07-08 a vyšší, můžete také zkopírovat přírůstkových snímků.

## <a name="full-snapshot-copy"></a>Kopírovat úplnou snímku
Snímky je možné zkopírovat do jiného účtu úložiště jako objekt blob Udržovat zálohy základní objekt blob. Můžete také zkopírovat snímek nad jeho základní objektu blob, který je třeba obnovení objektu blob na starší verzi. Snímek je zkopírován z jednoho účtu úložiště do jiného, zabírá na stejném místě jako objekty blob základní stránky. Proto kopírování celé snímky z jednoho účtu úložiště je pomalé a spotřebuje, kolik místa v cílovém účtu úložiště.

> [!NOTE]
> Pokud základní objekt blob zkopírujete do jiného umístění, nejsou zkopírovány snímek objektu blob společně. Podobně pokud přepsat základní objekt blob se kopie, snímky přidružené tomuto základní objekt blob to nebude mít vliv a zůstanou beze změny v části název základního objektu blob.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Zálohování disků pomocí funkce vytváření snímků
Jako strategie zálohování pro disky virtuálních počítačů, může trvat pravidelné snímky objektů blob disku nebo stránek a zkopírujte je do jiného úložiště účtu pomocí nástrojů jako [objekt Blob kopírování](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) operace nebo [AzCopy](../articles/storage/common/storage-use-azcopy.md). Můžete zkopírovat snímek do cílové objektů blob stránky s jiným názvem. Výsledný objekt blob stránky cíl je objekt blob zapisovat stránky a ne snímku. Dále v tomto článku popisujeme kroky pro zálohování disků virtuálních počítačů pomocí funkce vytváření snímků.

### <a name="restore-disks-using-snapshots"></a>Obnovit disky pomocí snímků
Až nastane čas na obnovení vašeho disku stabilní verzi, která byla dříve zaznamenaný v jednom ze snímků zálohy, můžete zkopírovat snímek objektu blob základní stránky. Po snímku je povýšen na základní stránce objektů blob, zůstane snímku, ale její zdroj je přepsána jeho kopii, může být jak číst a zapisovat. Dále v tomto článku popisujeme kroků k obnovení předchozí verze disku z jeho snímků.

### <a name="implementing-full-snapshot-copy"></a>Implementace kopie úplné snímku
Kopírovat úplnou snímek můžete implementovat pomocí tohoto postupu,

* Nejprve vytvořte snímek základní objekt blob pomocí [pořízení snímku objektu Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) operace.
* Potom zkopírujte snímku cílového účtu úložiště pomocí [objekt Blob kopírování](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob).
* Opakujte tento postup a udržování záložní kopie základní objekt blob.

## <a name="incremental-snapshot-copy"></a>Kopírování přírůstkový snímek
Nová funkce v [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) rozhraní API poskytuje mnohem lepší způsob, jak zálohovat snímky objektů BLOB stránky a disky. Rozhraní API vrátí seznam změn mezi základní objekt blob a snímky, což snižuje množství prostoru úložiště používaný u účtu zálohování. Rozhraní API podporuje objekty BLOB stránky úrovně Premium Storage, jakož i úložiště úrovně Standard. Pomocí tohoto rozhraní API, můžete vytvořit rychlejší a efektivnější řešení zálohování pro virtuální počítače Azure. Toto rozhraní API bude k dispozici v REST verzi 2015-07-08 a vyšší.

Přírůstkové kopírování snímku umožňuje kopírování z jednoho účtu úložiště do jiného rozdíl mezi,

* Základní objekt blob a jeho snímek nebo
* Žádné dva snímky základní objekt blob

Za předpokladu, že jsou splněny následující podmínky,

* Objekt blob byl vytvořen v 1. ledna 2016 nebo novějším.
* Objekt blob se přepíše s [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) nebo [objekt Blob kopírování](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) mezi dvěma snímky.

**Poznámka:**: Tato funkce je dostupná pro úrovně Premium a Standard objekty BLOB stránky Azure.

Až budete mít vlastní strategii zálohování pomocí snímků, kopírování snímky z jednoho účtu úložiště do jiného může trvat dlouho a může využívat velké úložiště. Místo kopírování celý snímek do účtu úložiště pro zálohování, můžete napsat rozdíl mezi po sobě jdoucích snímků objektů blob zálohování stránky. Tímto způsobem je podstatně snížit čas kopírování a místa pro ukládání záloh.

### <a name="implementing-incremental-snapshot-copy"></a>Implementace kopírování přírůstkový snímek
Přírůstkový snímek kopírování můžete implementovat pomocí tohoto postupu,

* Pořízení snímku základní objekt blob pomocí [pořízení snímku objektu Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob).
* Zkopírování snímku do účtu úložiště pro zálohování cílové pomocí [objekt Blob kopírování](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob). Toto je objekty blob stránky zálohování. Pořízení snímku objektu blob záložní stránky a uložte ho do účtu zálohování.
* Vytvořte nový snímek objektu blob základní pomocí vytvoření snímku objektu Blob.
* Získat rozdíl mezi první a druhý snímek základní objekt blob pomocí [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges). Nový parametr **prevsnapshot**, určete hodnotu Datum a čas snímku chcete zobrazit rozdíl oproti. Pokud tento parametr je k dispozici, odpověď REST obsahuje pouze stránky, které byly změněny mezi cílové snímek a předchozí snímek včetně vymazat stránek.
* Použití [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) tyto změny projeví na objekty blob stránky zálohování.
* Nakonec pořízení snímku objektu blob záložní stránky a uložte ho do účtu úložiště pro zálohování.

V další části bude popisujeme podrobněji jak díky čemuž můžete udržovat zálohy disků pomocí přírůstkového kopírování snímku

## <a name="scenario"></a>Scénář
V této části popisujeme scénář, který zahrnuje vlastní strategii zálohování pro virtuální počítače s disky pomocí snímků.

Vezměte v úvahu virtuálního počítače Azure řady DS-series s P30 disk úložiště úrovně premium připojen. Volá disků P30 *mypremiumdisk* je uložený v účtu služby premium storage volá *mypremiumaccount*. Účet úložiště úrovně standard s názvem *mybackupstdaccount* se použije k uložení zálohování *mypremiumdisk*. Rádi bychom se zachovat snímek *mypremiumdisk* každých 12 hodin.

Další informace o vytvoření účtu úložiště najdete v tématu [vytvořit účet úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

Další informace o zálohování virtuálních počítačů Azure, najdete v tématu [záloh virtuálních počítačů Azure plánování](../articles/backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Postup Udržovat zálohy disku pomocí přírůstkových snímků
Následující kroky popisují, jak pořizovat snímky *mypremiumdisk* a Udržovat zálohy v *mybackupstdaccount*. Zálohování je objekt blob standardní stránky volá *mybackupstdpageblob*. Objekty blob stránky zálohování vždy odráží stejného stavu jako poslední snímek *mypremiumdisk*.

1. Objekty blob stránky zálohování pro váš disk storage úrovně premium s vytvořením pořízení snímku *mypremiumdisk* volá *mypremiumdisk_ss1*.
2. Zkopírujte tento snímek do mybackupstdaccount jako objekt blob stránky, volá *mybackupstdpageblob*.
3. Pořízení snímku *mybackupstdpageblob* volá *mybackupstdpageblob_ss1*s použitím [pořízení snímku objektu Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) a uložit ho v *mybackupstdaccount*.
4. Během zálohování, vytvořte další snímek *mypremiumdisk*, Dejme tomu, že *mypremiumdisk_ss2*a ukládá je v *mypremiumaccount*.
5. Získat přírůstkové změny mezi dvěma snímky *mypremiumdisk_ss2* a *mypremiumdisk_ss1*s použitím [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) na *mypremiumdisk_ ss2* s **prevsnapshot** parametr nastaven na časové razítko *mypremiumdisk_ss1*. Zápis těchto přírůstkové změny pro objekty blob stránky zálohování *mybackupstdpageblob* v *mybackupstdaccount*. Pokud v přírůstkové změny nejsou odstraněné rozsahy, musí být zrušeno z objektu blob záložní stránky. Použití [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) zapisovat do objektů blob zálohování stránky přírůstkové změny.
6. Vytvoření snímku objektu blob záložní stránky *mybackupstdpageblob*, označované jako *mybackupstdpageblob_ss2*. Odstranit předchozí snímek *mypremiumdisk_ss1* z účtu služby premium storage.
7. Opakujte kroky 4 až 6 každý interval zálohování. Tímto způsobem, díky čemuž můžete udržovat zálohy *mypremiumdisk* v účtu úložiště úrovně standard.

![Zálohování disků pomocí přírůstkových snímků](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Postup obnovení ze snímků disku
Následující kroky popisují, jak obnovit premium disk *mypremiumdisk* na předchozí snímek z účtu úložiště pro zálohování *mybackupstdaccount*.

1. Identifikujte bod v čase, který chcete obnovit disk úrovně premium. Řekněme, že je snímek *mybackupstdpageblob_ss2*, který je uložený v účtu úložiště pro zálohování *mybackupstdaccount*.
2. V mybackupstdaccount, zvýšit úroveň snímku *mybackupstdpageblob_ss2* jako nový objekt blob zálohování základní stránky *mybackupstdpageblobrestored*.
3. Pořízení snímku tento objekt blob stránky obnovené zálohy, volá *mybackupstdpageblobrestored_ss1*.
4. Kopírovat objekt blob stránky obnovené *mybackupstdpageblobrestored* z *mybackupstdaccount* k *mypremiumaccount* jako nový disk premium  *mypremiumdiskrestored*.
5. Pořízení snímku *mypremiumdiskrestored*, označované jako *mypremiumdiskrestored_ss1* pro provádění budoucích přírůstkové zálohování.
6. Přejděte na virtuální počítač řady DS obnoveného disku *mypremiumdiskrestored* a odpojit starého *mypremiumdisk* z virtuálního počítače.
7. Proces zálohování je popsáno v předchozí části pro obnovený disk *mypremiumdiskrestored*, použije *mybackupstdpageblobrestored* jako objekty blob stránky zálohování.

![Obnovení ze snímků disku](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Další kroky
Další informace o vytváření snímky objektů blob a plánování infrastruktury zálohování virtuálních počítačů pomocí následujících odkazů.

* [Vytváří se snímek objektu Blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [Plánování infrastruktury zálohování virtuálních počítačů](../articles/backup/backup-azure-vms-introduction.md)

