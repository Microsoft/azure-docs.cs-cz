---
title: Kontrola stavu úlohy importu a exportu Azure – v1 | Microsoft Docs
description: Naučte se, jak používat soubory protokolu vytvořené při spuštění úlohy importu nebo exportu, aby se zobrazil stav úlohy import/export.
author: twooley
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/26/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 5ab0dd7f0e0ed90c205c37ddbb7ea3a9fca74ae9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85512255"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Kontrola stavu úlohy importu a exportu Azure pomocí kopírování souborů protokolu
Když služba Microsoft Azure Import/Export zpracovává jednotky přidružené k úloze importu nebo exportu, zapisuje soubory protokolu kopírování do účtu úložiště, do kterého importujete nebo exportujete objekty blob. Soubor protokolu obsahuje podrobný stav jednotlivých importovaných nebo exportovaných souborů. Adresa URL každého souboru protokolu kopírování se vrátí při dotazování na stav dokončené úlohy. Další informace najdete v tématu [získání úlohy](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get) .  

## <a name="example-urls"></a>Příklady adres URL

Níže jsou uvedené příklady adres URL pro kopírování souborů protokolů pro úlohu importu se dvěma jednotkami:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 Formát protokolů kopírování a úplný seznam stavových kódů najdete v tématu [Formát souboru protokolu služby Import/export](../storage-import-export-file-format-log.md) .  

## <a name="next-steps"></a>Další kroky

 * [Nastavení nástroje Azure pro import/export](storage-import-export-tool-setup-v1.md)   
 * [Příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Oprava úlohy importu](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Oprava úlohy exportu](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Řešení potíží s nástrojem Azure pro import/export](storage-import-export-tool-troubleshooting-v1.md)
