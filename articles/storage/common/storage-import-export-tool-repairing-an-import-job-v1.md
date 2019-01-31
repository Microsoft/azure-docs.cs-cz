---
title: Oprava úlohy importu Azure Import/Export - v1 | Dokumentace Microsoftu
description: Zjistěte, jak opravit úlohy importu, který jste vytvořili a spuštění pomocí služby Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: fda1d3d626c91ba984f08b96c79ab6a2fd2ec74b
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471727"
---
# <a name="repairing-an-import-job"></a>Oprava úlohy importu
Služba Microsoft Azure Import/Export nemusí podařit zkopírujte některé soubory nebo části souborů do služby Windows Azure Blob service. Mezi důvody pro selhání patří:  
  
-   Poškozené soubory  
  
-   Poškozené jednotky  
  
-   Klíč účtu úložiště změnit soubor během přenosu.  
  
Můžete spustit nástroj Microsoft Azure Import/Export v importu úlohy kopií souborů protokolu a nástroj nahraje do účtu úložiště Windows Azure k dokončení úlohy import chybějící soubory (nebo části souboru).  
  
## <a name="repairimport-parameters"></a>Parametry RepairImport

Je možné zadat následující parametry při **RepairImport**: 
  
|||  
|-|-|  
|**/ r:**< RepairFile\>|**Povinné.** Cesta k souboru opravit, který sleduje průběh opravy a umožňuje obnovit přerušené opravu, která. Každá jednotka musí mít jeden a pouze jeden soubor opravy. Při spuštění opravu pro daný disk předejte v cestě opravit soubor, který ještě neexistuje. Pokud chcete obnovit přerušené opravit, je třeba předat název existující soubor opravy. Vždy třeba zadat soubor opravy odpovídající cílové jednotce.|  
|**/logdir:**< LogDirectory\>|**Volitelné.** Adresář protokolu. Souborů podrobného protokolování se zapisují do tohoto adresáře. Pokud není zadán žádný adresář protokolu, se používá aktuální adresář jako adresář protokolu.|  
|**/ d:**< TargetDirectories\>|**Povinné.** Jeden nebo více oddělených středníkem adresářů, které obsahují původní soubory, které byly naimportovány. Import disku mohou být využity také, ale není potřeba, pokud jsou k dispozici alternativní umístění původních souborů.|  
|**/bk:**<BitLockerKey\>|**Volitelné.** Pokud chcete, aby nástroj k odemknutí zašifrované jednotky, kde původní soubory jsou k dispozici, měli byste určit klíč Bitlockeru.|  
|**/sn:**<StorageAccountName\>|**Povinné.** Název účtu úložiště pro úlohy importu.|  
|**/sk:**<StorageAccountKey\>|**Vyžaduje** pouze v případě sdíleného přístupového podpisu kontejneru není zadán. Klíč účtu pro účet úložiště pro úlohy importu.|  
|**/csas:**<ContainerSas\>|**Vyžaduje** pouze v případě není zadaný klíč účtu úložiště. Kontejner SAS pro přístup k objektům BLOB spojené s úlohou importu.|  
|**/CopyLogFile:**<DriveCopyLogFile\>|**Povinné.** Cesta k souboru protokolu kopie disku (buď podrobný protokol nebo Chyba protokol). Soubor je vygenerován pomocí služby Windows Azure Import/Export a si můžete stáhnout z úložiště objektů blob, které jsou přidružené k úloze. Kopírovat soubor protokolu obsahuje informace o neúspěšných objekty BLOB nebo soubory, které mají být opraven.|  
|**/PathMapFile:**<DrivePathMapFile\>|**Volitelné.** Cesta k souboru text, který slouží k vyřešení nejednoznačnosti, pokud máte více souborů se stejným názvem, který jste importovali ve stejné úloze. Při prvním spuštění nástroje ho do tohoto souboru se všemi nejednoznačné názvy. Následné spuštění nástroje použít tento soubor nejednoznačnosti.|  
  
## <a name="using-the-repairimport-command"></a>Pomocí příkazu RepairImport  
Chcete-li opravit import dat pomocí streamování dat v síti, je nutné zadat adresáře, které obsahují původní soubory byly import pomocí `/d` parametru. Musíte zadat také kopie souboru protokolu, který jste stáhli ze svého účtu úložiště. Typické příkazového řádku oprava úlohy importu se částečně neúspěšné vypadá takto:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
V následujícím příkladu kopírovat soubor protokolu byl poškozen jednu část 64 K souboru na disku, který byl dodán pro úlohy importu. Protože se jedná pouze chyby uvedené, zbývající objektů BLOB v rámci úlohy se úspěšně naimportovaly.  
  
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
  
Pokud tento protokol kopírování je předán do nástroje Azure Import/Export, pokusí se dokončit import tohoto souboru tak, že zkopírujete chybějící obsah přes síť. Ve výše uvedeném příkladu, nástroj hledá původní soubor `\animals\koala.jpg` v rámci dva adresáře `C:\Users\bob\Pictures` a `X:\BobBackup\photos`. Pokud soubor `C:\Users\bob\Pictures\animals\koala.jpg` existuje, nástroj pro Import/Export Azure zkopíruje chybějící rozsahu dat na odpovídající objekt blob `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`.  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Řešení konfliktů při použití RepairImport  
V některých situacích nemusí být nástroj moci najít nebo otevřít soubor nezbytné pro jednu z následujících důvodů: soubor nebyl nalezen, soubor není přístupný, název souboru je nejednoznačný nebo obsah souboru už není správné.  
  
Nejednoznačný chybě může dojít, pokud nástroj se pokouší vyhledat `\animals\koala.jpg` a existuje soubor s tímto názvem v rámci obou `C:\Users\bob\pictures` a `X:\BobBackup\photos`. To znamená, že oba `C:\Users\bob\pictures\animals\koala.jpg` a `X:\BobBackup\photos\animals\koala.jpg` existovat na jednotkách úlohy importu.  
  
`/PathMapFile` Možnost vám umožňuje tyto chyby vyřešit. Můžete zadat název souboru, který obsahuje seznam souborů, které nástroj nebyl schopen správně identifikovat. Následující příklad příkazového řádku naplní `9WM35C2V_pathmap.txt`:  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Nástroj potom zapíše cesty k souborům problematické pro `9WM35C2V_pathmap.txt`, každou na jeden řádek. Například soubor může obsahovat následující položky po spuštění příkazu:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 Pro každý soubor v seznamu měli byste se pokusit k vyhledání a otevření souboru a ujistěte se, že je k dispozici pro nástroj. Pokud chcete zjistit nástroj explicitně, kde najít soubor, můžete upravit cestu souboru mapy a přidejte cestu ke každému souboru na stejném řádku, oddělené tabulátorem:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Po zpřístupnění soubory potřebné k nástroji nebo aktualizuje soubor mapy cestu, můžete znovu spustit nástroj pro dokončení procesu importu.  
  
## <a name="next-steps"></a>Další postup
 
* [Nastavení nástroje Azure Import/Export](storage-import-export-tool-setup-v1.md)   
* [Příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Kontrola stavu úlohy s použitím kopií souborů protokolu](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Oprava úlohy exportu](../storage-import-export-tool-repairing-an-export-job-v1.md)   
* [Řešení potíží s nástrojem Azure pro import/export](storage-import-export-tool-troubleshooting-v1.md)
