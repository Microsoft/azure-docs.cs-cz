---
title: Kontrola stavu úlohy importu a exportu Azure – v1 | Dokumenty společnosti Microsoft
description: Přečtěte si, jak pomocí souborů protokolu vytvořených při spuštění úlohy importu nebo exportu zobrazit stav úlohy Import/Export.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/26/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 33e6ecca0eb4ca05b1f6bf53cdb3bd4ac8c8d8a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978442"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Kontrola stavu úlohy importu a exportu Azure pomocí souborů protokolu kopírování
Když služba Import a export Microsoft Azure řídí jednotky přidružené k importu nebo exportu úlohy, zapíše soubory protokolu kopírování do účtu úložiště do nebo ze kterého importujete nebo exportujete objekty BLOB. Soubor protokolu obsahuje podrobný stav každého souboru, který byl importován nebo exportován. Adresa URL každého souboru protokolu kopírování je vrácena při dotazování na stav dokončené úlohy. další informace naleznete v tématu [Get Job.](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get)  

## <a name="example-urls"></a>Příklad adres URL

Následují příklady adres URL pro kopírování souborů protokolu pro úlohu importu se dvěma jednotkami:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 Formát protokolů kopírování a úplný seznam stavových kódů naleznete v tématu [Import/export služby Log File Format.](../storage-import-export-file-format-log.md)  

## <a name="next-steps"></a>Další kroky

 * [Nastavení nástroje pro import a export Azure](storage-import-export-tool-setup-v1.md)   
 * [Příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Oprava úlohy importu](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Oprava úlohy exportu](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Řešení potíží s nástrojem Azure pro import/export](storage-import-export-tool-troubleshooting-v1.md)
