---
title: Oprava úlohy importu importu/exportu Azure – v1 | Microsoft Docs
description: Naučte se, jak opravit úlohu importu vytvořenou a spuštěnou pomocí služby Azure import/export.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/23/2017
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: d67046f799e60db3101dfeb27dee10f92f9aad79
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90052422"
---
# <a name="repairing-an-import-job"></a>Oprava úlohy importu
Ve službě Microsoft Azure Import/Export může dojít k selhání při kopírování některých souborů nebo částí souborů do služby Windows Azure Blob service. Mezi některé z příčin selhání patří:  
  
-   Poškozené soubory  
  
-   Poškozené jednotky  
  
-   Během přenosu souboru se změnil klíč účtu úložiště.  
  
Nástroj pro Microsoft Azure Import/Export můžete spustit pomocí souborů protokolu kopírovat úlohu importu. Nástroj nahraje chybějící soubory nebo části souboru do vašeho účtu úložiště Windows Azure, aby dokončil úlohu importu.  
  
## <a name="repairimport-parameters"></a>Parametry RepairImport

Pomocí **RepairImport**lze zadat následující parametry: 
  
|||  
|-|-|  
|**/r:**<RepairFile\>|**Požadovanou.** Cesta k souboru opravy, který sleduje průběh opravy a umožňuje obnovit přerušenou opravu. Každá jednotka musí mít jeden a jenom jeden opravný soubor. Když zahájíte opravu dané jednotky, předejte cestu k souboru opravy, který ještě neexistuje. Chcete-li obnovit přerušenou opravu, měli byste předat název existujícího opravného souboru. Vždy zadejte soubor opravy odpovídající cílové jednotce.|  
|**/logdir:**<LogDirectory\>|**Volitelné.** Adresář protokolu. Podrobné soubory protokolu se zapisují do tohoto adresáře. Pokud není zadaný žádný adresář protokolu, použije se jako adresář protokolu aktuální adresář.|  
|**/d:**<TargetDirectories\>|**Požadovanou.** Jeden nebo více středníkem oddělených adresářů, které obsahují původní importované soubory. Pokud jsou k dispozici alternativní umístění původních souborů, může se také použít importovaná jednotka, ale není nutná.|  
|**/BK:**<BitLockerKey\>|**Volitelné.** Pokud chcete, aby nástroj Odemkl šifrovanou jednotku, kde jsou k dispozici původní soubory, zadejte klíč BitLockeru.|  
|**/sn:**<StorageAccountName\>|**Požadovanou.** Název účtu úložiště pro úlohu importu.|  
|**/SK:**<StorageAccountKey\>|**Vyžaduje** se, pokud a jenom v případě, že není zadané SAS kontejneru. Klíč účtu pro účet úložiště pro úlohu importu|  
|**/csas:**<ContainerSas\>|**Vyžaduje** se jenom v případě, že není zadaný klíč účtu úložiště. SAS kontejneru pro přístup k objektům blob přidruženým k úloze importu.|  
|**/CopyLogFile:**<DriveCopyLogFile\>|**Požadovanou.** Cesta k souboru protokolu pro kopírování jednotek (buď podrobný protokol, nebo protokol chyb). Soubor vygeneruje služba Windows Azure pro import/export a dá se stáhnout z úložiště objektů BLOB přidruženého k úloze. Soubor protokolu kopírování obsahuje informace o neúspěšných objektech blob nebo souborech, které se mají opravit.|  
|**/PathMapFile:**<DrivePathMapFile\>|**Volitelné.** Cesta k textovému souboru, který se používá k vyřešení nejednoznačnosti, pokud máte více souborů se stejným názvem, které jste importovali do stejné úlohy. Při prvním spuštění nástroje může tento soubor naplnit všechny dvojznačné názvy. Pozdější spuštění nástroje tento soubor použijte k vyřešení nejednoznačnosti.|  
  
## <a name="using-the-repairimport-command"></a>Použití příkazu RepairImport  
Chcete-li opravit importovaná data streamovaná daty přes síť, je nutné zadat adresáře obsahující původní soubory, které jste importovali pomocí `/d` parametru. Zadejte také soubor protokolu pro kopírování, který jste si stáhli z účtu úložiště. Typický příkazový řádek, který umožňuje opravit úlohu importu s částečnými chybami, vypadá takto:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
V následujícím příkladu souboru protokolu kopírování byla na jednotce, která byla expedována pro úlohu importu, poškozena část souboru 1 64-K. Vzhledem k tomu, že tato chyba je jenom jedna, znamená to, že zbývající objekty BLOB v úloze se úspěšně naimportovaly.  
  
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
  
Když se tento protokol kopírování předává nástroji Azure pro import/export, nástroj se pokusí dokončit import tohoto souboru zkopírováním chybějícího obsahu v síti. Podle výše uvedeného příkladu Nástroj vyhledá původní soubor `\animals\koala.jpg` v obou adresářích `C:\Users\bob\Pictures` a `X:\BobBackup\photos` . Pokud soubor `C:\Users\bob\Pictures\animals\koala.jpg` existuje, nástroj pro import/export v Azure zkopíruje chybějící rozsah dat do odpovídajícího objektu BLOB `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg` .  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Řešení konfliktů při použití RepairImport  
V některých situacích nemusí být nástroj schopný najít nebo otevřít potřebný soubor z jednoho z následujících důvodů: soubor se nepovedlo najít, soubor není přístupný, název souboru je nejednoznačný nebo obsah souboru už není správný.  
  
Pokud se nástroj pokouší najít `\animals\koala.jpg` soubor s tímto názvem a v rámci, může dojít k dvojznačné chybě `C:\Users\bob\pictures` `X:\BobBackup\photos` . To znamená, že `C:\Users\bob\pictures\animals\koala.jpg` i `X:\BobBackup\photos\animals\koala.jpg` existují na jednotkách import úlohy.  
  
`/PathMapFile`Tato možnost umožňuje tyto chyby vyřešit. Můžete zadat název souboru, který obsahuje seznam souborů, které nástroj nedokázal správně identifikovat. Následující příklad příkazového řádku naplní `9WM35C2V_pathmap.txt` :  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Nástroj pak napíše problematické cesty k souborům na `9WM35C2V_pathmap.txt` jeden řádek. Soubor může například obsahovat následující položky po spuštění příkazu:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 Pro každý soubor v seznamu byste se měli pokusit vyhledat soubor a otevřít ho, aby bylo zajištěno, že je k dispozici pro nástroj. Pokud chcete nástroji sdělit explicitně, kde najít soubor, upravte soubor mapy cesty a přidejte cestu k jednotlivým souborům na stejném řádku, oddělené znakem tabulátoru:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Po zpřístupnění potřebných souborů pro nástroj nebo aktualizaci souboru mapování cesty můžete nástroj znovu spustit a dokončit tak proces importu.  
  
## <a name="next-steps"></a>Další kroky
 
* [Nastavení nástroje Azure pro import/export](storage-import-export-tool-setup-v1.md)   
* [Příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Kontrola stavu úlohy s použitím kopií souborů protokolu](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Oprava úlohy exportu](../storage-import-export-tool-repairing-an-export-job-v1.md)
