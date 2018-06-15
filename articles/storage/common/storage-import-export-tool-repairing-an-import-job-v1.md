---
title: Oprava úlohy importu Azure Import/Export - v1 | Microsoft Docs
description: Zjistěte, jak opravit úlohu importu, který byl vytvořen a spuštění pomocí služby Azure Import/Export.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 38cc16bd-ad55-4625-9a85-e1726c35fd1b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: c837713fd9e7d03287ae5a3644fd6bb47714c9d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927464"
---
# <a name="repairing-an-import-job"></a>Oprava úlohy importu
Službu Microsoft Azure Import/Export se pravděpodobně nepodaří zkopírujte některé soubory nebo části souborů do služby Windows Azure Blob. Chyby z těchto důvodů:  
  
-   Poškozené soubory  
  
-   Poškozené jednotky  
  
-   Klíč účtu úložiště změnila. Přestože během přenosu souboru.  
  
Můžete spustit nástroj Microsoft Azure Import/Export v importu souborů protokolu kopie úlohy a nástroj odesílá chybějící soubory (nebo části souborů) na váš účet úložiště služby Windows Azure k dokončení úlohy importu.  
  
## <a name="repairimport-parameters"></a>Parametry RepairImport

Mohou být zadány následující parametry s **RepairImport**: 
  
|||  
|-|-|  
|**/ r:**< RepairFile\>|**Vyžaduje se.** Cesta k souboru oprava, která sleduje průběh opravu a umožňuje obnovit přerušené opravit. Každé jednotky, musí mít pouze jeden soubor opravit. Když spustíte opravy pro danou jednotku, předejte v cestě oprava souboru, který ještě neexistuje. Pokud chcete obnovit přerušené opravy, by měla předávat název existující soubor opravit. Soubor opravy odpovídající cílová jednotka je vždy třeba zadat.|  
|**/logdir:**< LogDirectory\>|**Volitelný parametr.** K adresáři protokolů. Podrobné soubory protokolu se zapisují do tohoto adresáře. Pokud není zadán žádný adresář protokolu, aktuální adresář se používá jako adresář protokolu.|  
|**/ d:**< TargetDirectories\>|**Vyžaduje se.** Jeden nebo více oddělených středníkem adresáře, které obsahují původní soubory, které byly naimportovány. Import disku mohou být využity také, ale není vyžadováno, pokud jsou k dispozici alternativní umístění původní soubory.|  
|**/BK:**< BitLockerKey\>|**Volitelný parametr.** Pokud chcete nástroj k odemknutí zašifrované jednotky kde původní soubory jsou k dispozici, je třeba zadat klíč nástroje BitLocker.|  
|**/sn:**< StorageAccountName\>|**Vyžaduje se.** Název účtu úložiště pro úlohy importu.|  
|**/Sk:**< StorageAccountKey\>|**Požadované** Pokud není zadán sdíleného přístupového podpisu kontejneru. Klíč účtu pro účet úložiště pro úlohy importu.|  
|**/csas:**< ContainerSas\>|**Požadované** Pokud není zadaný klíč účtu úložiště. Kontejner SAS pro přístup k objektům BLOB přidružený k úloze importu.|  
|**/ CopyLogFile:**< DriveCopyLogFile\>|**Vyžaduje se.** Cesta k souboru protokolu kopie disku (buď podrobné protokolu nebo Chyba protokolu). Soubor je generována pomocí služby Windows Azure Import/Export a si můžete stáhnout z úložiště objektů blob, které jsou přidružené k úloze. Kopírovat soubor protokolu obsahuje informace o selhání objekty BLOB nebo soubory, které mají být opravit.|  
|**/ PathMapFile:**< DrivePathMapFile\>|**Volitelný parametr.** Cesta k textový soubor, který lze vyřešit nejednoznačnosti, pokud máte více souborů se stejným názvem, který měla importu ve stejné úloze. Při prvním spuštění nástroje jej můžete naplnit tento soubor se všemi nejednoznačné názvy. Při dalším spuštění nástroje pro tento soubor použít přeložit nejednoznačnosti.|  
  
## <a name="using-the-repairimport-command"></a>Pomocí příkazu RepairImport  
Chcete-li opravit importovat data pomocí vysílání datového proudu data přes síť, musíte zadat adresáře, které obsahují původní soubory byly import pomocí `/d` parametr. Musíte zadat také kopírovat soubor protokolu, který jste stáhli z vašeho účtu úložiště. Typické příkazového řádku k opravě úlohy importu s chybami částečné vypadá takto:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
V následujícím příkladu kopírování souboru protokolu byl poškozen jeden 64 tisíc část souboru na jednotce, která byla odeslaná úlohy importu. Protože se jedná pouze selhání uvedené, zbytek objektů BLOB v úlohy byly úspěšně importovány.  
  
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
  
Když tento protokol kopie předána nástroj Azure Import/Export, pokusí se dokončit import tohoto souboru kopírování chybějící obsahu přes síť. Po výše uvedeném příkladu nástroj vyhledá původní soubor `\animals\koala.jpg` v rámci dva adresáře `C:\Users\bob\Pictures` a `X:\BobBackup\photos`. Pokud soubor `C:\Users\bob\Pictures\animals\koala.jpg` existuje, nástroj Azure Import/Export zkopíruje chybějící oblasti dat na odpovídající objekt blob `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`.  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Řešení konfliktů při použití RepairImport  
V některých situacích nástroj pravděpodobně nebude moci najít nebo otevřít soubor potřebné pro jednu z následujících důvodů: soubor nebyl nalezen, soubor není dostupný, je nejednoznačný název souboru nebo obsah souboru již není správný.  
  
Nejednoznačné chybě může dojít, pokud tento nástroj se pokouší vyhledat `\animals\koala.jpg` a existuje soubor s tímto názvem v obou `C:\Users\bob\pictures` a `X:\BobBackup\photos`. To znamená i `C:\Users\bob\pictures\animals\koala.jpg` a `X:\BobBackup\photos\animals\koala.jpg` existovat na jednotkách úlohy importu.  
  
`/PathMapFile` Možnost umožňuje vyřešte tyto chyby. Můžete zadat název souboru, který obsahuje seznam souborů, které tento nástroj se nepodařilo správně identifikovat. V následujícím příkladu příkazového řádku naplní `9WM35C2V_pathmap.txt`:  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Tento nástroj pak zapíše problematické cesty k `9WM35C2V_pathmap.txt`, každou na jeden řádek. Například soubor může obsahovat následující položky po spuštění příkazu:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 Pro každý soubor v seznamu by měl pokusí najít a otevřít soubor zkontrolujte, zda že je k dispozici pro nástroj. Pokud chcete zjistit nástroj explicitně, kde najít soubor, můžete upravit cestě k souboru mapy a přidejte cestu ke každému souboru na stejném řádku, oddělených tabulátorem:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Po zpřístupnění nástroje potřebné soubory nebo aktualizaci souboru s mapováním cestu, můžete znovu spustit nástroj pro dokončení procesu importu.  
  
## <a name="next-steps"></a>Další kroky
 
* [Nastavení nástroje Azure Import/Export](storage-import-export-tool-setup-v1.md)   
* [Příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Kontrola stavu úlohy s použitím kopií souborů protokolu](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Oprava úlohy exportu](../storage-import-export-tool-repairing-an-export-job-v1.md)   
* [Řešení potíží s nástrojem Azure pro import/export](storage-import-export-tool-troubleshooting-v1.md)
