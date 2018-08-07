---
title: Stručná referenční příručka pro nástroj Azure Import/Export import úlohy příkazy - v1 | Dokumentace Microsoftu
description: Přehled příkazů Azure nástrojem Import/Export importu často používané příkazy úlohy To se vztahuje na v1 nástroje Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 16e0fa6f7336b39f63b00564d37c1be308bebb16
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526279"
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Stručná referenční příručka pro často používané příkazy pro úlohy importu
Tato část poskytuje že rychlý přehled o některé často používané příkazy. Podrobné využití, naleznete v tématu [příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md).  

## <a name="prepare-a-hard-drive-when-data-has-already-been-copied-to-the-hard-drive"></a>Připravit pevného disku v případě datového již byl zkopírován do pevného disku
 Následující příkaz připraví pevný disk, když datového již byl zkopírován do ní, ale nebyl dosud zašifruje pomocí Bitlockeru:  
  
```  
  WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /t:d /encrypt /srcdir:d:\movies\drama /dstdir:movies/drama/ /skipwrite
```    

## <a name="copy-a-single-directory-to-a-hard-drive"></a>Zkopírování jednoho adresáře na pevném disku  
 Následující příkaz zkopíruje na pevný disk, který nebyl dosud pomocí Bitlockeru šifrovat jeden zdrojový adresář:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
## <a name="copy-two-directories-to-a-hard-drive"></a>Zkopírujte dva adresáře na pevném disku  
 Pokud chcete zkopírovat dva adresáře zdrojových souborů na disk, použijte následující příkazy:  
  
 První příkaz určuje adresář protokolu, klíče účtu úložiště, cílové písmeno jednotky, `format/encrypt` požadavky a společné parametry:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
 Druhý příkaz určuje soubor deníku, nové ID relace a zdrojové a cílové umístění:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:music /srcdir:d:\Music /dstdir:entertainment/music/  
```  
  
## <a name="copy-a-large-file-to-a-hard-drive-in-a-second-copy-session"></a>Kopírování velkého souboru na pevném disku v relaci druhé kopie  
 Následující příkaz zkopíruje jeden velký soubor na pevný disk, který byl připraven v předchozí relaci kopírování:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:dvd /srcfile:d:\dvd\favoritemovie.vhd /dstblob:dvd/favoritemovie.vhd  
```  
  
## <a name="next-steps"></a>Další postup

* [Ukázkový pracovní postup pro přípravu pevných disků pro úlohu importu](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
