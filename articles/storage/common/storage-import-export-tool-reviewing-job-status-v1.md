---
title: Kontrola stavu úlohy Azure Import/Export - v1 | Microsoft Docs
description: Naučte se používat soubory protokolů vytvořené při spuštění úlohy import nebo export a zjistit stav úlohy importu a exportu.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: c69d1d69-6403-4eee-9949-0185faeecfa1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: muralikk
ms.openlocfilehash: bdb30bc28c36ab9e969efc8be3b87b97e4027b39
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873706"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Kontrola stavu úlohy Azure Import/Export s kopírovat soubory protokolu
Při zpracovávání službou Microsoft Azure Import/Export jednotky, které jsou přidružené úloze importu nebo exportu, zapíše kopírovat soubory protokolů pro účet úložiště, který nebo ze kterého se import nebo export objektů BLOB. Soubor protokolu obsahuje podrobné informace o stavu o každý soubor, který byl importovat nebo exportovat. Adresa URL ke každému souboru protokolu kopie je vrácena, pokud dotaz na stav dokončené úlohy; v tématu [Get Job](/rest/api/storageservices/Get-Job3) Další informace.  

## <a name="example-urls"></a>Příklad adresy URL

Následuje příklad adresy URL pro kopírování souborů protokolu pro úlohy importu se dvě jednotky:  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  
  
 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  
  
 V tématu [službu Import/Export formát souboru protokolu](../storage-import-export-file-format-log.md) pro formát kopírování protokolů a úplný seznam stavových kódů.  
  
## <a name="next-steps"></a>Další kroky
 
 * [Nastavení nástroje Azure Import/Export](storage-import-export-tool-setup-v1.md)   
 * [Příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Oprava úlohy importu](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Oprava úlohy exportu](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Řešení potíží s nástrojem Azure pro import/export](storage-import-export-tool-troubleshooting-v1.md)
