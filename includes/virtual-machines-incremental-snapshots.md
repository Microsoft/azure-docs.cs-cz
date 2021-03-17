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
ms.openlocfilehash: a662598efdca05769c7da9fbeecdf692dccdacb5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025643"
---
## <a name="overview"></a>Přehled
Azure Storage poskytuje možnost pořizovat snímky objektů BLOB. Snímky zachycují stav objektu BLOB v daném časovém okamžiku. V tomto článku popisujeme scénář, ve kterém můžete uchovávat zálohy disků virtuálních počítačů pomocí snímků. Tuto metodologii můžete použít, pokud se rozhodnete nepoužívat Azure Backup a službu Recovery Services a chcete vytvořit vlastní strategii zálohování pro disky virtuálních počítačů. Pro virtuální počítače, na kterých běží firemní nebo důležité úlohy, se doporučuje použít [Azure Backup](../articles/backup/backup-azure-vms-introduction.md) jako součást strategie zálohování.  

Disky virtuálních počítačů Azure se ukládají jako objekty blob stránky v Azure Storage. Vzhledem k tomu, že popisujeme strategii zálohování disků virtuálních počítačů v tomto článku, odkazujeme na snímky v kontextu objektů blob stránky. Další informace o snímcích najdete v tématu [vytvoření snímku objektu BLOB](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

## <a name="what-is-a-snapshot"></a>Co je snímek?
Snímek objektu BLOB je verze objektu BLOB jen pro čtení, která je zachycena v určitém časovém okamžiku. Po vytvoření snímku je možné ho číst, kopírovat nebo odstranit, ale nemění se. Snímky poskytují způsob, jak zálohovat objekt blob, jak se zobrazuje v okamžiku v čase. Až do REST verze 2015-04-05 jste měli možnost Kopírovat úplné snímky. S verzí REST 2015-07-08 a vyšší můžete také kopírovat přírůstkové snímky.

## <a name="full-snapshot-copy"></a>Úplná kopie snímku
Snímky se dají zkopírovat do jiného účtu úložiště jako objekt blob, abyste zachovali zálohy základního objektu BLOB. Snímek můžete také zkopírovat pomocí svého základního objektu blob, který je jako obnovení objektu blob do starší verze. Když se snímek zkopíruje z jednoho účtu úložiště do jiného, zabírá stejný prostor jako objekt BLOB základní stránky. Proto se kopírování celých snímků z jednoho účtu úložiště do druhého zpomaluje a v cílovém účtu úložiště spotřebovává mnoho místa.

> [!NOTE]
> Pokud zkopírujete základní objekt blob do jiného cíle, snímky objektu BLOB se spolu s ním nekopírují. Podobně platí, že pokud přepíšete základní objekt BLOB s kopií, nebudou ovlivněny snímky přidružené k základnímu objektu BLOB a zůstanou beze změny v základním názvu objektu BLOB.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Zálohování disků pomocí snímků
Jako strategii zálohování pro disky virtuálních počítačů můžete provádět pravidelné snímky objektu nebo objektu blob stránky a kopírovat je do jiného účtu úložiště pomocí nástrojů, jako je operace [kopírování objektů BLOB](/rest/api/storageservices/Copy-Blob) nebo [AzCopy](../articles/storage/common/storage-use-azcopy-v10.md). Snímek můžete zkopírovat do objektu BLOB cílové stránky s jiným názvem. Výsledný objekt BLOB cílové stránky je zapisovatelný objekt blob stránky, nikoli snímek. Později v tomto článku popisujeme kroky pro zálohování disků virtuálních počítačů pomocí snímků.

### <a name="restore-disks-using-snapshots"></a>Obnovení disků pomocí snímků
Pokud je čas obnovit disk do stabilní verze, která byla dříve zachycena v jednom ze záložních snímků, můžete zkopírovat snímek přes objekt BLOB základní stránky. Po zvýšení úrovně snímku na základní objekt blob stránky zůstane snímek, ale jeho zdroj přepíše kopii, kterou lze číst i zapsat. Později v tomto článku popisujeme postup obnovení předchozí verze disku z jeho snímku.

### <a name="implementing-full-snapshot-copy"></a>Implementace úplné kopie snímku
Úplnou kopii snímku můžete implementovat následujícím způsobem:

* Nejprve pořídit snímek základního objektu BLOB pomocí operace [Snapshot BLOB](/rest/api/storageservices/Snapshot-Blob) .
* Pak zkopírujte snímek do cílového účtu úložiště pomocí [objektu BLOB kopírování](/rest/api/storageservices/Copy-Blob).
* Tento postup opakujte, pokud chcete zachovat záložní kopie základního objektu BLOB.

## <a name="incremental-snapshot-copy"></a>Přírůstková kopie snímku
Nová funkce v rozhraní [GetPageRanges](/rest/api/storageservices/Get-Page-Ranges) API poskytuje mnohem lepší způsob, jak zálohovat snímky objektů blob stránky nebo disků. Rozhraní API vrátí seznam změn mezi základním objektem BLOB a snímky, což snižuje velikost prostoru úložiště použitého v účtu zálohy. Rozhraní API podporuje objekty blob stránky na Premium Storage i úložiště Standard. Pomocí tohoto rozhraní API můžete vytvářet rychlejší a efektivnější řešení zálohování pro virtuální počítače Azure. Toto rozhraní API bude k dispozici ve verzi REST 2015-07-08 a vyšší.

Přírůstková kopie snímku umožňuje kopírovat z jednoho účtu úložiště na jiný rozdíl mezi,

* Základní objekt BLOB a jeho snímek nebo
* Všechny dva snímky základního objektu BLOB

Pokud jsou splněné následující podmínky,

* Objekt BLOB byl vytvořen v lednu-1-2016 nebo novějším.
* Objekt BLOB nebyl přepsán pomocí [PutPage](/rest/api/storageservices/Put-Page) nebo [objektu BLOB kopírování](/rest/api/storageservices/Copy-Blob) mezi dvěma snímky.

>[!NOTE]
>Tato funkce je dostupná pro objekty blob stránky Azure úrovně Premium a Standard.

Když máte vlastní strategii zálohování pomocí snímků, může být kopírování snímků z jednoho účtu úložiště do jiného pomalé a může spotřebovat mnohem úložné místo. Místo zkopírování celého snímku do účtu úložiště pro zálohování můžete rozdíl mezi po sobě jdoucí snímky zapsat do objektu BLOB záložní stránky. Tímto způsobem se výrazně sníží doba kopírování a místo pro uložení záloh.

### <a name="implementing-incremental-snapshot-copy"></a>Implementace přírůstkové kopie snímku
Přírůstkovou kopii snímku můžete implementovat následujícím způsobem:

* Pořídit snímek základního objektu BLOB pomocí [objektu BLOB snímku](/rest/api/storageservices/Snapshot-Blob).
* Zkopírujte snímek do cílového účtu úložiště zálohování ve stejné nebo jiné oblasti Azure pomocí [objektu BLOB kopírování](/rest/api/storageservices/Copy-Blob). Toto je objekt BLOB záložní stránky. Pořídit snímek objektu BLOB záložní stránky a uložit ho do záložního účtu.
* Použijte jiný snímek základního objektu BLOB pomocí objektu BLOB snímku.
* Získejte rozdíl mezi prvním a druhým snímkům základního objektu BLOB pomocí [GetPageRanges](/rest/api/storageservices/Get-Page-Ranges). Použijte nový parametr **prevsnapshot** k určení hodnoty DateTime snímku, se kterým chcete získat rozdíl. Pokud je tento parametr přítomen, bude odpověď REST obsahovat pouze stránky, které byly změněny mezi cílovým snímkem a předchozím snímkem včetně vymazat stránky.
* Použijte [PutPage](/rest/api/storageservices/Put-Page) pro použití těchto změn v objektu BLOB záložní stránky.
* Nakonec pořídit snímek objektu BLOB záložní stránky a uložit ho do účtu úložiště zálohování.

V další části popíšeme podrobněji, jak můžete uchovávat zálohy disků pomocí přírůstkové kopie snímků.

## <a name="scenario"></a>Scénář
V této části popíšeme scénář, který zahrnuje vlastní strategii zálohování disků virtuálních počítačů pomocí snímků.

Vezměte virtuální počítač Azure řady DS-Series s připojeným diskem úložiště Premium Storage P30. Disk P30 s názvem *mypremiumdisk* je uložený v účtu Premium Storage s názvem *mypremiumaccount*. Pro uložení zálohy *mypremiumdisk* se používá účet standardního úložiště s názvem *mybackupstdaccount* . Chtěli bychom uchovávat snímek *mypremiumdisku* každých 12 hodin.

Další informace o vytvoření účtu úložiště najdete v tématu [Vytvoření účtu úložiště](../articles/storage/common/storage-account-create.md).

Další informace o zálohování virtuálních počítačů Azure najdete v tématu [plánování záloh virtuálních počítačů Azure](../articles/backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Postup uchovávání záloh disku pomocí přírůstkových snímků
Následující kroky popisují, jak pořizovat snímky *mypremiumdisk* a udržovat zálohy v *mybackupstdaccount*. Záloha je standardní objekt blob stránky s názvem *mybackupstdpageblob*. Objekt blob stránky zálohy vždy odráží stejný stav jako poslední snímek *mypremiumdisk*.

1. Vytvořte objekt BLOB záložní stránky pro disk služby Premium Storage tím, že pořídíte snímek *mypremiumdisk* s názvem *mypremiumdisk_ss1*.
2. Zkopírujte tento snímek do mybackupstdaccount jako objekt blob stránky s názvem *mybackupstdpageblob*.
3. Pořídit snímek *mybackupstdpageblob* s názvem *Mybackupstdpageblob_ss1* pomocí [objektu BLOB snímku](/rest/api/storageservices/Snapshot-Blob) a uložit ho v *mybackupstdaccount*.
4. Během období zálohování vytvořte další snímek *mypremiumdisk*, řekněme *mypremiumdisk_ss2* a uložte ho do *mypremiumaccount*.
5. Umožňuje získat přírůstkové změny mezi dvěma snímky, *mypremiumdisk_ss2* a *mypremiumdisk_ss1* pomocí [GetPageRanges](/rest/api/storageservices/Get-Page-Ranges) na *mypremiumdisk_ss2* s parametrem **prevsnapshot** nastaveným na časové razítko *mypremiumdisk_ss1*. Zapište tyto přírůstkové změny do objektu BLOB záložní stránky *mybackupstdpageblob* v *mybackupstdaccount*. Pokud jsou v přírůstkových změnách odstraněny rozsahy, musí být z objektu BLOB záložní stránky vymazány. [PutPage](/rest/api/storageservices/Put-Page) použijte k zápisu přírůstkových změn do objektu BLOB záložní stránky.
6. Pořídit snímek záložní stránky BLOB *mybackupstdpageblob* s názvem *mybackupstdpageblob_ss2*. Odstraní předchozí snímek *mypremiumdisk_ss1* z účtu Premium Storage.
7. Opakujte kroky 4-6 každé okno zálohování. Tímto způsobem můžete uchovávat zálohy *mypremiumdisk* v účtu úložiště úrovně Standard.

![Zálohování disku pomocí přírůstkových snímků](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Postup obnovení disku ze snímků
Následující postup popisuje, jak obnovit disk Premium *mypremiumdisk* na dřívější snímek z účtu úložiště zálohování *mybackupstdaccount*.

1. Identifikujte bod v čase, na který chcete disk Premium obnovit. Řekněme, že se jedná o snímek *mybackupstdpageblob_ss2*, který je uložený v účtu úložiště záloh *mybackupstdaccount*.
2. V mybackupstdaccount zvyšte úroveň snímku *mybackupstdpageblob_ss2* jako novou databázi BLOB základní stránky *mybackupstdpageblobrestored*.
3. Pořídit snímek tohoto obnoveného objektu BLOB záložní stránky s názvem *mybackupstdpageblobrestored_ss1*.
4. Zkopírujte obnovený objekt blob stránky *mybackupstdpageblobrestored* z *mybackupstdaccount* do *mypremiumaccount* jako nový disk Premium *mypremiumdiskrestored*.
5. Pořídit snímek *mypremiumdiskrestored*, který se nazývá *mypremiumdiskrestored_ss1* pro budoucí přírůstkové zálohování.
6. Nasměrujte virtuální počítač řady DS na obnovený disk *mypremiumdiskrestored* a odpojte starý *mypremiumdisk* z virtuálního počítače.
7. Zahajte proces zálohování, který je popsaný v předchozí části, pro obnovený disk *mypremiumdiskrestored* pomocí *mybackupstdpageblobrestored* jako záložní objekt blob stránky.

![Obnovit disk ze snímků](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Další kroky
Pomocí následujících odkazů se dozvíte víc o vytváření snímků objektu BLOB a plánování infrastruktury zálohování virtuálních počítačů.

* [Vytvoření snímku objektu BLOB](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [Plánování infrastruktury zálohování virtuálních počítačů](../articles/backup/backup-azure-vms-introduction.md)