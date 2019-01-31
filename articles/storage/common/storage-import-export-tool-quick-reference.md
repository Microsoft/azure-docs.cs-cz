---
title: Stručná referenční příručka pro nástroj Azure Import/Export import úlohy příkazy | Dokumentace Microsoftu
description: Přehled příkazů Azure nástrojem Import/Export importu často používané příkazy úlohy
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 7d21ab42188d5b8d6cb8c179a28d1b5bee822f05
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453582"
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Stručná referenční příručka pro často používané příkazy pro úlohy importu

Tento článek poskytuje že rychlý přehled o některé často používané příkazy. Podrobné využití, naleznete v tématu [příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import.md).

## <a name="first-session"></a>První relace

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

## <a name="second-session"></a>Druhý relace

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /DataSet:dataset-2.csv
```

## <a name="abort-latest-session"></a>Přerušit nejnovější relace

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /AbortSession
```

## <a name="resume-latest-interrupted-session"></a>Obnovit nejnovější přerušení relace

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /ResumeSession
```

## <a name="add-drives-to-latest-session"></a>Přidejte disky do nejnovější relace

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /AdditionalDriveSet:driveset-2.csv
```

## <a name="next-steps"></a>Další postup

* [Ukázkový pracovní postup pro přípravu pevných disků pro úlohu importu](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
