---
title: Oprava úlohy importu a exportu Azure – v1 | Dokumenty společnosti Microsoft
description: Zjistěte, jak opravit úlohu importu, která byla vytvořena a spuštěna pomocí služby Azure Import/Export.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: f5db321d8c4a6e42591a82b0ed8eb6bc6e93bad4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973879"
---
# <a name="repairing-an-import-job"></a>Oprava úlohy importu
Služba Import a export Microsoft Azure nemusí zkopírovat některé soubory nebo části souboru do služby Objektů blob Windows Azure. Některé důvody selhání zahrnují:  
  
-   Poškozené soubory  
  
-   Poškozené pohony  
  
-   Klíč účtu úložiště se během přenosu souboru změnil.  
  
Nástroj pro import a export aplikace Microsoft Azure můžete spustit pomocí souborů protokolu kopírování úlohy importu a nástroj nahraje chybějící soubory (nebo části souboru) do vašeho účtu úložiště Windows Azure, abyste dokončili úlohu importu.  
  
## <a name="repairimport-parameters"></a>Parametry RepairImport

Následující parametry lze zadat s **RepairImport**: 
  
|||  
|-|-|  
|**/r:**<RepairFile\>|**Požadované.** Cesta k souboru opravy, který sleduje průběh opravy a umožňuje pokračovat v přerušené opravě. Každá jednotka musí mít jeden a pouze jeden soubor opravy. Při spuštění opravy pro danou jednotku, předat cestu k souboru opravy, který ještě neexistuje. Chcete-li pokračovat v přerušené opravě, měli byste předat název existujícího souboru opravy. Vždy musí být zadán soubor opravy odpovídající cílové jednotce.|  
|**/logdir:**<LogDirectory\>|**Volitelné.** Adresář protokolu. Podrobné soubory protokolu jsou zapsány do tohoto adresáře. Pokud není zadán žádný adresář protokolu, bude jako adresář protokolu použit aktuální adresář.|  
|**/d:**<cílové adresáře\>|**Požadované.** Jeden nebo více adresářů oddělených středníkem, které obsahují původní soubory, které byly importovány. Importní jednotka může být také použita, ale není vyžadována, pokud jsou k dispozici alternativní umístění původních souborů.|  
|**/bk:**<BitLockerKey\>|**Volitelné.** Chcete-li, aby nástroj odemkl šifrovanou jednotku, na které jsou k dispozici původní soubory, měl byste zadat klíč nástroje BitLocker.|  
|**/sn:**<AccountName úložiště\>|**Požadované.** Název účtu úložiště pro úlohu importu.|  
|**/sk:**<StorageAccountKey\>|**Povinné,** pokud a pouze v případě, že není zadán kontejner SAS. Klíč účtu pro účet úložiště pro úlohu importu.|  
|**/csas:**<ContainerSas\>|**Povinné,** pokud a pouze v případě, že není zadán klíč účtu úložiště. Kontejner SAS pro přístup k objektům BLOB přidružených k úloze importu.|  
|**/CopyLogFile:**<DriveCopyLogFile\>|**Požadované.** Cesta k souboru protokolu kopírování jednotky (podrobný protokol nebo protokol chyb). Soubor je generován službou Import a export Windows Azure a lze jej stáhnout z úložiště objektů blob přidruženého k úloze. Soubor protokolu kopírování obsahuje informace o neúspěšných objektech BLOB nebo souborech, které mají být opraveny.|  
|**/PathMapFile:**<DrivePathMapFile\>|**Volitelné.** Cesta k textovému souboru, který lze použít k vyřešení nejasností, pokud máte více souborů se stejným názvem, který jste importovali ve stejné úloze. Při prvním spuštění může nástroj naplnit tento soubor všemi nejednoznačnými názvy. Následné spuštění nástroje pomocí tohoto souboru vyřešit nejasnosti.|  
  
## <a name="using-the-repairimport-command"></a>Použití příkazu RepairImport  
Chcete-li opravit data importu datovým proudem dat v síti, je nutné zadat `/d` adresáře obsahující původní soubory, které jste importovali pomocí parametru. Je také nutné zadat soubor protokolu kopírování, který jste stáhli z účtu úložiště. Typický příkazový řádek pro opravu úlohy importu s částečnými chybami vypadá takto:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
V následujícím příkladu souboru protokolu kopírování byla na jednotce, která byla dodána pro úlohu importu, poškozena jedna část souboru 64 K. Vzhledem k tomu, že se jedná o jediné uvedené selhání, byly úspěšně importovány ostatní objekty BLOB v úloze.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
 <DriveId>9WM35C2V</DriveId>  
 <Blob Status="CompletedWithErrors">  
 <BlobPath>pictures/animals/koala.jpg</BlobPath>  
 <FilePath>\animals\koala.jpg</FilePath>  
 <Length>163840</Length>  
 <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
 <PageRangeList>  
  <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted" />  
 </PageRangeList>  
 </Blob>  
 <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
Když je tento protokol kopírování předán nástroji pro import a export Azure, nástroj se pokusí dokončit import tohoto souboru zkopírováním chybějícího obsahu v síti. Po výše uvedeném příkladu nástroj `\animals\koala.jpg` vyhledá původní soubor `C:\Users\bob\Pictures` `X:\BobBackup\photos`v rámci dvou adresářů a . Pokud soubor `C:\Users\bob\Pictures\animals\koala.jpg` existuje, nástroj pro import a export Azure zkopíruje chybějící `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`rozsah dat do odpovídajícího objektu blob .  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Řešení konfliktů při použití RepairImport  
V některých situacích nemusí být nástroj schopen najít nebo otevřít potřebný soubor z jednoho z následujících důvodů: soubor nebyl nalezen, soubor není přístupný, název souboru je nejednoznačný nebo obsah souboru již není správný.  
  
Nejednoznačná chyba může dojít, `\animals\koala.jpg` pokud se nástroj pokouší najít `C:\Users\bob\pictures` a `X:\BobBackup\photos`je soubor s tímto názvem pod oběma a . To znamená, `C:\Users\bob\pictures\animals\koala.jpg` `X:\BobBackup\photos\animals\koala.jpg` že oba a existují na jednotkách úloh importu.  
  
Tato `/PathMapFile` možnost umožňuje tyto chyby vyřešit. Můžete zadat název souboru, který obsahuje seznam souborů, které nástroj nebyl schopen správně identifikovat. Následující příklad příkazového řádku `9WM35C2V_pathmap.txt`naplní :  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Nástroj pak na každém řádku zapíše problematické cesty k `9WM35C2V_pathmap.txt`souborům . Soubor může například po spuštění příkazu obsahovat následující položky:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 Pro každý soubor v seznamu byste se měli pokusit soubor vyhledat a otevřít, abyste se ujistili, že je pro nástroj k dispozici. Chcete-li nástroji výslovně sdělit, kde najít soubor, můžete upravit soubor mapy cesty a přidat cestu ke každému souboru na stejném řádku, oddělenéznakem tabulátoru:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Po zpřístupnění potřebných souborů nástroji nebo aktualizaci souboru mapy cesty můžete nástroj znovu spustit a dokončit proces importu.  
  
## <a name="next-steps"></a>Další kroky
 
* [Nastavení nástroje pro import a export Azure](storage-import-export-tool-setup-v1.md)   
* [Příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Kontrola stavu úlohy s použitím kopií souborů protokolu](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Oprava úlohy exportu](../storage-import-export-tool-repairing-an-export-job-v1.md)   
* [Řešení potíží s nástrojem Azure pro import/export](storage-import-export-tool-troubleshooting-v1.md)
