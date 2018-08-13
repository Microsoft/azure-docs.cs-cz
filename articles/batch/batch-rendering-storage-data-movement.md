---
title: Azure Batch úložiště a data přesunu pro vykreslování
description: Přesun možnosti úložiště a dat pro úlohy vykreslování
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: d5b006fd744e463c73ee0a32388f254017e96354
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "40034725"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Přesun možnosti úložiště a data pro vykreslení asset a výstupní soubory

Zpřístupnění soubory scény a prostředků pro vykreslování aplikace ve fondu virtuálních počítačů několika způsoby:

* [Úložiště objektů blob v Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction):
  * Soubory scény a prostředků se nahrají do služby blob storage z místního souboru systému. Při spuštění aplikace úlohou jsou tak k nim může přistupovat aplikací vykreslování požadované soubory zkopírovány z úložiště objektů blob do virtuálního počítače. Výstupní soubory jsou zapsána aplikací vykreslování na disk virtuálního počítače a poté zkopírován do úložiště objektů blob.  V případě potřeby výstupní soubory můžete stáhnout z úložiště objektů blob v místním systému souborů.
  * Úložiště objektů blob v Azure je jednoduché a nákladově efektivní možnosti pro menší projekty.  Jako všech prostředků, které jsou vyžadovány soubory na každý fond virtuálních počítačů, pak po zvyšuje počet a velikost souborů prostředků jsou co nejúčinnější péče musí být udělat, aby přenosy souborů.  
* Azure storage jako systém souborů pomocí [blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux):
  * Účet úložiště může pro virtuální počítače s Linuxem, vystavený a při ovladač blobfuse virtuálním souborovém systému slouží jako systém souborů.
  * Tuto možnost má výhodu, že je nákladově velmi efektivní, jako jsou požadovány pro systém souborů žádné virtuální počítače a navíc blobfuse ukládání do mezipaměti na virtuálních počítačích se vyhnete opakované stahování stejné soubory, které pro více úloh a úkolů.  Přesun dat je také jednoduchý, jako jsou soubory jednoduše objekty BLOB a standardní rozhraní API a nástroje, jako je například azcopy, slouží ke kopírování souborů mezi místním systému souborů a úložiště Azure.
* Systém souborů nebo sdílení souborů:
  * V závislosti na operačním systému virtuálního počítače a požadavky na výkon a škálování, pak mezi možnosti patří [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)pomocí virtuálního počítače s připojenými disky pro systém souborů NFS, použití několika virtuálních počítačů s připojenými disky pro systém souborů DFS například GlusterFS, nebo pomocí nabídky třetí strany.
  * [Systémy Avere](http://www.averesystems.com/) je nyní součástí Microsoftu a budou mít v blízké budoucnosti řešení, které jsou ideální pro vykreslování ve velkém měřítku, vysoce výkonné.  Avere řešení vám umožní systému souborů NFS založené na Azure nebo mezipaměť SMB má být vytvořen, který funguje ve spojení s úložištěm objektů blob nebo na místních zařízeních NAS.
  * Systém souborů, souborů může číst nebo zapisovat přímo do systému souborů nebo je možné zkopírovat mezi systém souborů a fond virtuálních počítačů.
  * Sdílený systém souborů umožňuje velký počet prostředků sdílet mezi projekty a úlohy, které se budou využívat, s vykreslováním úlohy přístup pouze k co je potřeba.

## <a name="using-azure-blob-storage"></a>Použití úložiště objektů blob v Azure

Účet blob storage nebo účet úložiště pro obecné účely verze 2 by měla sloužit.  Tyto dva typy účtů úložiště se dá nakonfigurovat s výrazně vyšší limity porovnání na účet úložiště pro obecné účely v1, jak je uvedeno v [tento příspěvek na blogu](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).  Při konfiguraci, vyšší limity umožní mnohem lepší výkon a škálovatelnost, zvlášť pokud máte mnoho virtuálních počítačů fondu přístupu k účtu úložiště.

### <a name="copying-files-between-client-and-blob-storage"></a>Kopírování souborů mezi klientem a blob storage

Pokud chcete zkopírovat soubory do a z úložiště Azure, různé mechanismy lze použít úložiště objektů blob rozhraní API, včetně [knihovna pro přesun dat úložiště Azure](https://github.com/Azure/azure-storage-net-data-movement), nástroje příkazového řádku azcopy pro [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) nebo [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux), [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/), a [Azure Batch Explorer](https://azure.github.io/BatchExplorer/).

Například pomocí nástroje azcopy, všechny prostředky ve složce můžete přenést následujícím způsobem:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Pokud chcete zkopírovat pouze změněné soubory, je možné parametrem:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopírování souborů vstupní prostředků z úložiště objektů blob k virtuálním počítačům ve fondu služby Batch

Existuje několik různých přístupů ke kopírování souborů se určuje podle velikosti prostředků úlohy nejlepším řešením.
Nejjednodušším způsobem je ke kopírování všech souborů prostředků do fondu virtuálních počítačů pro každou úlohu:

* Pokud nejsou soubory jedinečné pro úlohu, ale jsou požadovány pro všechny úkoly úlohy, o [úkol přípravy úlohy](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask) je možné zadat pro zkopírujte všechny soubory.  Úkol přípravy úlohy je spustit jednou po první úlohy spuštěné na virtuálním počítači, ale není znovu pro následující úlohy.
* A [úkol uvolnění úlohy](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask) se musí nastavit odebrat soubory za úlohu po dokončení úlohy; tím se vyhnete disku virtuálního počítače, získávání vyplnění podle všech souborů prostředků úlohy.
* Pokud existuje více úloh s využitím stejné prostředky s pouze přírůstkové změny, které prostředky pro každou úlohu, pak všechny soubory prostředků se stále zkopírují, i v případě, že byly aktualizovány pouze podmnožinu.  To může být neefektivní, pokud existuje velké množství souborů velkých prostředků.

Když jsou opakovaně souborů prostředků mezi úlohami s pouze přírůstkové změny mezi úlohami, a zefektivnit tak zapojí o něco víc, ale přístup je ukládat prostředky do sdílené složky na virtuálním počítači a synchronizovat změněné soubory.

* Úkol přípravy úlohy bude provádět kopírování pomocí nástroje azcopy s parametrem virtuálního počítače sdílené složky, které jsou určeny proměnnou prostředí AZ_BATCH_NODE_SHARED_DIR.  To zkopíruje pouze změněné soubory na každý virtuální počítač.
* Tím bude mít má být poskytnut velikosti nad všemi prostředky k Ujistěte se, že se vejde na dočasné jednotce virtuální počítače ve fondu.

Služba Azure Batch obsahuje integrovanou podporu pro kopírování souborů mezi účet úložiště a virtuální počítače ve fondu služby Batch.  Úloha [soubory prostředků](https://docs.microsoft.com/rest/api/batchservice/job/add#resourcefile) kopírování souborů z úložiště do fondu virtuálních počítačů a může být zadané pro úkol přípravy úlohy.  Bohužel když existují stovky soubory je možné k dosažení limitu a selhání úlohy.  Pokud existuje velký počet prostředků se doporučuje použít příkazový řádek azcopy v úkol přípravy úlohy, které můžete použít zástupné znaky a nemá žádné omezení.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Kopírování výstupních souborů do objektu blob úložiště z virtuální počítače ve fondu služby Batch

[Výstupní soubory](https://docs.microsoft.com/rest/api/batchservice/task/add#outputfile) může být použité kopírování souborů z fondu virtuálních počítačů do úložiště.  Jeden nebo více souborů je možné zkopírovat z virtuálního počítače do účtu úložiště zadaný po dokončení úlohy.  Vykresleného výstupu by se měly zkopírovat, ale také může být vhodné pro ukládání souborů protokolu.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Pro fondy virtuálních počítačů s Linuxem pomocí blobfuse virtuální systém souborů

Blobfuse je ovladač virtuálním souborovém systému pro Azure Blob Storage, který umožňuje přístup k souborům uložené jako objekty BLOB v účtu úložiště v systému Linux.

Uzly fondu můžete připojit systém souborů při spuštění nebo připojení může dojít, jako součást přípravy úlohy – úlohu, která se spouští pouze při spuštění první úlohy v rámci úlohy na uzlu.  Využití disku paměti RAM a místní disk SSD virtuálních počítačů pro ukládání do mezipaměti soubory, které podstatně tím zvýšíte výkon Pokud více úkolů na uzlu získat přístup k některým stejné soubory, které je možné nakonfigurovat Blobfuse.

[Ukázkové šablony jsou k dispozici](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) ke spuštění samostatného V-Ray vykreslí pomocí systému souborů blobfuse a může sloužit jako základ pro šablony pro jiné aplikace.

### <a name="accessing-files"></a>Přístup k souborům

Úlohy zadejte cesty k vstupních a výstupních souborů pomocí systému připojeného souboru.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopírování souborů vstupní prostředků z úložiště objektů blob k virtuálním počítačům ve fondu služby Batch

Jednoduše objekty BLOB ve službě Azure Storage jsou soubory pak standardní objektů blob rozhraní API, nástrojů a uživatelská rozhraní slouží ke kopírování souborů mezi služby v místním souboru systému a objektu blob storage; například azcopy Průzkumníka služby Storage, služby Batch Explorer, atd.

## <a name="using-azure-files-with-windows-vms"></a>Služba soubory Azure pomocí virtuální počítače s Windows

[Služba soubory Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné přes protokol SMB.  Služba soubory Azure je založen na úložiště objektů blob Azure; je [nákladově efektivní](https://azure.microsoft.com/pricing/details/storage/files/) a dá se s replikací dat do jiné oblasti tak globálně redundantní.  [Měřítko cíle](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) byste měli zkontrolovat, chcete-li zjistit, pokud soubory Azure by měla sloužit zadanou velikost prognózy fondu a počtu souborů prostředků.

Je [blogový příspěvek](https://blogs.msdn.microsoft.com/windowsazurestorage/2014/05/26/persisting-connections-to-microsoft-azure-files/) a [dokumentaci](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) zabývají připojení sdílené složky Azure.

### <a name="mounting-an-azure-files-share"></a>Připojením sdílené složky služby soubory Azure

Pokud chcete použít ve službě Batch, je třeba provést pokaždé, když úkolu v spustit, protože není možné zachovat propojení mezi úkoly operaci připojení.  Nejjednodušší způsob, jak to provést, je použití cmdkey uchování přihlašovacích údajů a spouštěcí úkol v konfiguraci fondu a pak připojit sdílenou složku před každý úkol.

Příklad použití cmdkey v šabloně fondu (uvozen řídicími znaky, pro použití v souboru JSON) – když oddělení cmdkey volání z volání příkaz net use uživatelský kontext pro spouštěcí úkol musí být stejná jako použitý ke spuštění úlohy:

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

Příklad úlohy úloha příkazového řádku:
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

Úlohy zadejte cesty k vstupních a výstupních souborů pomocí systému připojeného souboru, buď pomocí mapovaná jednotka nebo cesta UNC.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopírování souborů vstupní prostředků z úložiště objektů blob k virtuálním počítačům ve fondu služby Batch

Služba soubory Azure podporuje všechny hlavní rozhraní API a nástrojů, které mají podporu; služby Azure Storage například azcopy, Azure CLI, Průzkumníka služby Storage, Azure Powershellu, Průzkumníka služby Batch, atd.

[Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) je možné automaticky synchronizovat soubory mezi v místním systému souborů a sdílené složky Azure.

## <a name="next-steps"></a>Další postup

Další informace o možnostech úložiště najdete v podrobné dokumentaci:

* [Úložiště objektů blob v Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)
* [Blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)
* [Služba soubory Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)
