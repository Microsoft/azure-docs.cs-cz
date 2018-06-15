---
title: Stručná referenční příručka pro úlohy příkazy pro import nástroj Azure Import/Export - v1 | Microsoft Docs
description: Azure Import/Export nástroj informace o příkazech pro import často používané příkazy úlohy. Vztahuje se k v1 nástroj importu a exportu.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 370cf6fae7ae106e8341f65086c8b8187d335746
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873678"
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Stručná referenční příručka pro často používané příkazy pro úlohy importu
Tato část poskytuje že rychlý přehled o některé často používané příkazy. Podrobné informace o použití, najdete v části [Příprava pevné disky pro úlohy importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md).  

## <a name="prepare-a-hard-drive-when-data-has-already-been-copied-to-the-hard-drive"></a>Příprava na pevný disk, když data již byla zkopírována do pevného disku
 Následující příkaz připraví na pevný disk, pokud již byl zkopírován do ní data, ale nebyla ještě šifrované pomocí nástroje BitLocker:  
  
```  
  WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /t:d /encrypt /srcdir:d:\movies\drama /dstdir:movies/drama/ /skipwrite
```    

## <a name="copy-a-single-directory-to-a-hard-drive"></a>Zkopírovat jeden adresář na pevný disk  
 Následující příkaz zkopíruje na pevný disk, který nebyl ještě pomocí Bitlockeru šifrovat jeden zdrojový adresář:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
## <a name="copy-two-directories-to-a-hard-drive"></a>Zkopírujte dva adresáře na pevný disk  
 Při kopírování dva adresáře zdroje na jednotku, použijte následující příkazy:  
  
 První příkaz určuje adresář protokolu, klíč účtu úložiště, cílový písmeno jednotky, `format/encrypt` požadavky a společné parametry:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
 V druhém příkazu Určuje soubor deníku, nové ID relace a zdrojové a cílové umístění:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:music /srcdir:d:\Music /dstdir:entertainment/music/  
```  
  
## <a name="copy-a-large-file-to-a-hard-drive-in-a-second-copy-session"></a>Kopírování velkého souboru na pevný disk v relaci druhé kopie  
 Následující příkaz zkopíruje jeden velký soubor na pevný disk, který byl připraven v předchozí relaci kopie:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:dvd /srcfile:d:\dvd\favoritemovie.vhd /dstblob:dvd/favoritemovie.vhd  
```  
  
## <a name="next-steps"></a>Další kroky

* [Ukázkový pracovní postup pro přípravu pevných disků pro úlohu importu](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
