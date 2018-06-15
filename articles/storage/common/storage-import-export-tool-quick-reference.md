---
title: Stručná referenční příručka pro nástroj Azure Import/Export import úlohy příkazy | Microsoft Docs
description: Azure Import/Export nástroj informace o příkazech pro import často používané příkazy úlohy.
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
ms.openlocfilehash: d51ae35ead0e7d8289de663e5b7b48d28271e810
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873720"
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Stručná referenční příručka pro často používané příkazy pro úlohy importu

Tento článek poskytuje že rychlý přehled o některé často používané příkazy. Podrobné informace o použití, najdete v části [Příprava pevné disky pro úlohy importu](../storage-import-export-tool-preparing-hard-drives-import.md).

## <a name="first-session"></a>První relaci

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

## <a name="second-session"></a>Druhý relace

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /DataSet:dataset-2.csv
```

## <a name="abort-latest-session"></a>Abort – nejnovější relace

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /AbortSession
```

## <a name="resume-latest-interrupted-session"></a>Obnovte nejnovější dojde k přerušení relace

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /ResumeSession
```

## <a name="add-drives-to-latest-session"></a>Přidejte disky do nejnovější relace

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3 /AdditionalDriveSet:driveset-2.csv
```

## <a name="next-steps"></a>Další kroky

* [Ukázkový pracovní postup pro přípravu pevných disků pro úlohu importu](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
