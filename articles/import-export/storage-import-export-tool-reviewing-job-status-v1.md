---
title: Kontrola stavu úlohy importu a exportu Azure – v1 | Microsoft Docs
description: Naučte se, jak používat soubory protokolu vytvořené úlohou importu nebo exportu k zobrazení stavu úlohy.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: f634ceb60ae78d4d825c73bd2e98da2fb951b374
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98706307"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Kontrola stavu úlohy importu a exportu Azure pomocí kopírování souborů protokolu
Když služba Microsoft Azure Import/Export zpracuje jednotky, které jsou přidružené k úloze importu nebo exportu, zapisuje soubory protokolu kopírování do účtu úložiště, který jste použili k importu nebo exportu objektů BLOB. Soubor protokolu obsahuje podrobný stav jednotlivých importovaných nebo exportovaných souborů. Služba při dotazování na stav dokončené úlohy vrátí adresu URL pro každý soubor protokolu kopírování. Další informace najdete v tématu [získání úlohy](/rest/api/storageimportexport/Jobs/Get).  

## <a name="example-urls"></a>Příklady adres URL

Níže jsou uvedené příklady adres URL pro kopírování souborů protokolů pro úlohu importu se dvěma jednotkami:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 Formát protokolů kopírování a úplný seznam stavových kódů najdete v tématu [Formát souboru protokolu služby Import/export](/previous-versions/azure/storage/common/storage-import-export-file-format-log) .  

## <a name="next-steps"></a>Další kroky

 * [Nastavení nástroje Azure pro import/export](storage-import-export-tool-setup-v1.md)   
 * [Příprava pevných disků pro úlohu importu](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)   
 * [Oprava úlohy importu](./storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Oprava úlohy exportu](./storage-import-export-tool-repairing-an-export-job-v1.md)