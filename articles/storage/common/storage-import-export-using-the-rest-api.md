---
title: Použití rozhraní REST API služby Azure import u exportu | Dokumenty společnosti Microsoft
description: Zjistěte, kde najít prostředky pro používání rozhraní REST API služby Azure Import/Export, včetně jak s návodem, tak referenčního materiálu.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 833b8c79fba57b7129092e084381c0671c396496
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978862"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Použití rozhraní REST API služby Azure Import/Export

Služba Import a export Microsoft Azure poskytuje rozhraní REST API, které umožňuje programovou kontrolu úloh importu a exportu. Rozhraní REST API můžete použít k provádění všech operací importu a exportu, které můžete provádět s [portálem Azure](https://portal.azure.com/). Kromě toho můžete použít rozhraní REST API k provádění určitých podrobných operací, jako je například dotazování procento dokončení úlohy, která není aktuálně k dispozici na webu Azure Portal.

Přehled služby Import a export a kurz, který ukazuje, jak pomocí portálu vytvářet a spravovat úlohy importu a exportu, najdete v článku [Použití služby Import/Export Microsoft Azure k přenosu dat do úložiště objektů blob](../storage-import-export-service.md) a v kurzu, který ukazuje, jak pomocí portálu vytvářet a spravovat úlohy importu a exportu.

## <a name="service-endpoints"></a>Koncové body služby

Služba Import a export Azure je poskytovatelem prostředků pro Azure Resource Manager a poskytuje sadu rest API na následujícím koncovém bodu HTTPS pro správu úloh importu a exportu:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Správa verzí

Požadavky na službu Import/Export `api-version` musí zadat parametr `2016-11-01`a nastavit jeho hodnotu na .

## <a name="importexport-service-operations"></a>Operace dovozních a vývozních služeb

[Vytvoření úlohy importu](../storage-import-export-creating-an-import-job.md)

[Vytvoření úlohy exportu](../storage-import-export-creating-an-export-job.md)

[Načítání informací o stavu úlohy](storage-import-export-retrieving-state-info-for-a-job.md)

[Vytváření výčtu úloh](../storage-import-export-enumerating-jobs.md)

[Rušení a odstraňování úloh](storage-import-export-cancelling-and-deleting-jobs.md)

[Zálohování manifestů jednotky](../storage-import-export-backing-up-drive-manifests.md)

[Diagnostika a zotavení z chyb pro úlohy Import/export](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Další kroky

* [Úložiště importu nebo exportu REST](/rest/api/storageimportexport)
