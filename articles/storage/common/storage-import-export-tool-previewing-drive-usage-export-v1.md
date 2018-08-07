---
title: Náhled využití disku pro úlohu exportu Azure Import/Export - v1 | Dokumentace Microsoftu
description: Zjistěte, jak zobrazit seznam objektů BLOB, které jste vybrali pro úlohu exportu ve službě Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 21c0fd9b258100e769172332713769024fb12969
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520567"
---
# <a name="previewing-drive-usage-for-an-export-job"></a>Náhled využití disku pro úlohu exportu
Než začnete vytvářet úlohy exportu, musíte zvolit sadu objektů BLOB nelze exportovat. Služba Microsoft Azure Import/Export umožňuje použít seznam cesty k objektům blob nebo objekt blob předpony k reprezentaci objektů BLOB, který jste vybrali.  
  
Dále je třeba určit, kolik jednotek, je nutné odeslat. Poskytuje nástroje pro Import/Export `PreviewExport` příkaz Náhled využití disku pro objekty BLOB, které jste vybrali, na základě velikosti disků, budete používat.

## <a name="command-line-parameters"></a>Parametry příkazového řádku

Při použití, můžete použít následující parametry `PreviewExport` příkaz nástroje Import/Export.

|Parametr příkazového řádku|Popis|  
|--------------------------|-----------------|  
|**/logdir:**< LogDirectory\>|Volitelné. Adresář protokolu. Podrobné soubory protokolů se zapíšou do tohoto adresáře. Pokud není zadán žádný adresář protokolu, se použije aktuální adresář jako adresář protokolu.|  
|**/sn:**< StorageAccountName\>|Povinná hodnota. Název účtu úložiště pro úlohu exportu.|  
|**/Sk:**< StorageAccountKey\>|Vyžaduje pouze v případě sdíleného přístupového podpisu kontejneru není zadán. Klíč účtu pro účet úložiště pro úlohu exportu.|  
|**/csas:**< ContainerSas\>|Vyžaduje pouze v případě není zadaný klíč účtu úložiště. Sdíleného přístupového podpisu kontejneru pro výpis objektů BLOB nelze exportovat do úlohy exportu.|  
|**/ ExportBlobListFile:**< ExportBlobListFile\>|Povinná hodnota. Cesta k souboru XML soubor obsahující seznam cesty k objektům blob nebo objekt blob předpony cesty pro objekty BLOB nelze exportovat. Formát souboru používaný v `BlobListBlobPath` prvek [úlohy umístit](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operace rozhraní REST API služby Import/Export.|  
|**/ DriveSize:**< DriveSize\>|Povinná hodnota. Velikost disků pro úlohu exportu *třeba*, 500 GB, 1,5 TB.|  

## <a name="command-line-example"></a>Příklad příkazového řádku

Následující příklad ukazuje, `PreviewExport` příkaz:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
Export souboru seznamu objektů blob může obsahovat názvy objektů blob a objektů blob předpony, jak je znázorněno zde:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Nástroje Import/Export Azure vypíše všechny objekty BLOB export a vypočítá jak se zabalit do jednotky zadané velikosti, s ohledem všechny nezbytné režijní náklady a pak odhadne požadovaný počet jednotek, které jsou potřeba pro uložení objektů BLOB a informace o využití disku.  
  
Tady je příklad výstupu s informační protokoly vynechán:  
  
```  
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  
  
Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB    
```  
  
## <a name="next-steps"></a>Další postup

* [Referenční informace k Azure nástrojem Import/Export](../storage-import-export-tool-how-to-v1.md)
