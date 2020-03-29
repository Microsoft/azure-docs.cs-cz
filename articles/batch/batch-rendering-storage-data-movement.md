---
title: Úložiště a přesun dat pro vykreslování – Azure Batch
description: Seznamte se s různými možnostmi ukládání a přesunu dat pro vykreslování úloh datových zdrojů a výstupních souborů.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 1076fc152ddf2c3a2d4f2346262ca90215d68ddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75390387"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Možnosti ukládání a přesunu dat pro vykreslování datových zdrojů a výstupních souborů

Existuje několik možností pro zpřístupnění souborů scény a datových zdrojů vykreslovacích aplikacím ve virtuálních počítačích fondu:

* [Úložiště objektů blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction):
  * Soubory scén a datových zdrojů se nahrávají do úložiště objektů blob z místního systému souborů. Když je aplikace spuštěna úlohou, pak se požadované soubory zkopírují z úložiště objektů blob do virtuálního počítače, aby k nim mohla přistupovat vykreslovací aplikace. Výstupní soubory jsou zapsány vykreslovací aplikací na disk virtuálního počítače a pak zkopírovány do úložiště objektů blob.  V případě potřeby lze výstupní soubory stáhnout z úložiště objektů blob do místního systému souborů.
  * Úložiště objektů blob Azure je jednoduchá a nákladově efektivní možnost pro menší projekty.  Jako všechny soubory datových zdrojů jsou požadovány na každém virtuálním počítači fondu, pak jakmile počet a velikost souborů datových zdrojů zvyšuje pozornost je třeba zajistit, že přenosy souborů jsou co nejúčinnější.  
* Azure storage jako souborový systém pomocí [blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux):
  * Pro virtuální počítače s Linuxem může být účet úložiště vystaven a použit jako systém souborů při použití ovladače virtuálního souborového systému blobfuse.
  * Tato možnost má tu výhodu, že je velmi nákladově efektivní, protože pro systém souborů nejsou vyžadovány žádné virtuální aplikace, plus ukládání blobfuse do mezipaměti na virtuálních počítačích zabraňuje opakovanému stahování stejných souborů pro více úloh a úkolů.  Přesun dat je také jednoduchý, protože soubory jsou jednoduše objekty BLOB a standardní api a nástroje, jako je například azcopy, lze použít ke kopírování souboru mezi místním souborovým systémem a úložištěm Azure.
* Systém souborů nebo sdílená složka:
  * V závislosti na operačním systému virtuálního počítače a požadavcích na výkon a škálování pak možnosti zahrnují [soubory Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction), použití virtuálního počítače s připojenými disky pro systém souborů NFS, použití více virtuálních počítačů s připojenými disky pro distribuovaný souborový systém, jako je GlusterFS, nebo pomocí nabídky třetí strany.
  * [Avere Systems](https://www.averesystems.com/) je nyní součástí společnosti Microsoft a bude mít v blízké budoucnosti řešení, která jsou ideální pro rozsáhlé a vysoce výkonné vykreslování.  Řešení Avere umožní vytvořit mezipaměť nfs nebo SMB založené na Azure, která funguje ve spojení s úložištěm objektů blob nebo s místními zařízeními NAS.
  * Pomocí systému souborů lze soubory číst nebo zapisovat přímo do systému souborů nebo je lze kopírovat mezi systémem souborů a virtuálními aplikacemi fondu.
  * Sdílený systém souborů umožňuje využívat velký počet datových zdrojů sdílených mezi projekty a úlohami, přičemž úkoly vykreslování mají přístup pouze k tomu, co je požadováno.

## <a name="using-azure-blob-storage"></a>Použití úložiště objektů blob Azure

Měl by se používat účet úložiště objektů blob nebo účet úložiště pro obecné účely v2.  Tyto dva typy účtů úložiště lze nakonfigurovat s výrazně vyšší limity ve srovnání s účtem úložiště pro obecné účely v1, jak je podrobně popsáno v [tomto příspěvku blogu](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).  Při konfiguraci vyšší limity umožní mnohem lepší výkon a škálovatelnost, zejména v případě, že existuje mnoho virtuálních počítače fondu přístup k účtu úložiště.

### <a name="copying-files-between-client-and-blob-storage"></a>Kopírování souborů mezi klientem a úložištěm objektů blob

Ke kopírování souborů do a z úložiště Azure lze použít různé mechanismy, včetně rozhraní BLOB API úložiště, [knihovny Datových pohybů úložiště Azure](https://github.com/Azure/azure-storage-net-data-movement), nástroje příkazového řádku azcopy pro [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) nebo [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux), [Průzkumníka úložiště Azure](https://azure.microsoft.com/features/storage-explorer/)a [Průzkumníka dávek Azure](https://azure.github.io/BatchExplorer/).

Například pomocí azkopie lze všechny datové zdroje ve složce převést následujícím způsobem:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Chcete-li kopírovat pouze upravené soubory, lze použít parametr /XO:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopírování vstupních souborů datových zdrojů z úložiště objektů blob do virtuálních aplikací fondu dávek

Existuje několik různých přístupů ke kopírování souborů s nejlepším přístupem určeným velikostí prostředků úlohy.
Nejjednodušší majení je zkopírovat všechny soubory datových zdrojů do virtuálních her fondu pro každou úlohu:

* Pokud existují soubory jedinečné pro úlohu, ale jsou vyžadovány pro všechny úkoly úlohy, pak lze zadat [úlohu přípravy úlohy](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask) pro kopírování všech souborů.  Úloha přípravy úlohy se spustí jednou, když je první úloha úlohy spuštěna na virtuálním počítači, ale není znovu spuštěna pro následné úlohy úlohy.
* [Úloha uvolnění úlohy](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask) by měla být určena k odebrání souborů pro úlohu po dokončení úlohy. tím zabráníte tomu, aby se disk virtuálního počítače vyplnil všemi soubory prostředků úlohy.
* Pokud existuje více úloh, které používají stejné datové zdroje, pouze s přírůstkovými změnami datových zdrojů pro každou úlohu, jsou všechny soubory datových zdrojů stále zkopírovány, i když byla aktualizována pouze podmnožina.  To by bylo neefektivní, pokud existuje velké množství velkých souborů majetku.

Při soubory datových zdrojů jsou znovu použity mezi úlohami, pouze přírůstkové změny mezi úlohami, pak efektivnější, ale o něco více zapojený přístup je k ukládání datových zdrojů ve sdílené složce na virtuálním počítači a synchronizace změněné soubory.

* Úloha přípravy úlohy by provedla kopii pomocí azcopy s parametrem /XO do sdílené složky virtuálního zařízení určené proměnnou prostředí AZ_BATCH_NODE_SHARED_DIR.  To bude kopírovat pouze změněné soubory do každého virtuálního virtuálního ms.
* Myšlenka bude muset být věnována velikosti všech prostředků, aby bylo zajištěno, že se vejdou na dočasné jednotky virtuálních počítačů fondu.

Azure Batch má integrovanou podporu pro kopírování souborů mezi účtem úložiště a virtuálními počítači fondu dávek.  [Soubory prostředků](https://docs.microsoft.com/rest/api/batchservice/job/add#resourcefile) úloh kopírují soubory z úložiště do fondu virtuálních aplikací a mohou být určeny pro úlohu přípravy úlohy.  Bohužel, když tam jsou stovky souborů je možné zasáhnout limit a úkoly k selhání.  Pokud existuje velký počet datových zdrojů, doporučujeme použít příkazový řádek azkopie v úloze přípravy úlohy, která může používat zástupné znaky a nemá žádné omezení.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Kopírování výstupních souborů do úložiště objektů blob z virtuálních aplikací fondu dávek

[Výstupní soubory](https://docs.microsoft.com/rest/api/batchservice/task/add#outputfile) lze použít kopírování souborů z virtuálního virtuálního zařízení fondu do úložiště.  Jeden nebo více souborů lze zkopírovat z virtuálního účtu do zadaného účtu úložiště po dokončení úlohy.  Vykreslený výstup by měl být zkopírován, ale také může být žádoucí ukládat soubory protokolu.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Použití blobfuse virtuální souborový systém pro fondy virtuálních virtuálních aplikací pro Linux

Blobfuse je virtuální ovladač systému souborů pro Azure Blob Storage, který umožňuje přístup k souborům uloženým jako objekty BLOB v účtu úložiště prostřednictvím systému souborů Linux.

Uzly fondu můžete připojit systém souborů při spuštění nebo připojení může dojít jako součást úlohy přípravy úlohy – úloha, která je spuštěna pouze při spuštění první úlohy v úloze na uzlu.  Blobfuse lze nakonfigurovat tak, aby využívalramdisk i místní SSD virtuálních počítačů pro ukládání souborů do mezipaměti, což výrazně zvýší výkon, pokud více úloh na uzlu přistupuje k některým stejným souborům.

[Ukázkové šablony jsou k dispozici](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) pro spuštění samostatné vokáce V-Ray pomocí blobfuse souborového systému a mohou být použity jako základ pro šablony pro jiné aplikace.

### <a name="accessing-files"></a>Přístup k souborům

Úlohy úloh určují cesty pro vstupní soubory a výstupní soubory pomocí připojeného systému souborů.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopírování vstupních souborů datových zdrojů z úložiště objektů blob do virtuálních aplikací fondu dávek

Vzhledem k tomu, že soubory jsou jednoduše objekty BLOB ve službě Azure Storage, pak standardní řešení objektů BLOB, nástroje a umělá nastavení se dají použít ke kopírování souborů mezi místním systémem souborů a úložištěm objektů blob; například azcopy, Storage Explorer, Batch Explorer atd.

## <a name="using-azure-files-with-windows-vms"></a>Používání souborů Azure s virtuálními počítači s Windows

[Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) nabízí plně spravované sdílené složky v cloudu, které jsou přístupné prostřednictvím protokolu SMB.  Soubory Azure je založené na úložišti objektů blob Azure; je [nákladově efektivní](https://azure.microsoft.com/pricing/details/storage/files/) a lze jej nakonfigurovat s replikací dat do jiné oblasti, která je tak globálně redundantní.  [Škálování cíle](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) by měly být přezkoumány k určení, pokud soubory Azure by měly být použity vzhledem k velikosti fondu prognózy a počet souborů datových zdrojů.

K dispozici je [příspěvek blogu](https://blogs.msdn.microsoft.com/windowsazurestorage/2014/05/26/persisting-connections-to-microsoft-azure-files/) a [dokumentace,](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) která popisuje, jak připojit sdílenou složku Azure.

### <a name="mounting-an-azure-files-share"></a>Připojení sdílené složky Azure Files

Chcete-li použít v Batch, připojení operace musí být provedena pokaždé, když úloha v běhu, protože není možné zachovat připojení mezi úkoly.  Nejjednodušší způsob, jak to provést, je použít příkaz cmdkey k uchování přihlašovacích údajů pomocí úlohy spuštění v konfiguraci fondu a potom před každou úlohu připojit sdílenou složku.

Příklad použití cmdkey v šabloně fondu (uvozené pro použití v souboru JSON) – všimněte si, že při oddělení volání cmdkey od volání net use musí být kontext uživatele pro počáteční úlohu stejný jako kontext použitý pro spuštění úloh:

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

Příklad příkazového řádku úlohy úlohy úlohy:
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

Úlohy úloh určují cesty pro vstupní soubory a výstupní soubory pomocí připojeného systému souborů, a to buď pomocí mapované jednotky, nebo cesty UNC.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Kopírování vstupních souborů datových zdrojů z úložiště objektů blob do virtuálních aplikací fondu dávek

Soubory Azure jsou podporované všemi hlavními api a nástroji, které mají podporu Azure Storage; například azcopy, Azure CLI, Storage Explorer, Azure PowerShell, Batch Explorer atd.

[Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) je k dispozici pro automatickou synchronizaci souborů mezi místním souborovým systémem a sdílenou složkou Azure File.

## <a name="next-steps"></a>Další kroky

Další informace o možnostech úložiště naleznete v podrobné dokumentaci:

* [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)
* [Blůza](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)
* [Soubory Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)
