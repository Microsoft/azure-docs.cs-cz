---
title: Úložiště a přesun dat pro vykreslování
description: Seznamte se s různými možnostmi úložiště a přesunu dat pro vykreslování assetů a výstupních souborů.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: 55ec04df2a107dabfc72298bc8849c13f3a926e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86147299"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Možnosti úložiště a přesunu dat pro vykreslování assetů a výstupních souborů

K dispozici je několik možností, jak zpřístupnit soubory scény a assetů pro vykreslování aplikací na virtuálních počítačích fondu:

* [Úložiště objektů BLOB v Azure](../storage/blobs/storage-blobs-introduction.md):
  * Scény a soubory prostředků se nahrají do úložiště objektů BLOB z místního systému souborů. Když je aplikace spuštěná úlohou, pak se požadované soubory zkopírují z úložiště objektů blob na virtuální počítač, aby k nim bylo možné přistupovat z aplikace pro vykreslování. Výstupní soubory jsou zapsány aplikací pro vykreslování na disk virtuálního počítače a následně zkopírovány do úložiště objektů BLOB.  V případě potřeby lze výstupní soubory stáhnout ze služby Blob Storage do místního systému souborů.
  * Úložiště objektů BLOB v Azure je jednoduchá a cenově výhodná možnost pro menší projekty.  Vzhledem k tomu, že všechny soubory assetů se vyžadují na každém virtuálním počítači fondu, pak se po počtu a velikosti souborů assetu postará o péči, aby bylo zajištěno co nejefektivnější přenos souborů.  
* Úložiště Azure jako systém souborů pomocí [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md):
  * Pro virtuální počítače se systémem Linux se dá účet úložiště vystavit a použít jako systém souborů, když se použije ovladač virtuálního systému souborů blobfuse.
  * Tato možnost má výhodu, že je velmi nákladově efektivní, protože systém souborů nevyžaduje žádné virtuální počítače, a mezipaměť blobfuse u virtuálních počítačů vyloučí opakované stahování stejných souborů pro více úloh a úloh.  Přesun dat je také jednoduchý, protože soubory jsou jednoduše objekty BLOB a standardní rozhraní API a nástroje, jako je AzCopy, je možné použít ke kopírování souborů mezi místními systémy souborů a Azure Storage.
* Systém souborů nebo sdílená složka:
  * V závislosti na operačním systému virtuálního počítače a požadavcích na výkon a škálování pak možnosti zahrnují [soubory Azure](../storage/files/storage-files-introduction.md), použití virtuálního počítače s připojenými disky pro systém souborů NFS, použití několika virtuálních počítačů s připojenými disky pro distribuovaný systém souborů, jako je GlusterFS nebo použití nabídky třetí strany.
  * [Systémy avere](https://www.averesystems.com/) je teď součástí Microsoftu a v blízké budoucnosti budou mít řešení, která jsou ideální pro velké a vysoce výkonné vykreslování.  Řešení avere umožní vytvořit mezipaměť systému souborů NFS nebo SMB založenou na Azure, která bude fungovat ve spojení s úložištěm objektů BLOB nebo s místními zařízeními NAS.
  * Se systémem souborů lze soubory číst nebo zapisovat přímo do systému souborů nebo je lze zkopírovat mezi systémem souborů a virtuálními počítači fondu.
  * Sdílený systém souborů umožňuje využít velký počet prostředků, které se sdílejí mezi projekty a úlohami, které se mají použít, a úlohy vykreslování přistupují pouze k tomu, co je potřeba.

## <a name="using-azure-blob-storage"></a>Použití úložiště objektů BLOB v Azure

Měl by se použít účet úložiště BLOB nebo účet úložiště pro obecné účely v2.  Tyto dva typy účtů úložiště se dají nakonfigurovat s výrazně vyššími limity v porovnání s účtem úložiště pro obecné účely V1, jak je podrobně popsáno v [tomto blogovém příspěvku](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).  Po nakonfigurování budou vyšší limity umožňovat mnohem vyšší výkon a škálovatelnost, zejména v případě, že k účtu úložiště přistupuje mnoho virtuálních počítačů fondu.

### <a name="copying-files-between-client-and-blob-storage"></a>Kopírování souborů mezi klientem a úložištěm objektů BLOB

Pokud chcete kopírovat soubory do a z úložiště Azure Storage, můžete použít různé mechanismy, včetně rozhraní API pro úložiště objektů blob, [Azure Storage knihovny pro přesun dat](https://github.com/Azure/azure-storage-net-data-movement), nástroje příkazového řádku AzCopy pro [Windows](../storage/common/storage-use-azcopy-v10.md) nebo [Linux](../storage/common/storage-use-azcopy-v10.md), [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/)a [Azure Batch Explorer](https://azure.github.io/BatchExplorer/).

Pomocí AzCopy můžete například přenést všechny prostředky ve složce následujícím způsobem:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

K zkopírování pouze změněných souborů lze použít parametr/XO:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopírování souborů vstupních assetů z úložiště objektů blob do virtuálních počítačů fondu služby Batch

Existuje několik různých přístupů ke kopírování souborů s nejlepším přístupem určeným velikostí assetů úloh.
Nejjednodušším přístupem je zkopírovat všechny soubory assetů do fondu virtuálních počítačů pro každou úlohu:

* Pokud jsou soubory jedinečné pro úlohu, ale jsou požadovány pro všechny úlohy úlohy, je možné určit [úkol přípravy úlohy](/rest/api/batchservice/job/add#jobpreparationtask) pro zkopírování všech souborů.  Úkol přípravy úlohy se spustí jednou, když se na virtuálním počítači spustí první úloha úlohy, ale pro další úlohy úlohy se znovu nespustí.
* Po dokončení úlohy by měl být zadán [úkol uvolnění úlohy](/rest/api/batchservice/job/add#jobreleasetask) pro odebrání souborů jednotlivých úloh. Tím se vyhnete vyplňování disků virtuálního počítače všemi soubory prostředků úlohy.
* Pokud existuje více úloh využívajících stejné prostředky, přičemž pouze přírůstkové změny prostředků pro každou úlohu, pak jsou všechny soubory prostředků stále zkopírovány i v případě, že byla aktualizována pouze podmnožina.  To by bylo neefektivní, pokud máte spoustu velkých souborů prostředků.

Při opětovném použití souborů assetů mezi úlohami, s pouze přírůstkovým změnám mezi úlohami, je efektivnější, ale ještě mírně spojený přístup, ukládat prostředky do sdílené složky na virtuálním počítači a synchronizovat změněné soubory.

* Úkol přípravy úlohy by provedl kopii pomocí AzCopy s parametrem/XO pro sdílenou složku virtuálního počítače určenou proměnnou prostředí AZ_BATCH_NODE_SHARED_DIR.  Tím se zkopírují pouze změněné soubory do každého virtuálního počítače.
* V myšlenkách se bude muset předávat velikost všech prostředků, aby se zajistilo, že se budou vejít na dočasné jednotky virtuálních počítačů fondu.

Azure Batch má integrovanou podporu pro kopírování souborů mezi účtem úložiště a virtuálními počítači fondu služby Batch.  [Soubory prostředků](/rest/api/batchservice/job/add#resourcefile) úlohy kopírují soubory z úložiště do fondu virtuálních počítačů a dají se zadat pro úkol přípravy úlohy.  V případě, že existují stovky souborů, je možné dosáhnout limitu a úlohy, které selžou.  V případě velkého počtu prostředků se doporučuje použít příkazový řádek AzCopy v úloze přípravy úlohy, která může používat zástupné znaky a nemá žádné omezení.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Kopírování výstupních souborů do úložiště objektů BLOB z virtuálních počítačů fondu Batch

[Výstupní soubory](/rest/api/batchservice/task/add#outputfile) se dají použít ke kopírování souborů z virtuálního počítače fondu do úložiště.  Po dokončení úlohy se dá zkopírovat jeden nebo víc souborů z virtuálního počítače do zadaného účtu úložiště.  Vykreslený výstup by měl být zkopírován, ale také může být žádoucí ukládat soubory protokolu.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Použití virtuálního systému souborů blobfuse pro fondy virtuálních počítačů se systémem Linux

Blobfuse je ovladač virtuálního systému souborů pro Azure Blob Storage, který umožňuje přístup k souborům uloženým jako objekty BLOB v účtu úložiště prostřednictvím systému souborů Linux.

Uzly fondu můžou připojit systém souborů při spuštění nebo se může stát, že se připojení stane součástí úlohy přípravy úlohy – úkol, který se spustí, jenom když se první úloha v úloze spustí na uzlu.  Blobfuse je možné nakonfigurovat tak, aby pro ukládání souborů do mezipaměti využila jak disk RAM, tak i virtuální místní diskovou jednotku (SSD). tím se výrazně zvýší výkon, pokud je v uzlu přístup k některým ze stejných souborů.

[Ukázkové šablony jsou k dispozici](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) pro spouštění samostatného V-Ray vykreslí pomocí systému souborů blobfuse a lze je použít jako základ pro šablony pro jiné aplikace.

### <a name="accessing-files"></a>Přístup k souborům

Úlohy úlohy určují cesty pro vstupní soubory a výstupní soubory pomocí připojeného systému souborů.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopírování souborů vstupních assetů z úložiště objektů blob do virtuálních počítačů fondu služby Batch

Protože soubory jsou v Azure Storage objekty blob, pak je možné pomocí standardních rozhraní API objektů blob, nástrojů a uživatelská rozhraní kopírovat soubory mezi místním systémem souborů a úložištěm objektů BLOB. například AzCopy, Průzkumník služby Storage, Batch Explorer atd.

## <a name="using-azure-files-with-windows-vms"></a>Používání souborů Azure s virtuálními počítači s Windows

[Soubory Azure](../storage/files/storage-files-introduction.md) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné prostřednictvím protokolu SMB.  Soubory Azure jsou založené na službě Azure Blob Storage; je to [nákladově efektivní](https://azure.microsoft.com/pricing/details/storage/files/) a je možné ji nakonfigurovat s replikací dat do jiné oblasti, aby byla globálně redundantní.  [Cíle škálování](../storage/files/storage-files-scale-targets.md#azure-files-scale-targets) by se měly zkontrolovat, aby se zjistilo, jestli se soubory Azure mají použít pro velikost fondu prognóz a počet souborů assetů.

K dispozici je [dokumentace](../storage/files/storage-how-to-use-files-windows.md) , která popisuje, jak připojit sdílenou složku Azure.

### <a name="mounting-an-azure-files-share"></a>Připojení sdílené složky služby soubory Azure

Pro použití ve službě Batch je potřeba provést operaci připojení pokaždé, když je úloha spuštěná, protože není možné zachovat připojení mezi úlohami.  Nejjednodušší způsob, jak to provést, je použít funkci cmdkey k uchování přihlašovacích údajů pomocí spouštěcího úkolu v konfiguraci fondu a pak připojit sdílenou složku před každou úlohu.

Příklad použití cmdkey v šabloně fondu (řídicí znaky pro použití v souboru JSON) – Všimněte si, že při oddělování volání cmdkey z volání NET USE musí být uživatelský kontext pro spouštěcí úkol stejný jako použitý ke spouštění úkolů:

```
"startTask": {
  "commandLine": "cmdkey /add:storageaccountname.file.core.windows.net
    /user:AZURE\\markscuscusbatch /pass:storage_account_key",
  "userIdentity":{
    "autoUser": {
      "elevationLevel": "nonadmin",
      "scope": "pool"
    }
}
```

Příklad příkazového řádku úlohy úlohy:
```
"commandLine":"net use S:
  \\\\storageaccountname.file.core.windows.net\\rendering &
3dsmaxcmdio.exe -v:5 -rfw:0 -10 -end:10
  -bitmapPath:\"s:\\3dsMax\\Dragon\\Assets\"
  -outputName:\"s:\\3dsMax\\Dragon\\RenderOutput\\dragon.jpg\"
  -w:1280 -h:720
  \"s:\\3dsMax\\Dragon\\Assets\\Dragon_Character_Rig.max\""
```

### <a name="accessing-files"></a>Přístup k souborům

Úlohy úlohy určují cesty pro vstupní soubory a výstupní soubory pomocí připojeného systému souborů, a to buď pomocí mapované jednotky nebo cesty UNC.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopírování souborů vstupních assetů z úložiště objektů blob do virtuálních počítačů fondu služby Batch

Služba soubory Azure je podporovaná všemi hlavními rozhraními API a nástroji, které Azure Storage podporu podporují. např. AzCopy, Azure CLI, Průzkumník služby Storage, Azure PowerShell, Batch Explorer atd.

K dispozici je [Azure File Sync](../storage/files/storage-sync-files-planning.md) k automatické synchronizaci souborů mezi místním systémem souborů a sdílenou složkou Azure.

## <a name="next-steps"></a>Další kroky

Další informace o možnostech úložiště najdete v podrobné dokumentaci:

* [Úložiště objektů BLOB v Azure](../storage/blobs/storage-blobs-introduction.md)
* [Blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)
* [Soubory Azure](../storage/files/storage-files-introduction.md)
