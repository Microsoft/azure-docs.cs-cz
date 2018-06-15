---
title: Náhled využití disku pro úlohy exportu Azure Import/Export - v1 | Microsoft Docs
description: Informace o zobrazení náhledu seznam objektů BLOB, které jste vybrali pro úlohy exportu ve službě Azure Import/Export.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 7707d744-7ec7-4de8-ac9b-93a18608dc9a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 6ec74ae0b0931f3fed99a43f4f7e58f9d425b138
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873643"
---
# <a name="previewing-drive-usage-for-an-export-job"></a>Náhled využití disku pro úlohu exportu
Než vytvoříte úlohy exportu, je třeba vybrat sadu objektů blob pro export. Službu Microsoft Azure Import/Export můžete použít seznam objektů blob cesty nebo objekt blob předpony k reprezentaci objektů BLOB, které jste vybrali.  
  
Dále musíte určit, kolik jednotek, budete muset odeslat. Tento nástroj Import/Export nabízí `PreviewExport` příkaz Náhled využití disku pro objekty BLOB, které jste vybrali, na základě velikosti jednotky se chystáte použít.

## <a name="command-line-parameters"></a>Parametry příkazového řádku

Při použití, můžete použít následující parametry `PreviewExport` příkaz nástroje importu a exportu.

|Parametr příkazového řádku|Popis|  
|--------------------------|-----------------|  
|**/logdir:**< LogDirectory\>|Volitelné. K adresáři protokolů. Souborů podrobného protokolování se zapíšou do tohoto adresáře. Pokud není zadán žádný adresář protokolu, aktuální adresář se použije jako adresář protokolu.|  
|**/sn:**< StorageAccountName\>|Povinná hodnota. Název účtu úložiště pro úlohy exportu.|  
|**/Sk:**< StorageAccountKey\>|Vyžaduje, pokud není zadán sdíleného přístupového podpisu kontejneru. Klíč účtu pro účet úložiště pro úlohy exportu.|  
|**/csas:**< ContainerSas\>|Vyžaduje, pokud není zadaný klíč účtu úložiště. Kontejner SAS pro výpis objekty BLOB ve úloha exportu.|  
|**/ ExportBlobListFile:**< ExportBlobListFile\>|Povinná hodnota. Cesta k souboru XML soubor obsahující seznam objektů blob cesty nebo objektu blob předpony cestu pro export objektů BLOB. Formát souboru, který je používán `BlobListBlobPath` element v [Put úlohy](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) operace importu/exportu služby REST API.|  
|**/ DriveSize:**< DriveSize\>|Povinná hodnota. Velikost jednotky použijte pro úlohy exportu, *například*, 500 GB, 1,5 TB.|  

## <a name="command-line-example"></a>Příklad příkazového řádku

Následující příklad ukazuje `PreviewExport` příkaz:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
Seznam souboru exportu objektu blob může obsahovat názvy objektů blob a objektů blob předpony, jak je vidět tady:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Nástroj Azure Import/Export obsahuje seznam všech objektů blob pro export a vypočítá postup pack je do jednotky po zadanou velikost vezme v úvahu všechny nezbytné režijní náklady a pak odhadne počet jednotek, které jsou potřebné pro uložení objektů BLOB a informace o využití disku.  
  
Tady je příklad výstupu s informační protokoly vynechání:  
  
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
  
## <a name="next-steps"></a>Další kroky

* [Referenční dokumentace Azure nástroj pro Import/Export](../storage-import-export-tool-how-to-v1.md)
