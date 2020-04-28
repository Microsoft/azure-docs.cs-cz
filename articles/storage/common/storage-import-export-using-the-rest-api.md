---
title: Použití REST API služby importu a exportu v Azure | Microsoft Docs
description: Naučte se najít prostředky pro použití REST API služby importu a exportu v Azure, včetně referenčního materiálu.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 833b8c79fba57b7129092e084381c0671c396496
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "74978862"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Použití rozhraní REST API služby Azure Import/Export

Služba Microsoft Azure Import/Export zpřístupňuje REST API umožňující programové řízení úloh importu/exportu. REST API můžete použít k provedení všech operací importu/exportu, které můžete s [Azure Portal](https://portal.azure.com/)provádět. Kromě toho můžete použít REST API k provádění určitých podrobných operací, jako je dotazování procentuálního dokončení úlohy, které není aktuálně k dispozici v Azure Portal.

V tématu [použití služby Microsoft Azure import/export k přenosu dat do BLOB Storage](../storage-import-export-service.md) najdete přehled služby Import/export a kurz, který ukazuje, jak pomocí portálu vytvářet a spravovat úlohy importu a exportu.

## <a name="service-endpoints"></a>Koncové body služby

Služba import/export Azure je poskytovatel prostředků pro Azure Resource Manager a poskytuje sadu rozhraní REST API pro správu úloh importu/exportu v následujícím koncovém bodu HTTPS:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Správa verzí

Požadavky na službu importu/exportu musí zadat `api-version` parametr a nastavit jeho hodnotu na. `2016-11-01`

## <a name="importexport-service-operations"></a>Operace služby Import/export

[Vytvoření úlohy importu](../storage-import-export-creating-an-import-job.md)

[Vytvoření úlohy exportu](../storage-import-export-creating-an-export-job.md)

[Načítání informací o stavu úlohy](storage-import-export-retrieving-state-info-for-a-job.md)

[Vytváření výčtu úloh](../storage-import-export-enumerating-jobs.md)

[Rušení a odstraňování úloh](storage-import-export-cancelling-and-deleting-jobs.md)

[Zálohování manifestů jednotek](../storage-import-export-backing-up-drive-manifests.md)

[Diagnostika a zotavení z chyb pro úlohy Import/export](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Další kroky

* [REST pro import/export úložiště](/rest/api/storageimportexport)
