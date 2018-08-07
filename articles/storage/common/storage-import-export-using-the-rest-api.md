---
title: Pomocí rozhraní REST API služby Azure Import/Export | Dokumentace Microsoftu
description: Zjistěte, kde můžete najít prostředky pro použití rozhraní REST API, včetně postupy a referenční materiály služby Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 3775a77a6dfc590e79e785e1604226c1187952de
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528404"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Použití rozhraní REST API služby Azure Import/Export

Služba Microsoft Azure Import/Export zpřístupňuje rozhraní REST API umožňují programové řízení úlohy import/export. Můžete provádět všechny operace importu/exportu, které můžete provádět pomocí rozhraní REST API [webu Azure portal](https://portal.azure.com/). Kromě toho můžete použít rozhraní REST API k provádění určitých detailní operací, jako jsou například dotazování procento dokončení úlohy, která není aktuálně k dispozici na webu Azure Portal.

Zobrazit [pomocí služby Microsoft Azure Import/Export pro přenos dat do úložiště objektů Blob](../storage-import-export-service.md) přehledné informace o službě Import/Export a kurz, který ukazuje, jak pomocí portálu pro vytváření a správě import a export úloh.

## <a name="service-endpoints"></a>Koncové body služby

Služba Azure Import/Export je poskytovatel prostředků Azure Resource Manageru a poskytuje sadu rozhraní REST API následující koncový bod HTTPS pro správu úlohy importu/exportu:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Správa verzí

Musíte zadat požadavky na službu Import/Export `api-version` parametr a nastavte jej na hodnotu `2016-11-01`.

## <a name="importexport-service-operations"></a>Operace služby Import/Export

[Vytvoření úlohy importu](../storage-import-export-creating-an-import-job.md)

[Vytvoření úlohy exportu](../storage-import-export-creating-an-export-job.md)

[Načítání informací o stavu úlohy](storage-import-export-retrieving-state-info-for-a-job.md)

[Vytváření výčtu úloh](../storage-import-export-enumerating-jobs.md)

[Rušení a odstraňování úloh](storage-import-export-cancelling-and-deleting-jobs.md)

[Zálohování manifestů jednotek](../storage-import-export-backing-up-drive-manifests.md)

[Diagnostika a zotavení z chyb pro úlohy Import/export](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Další postup

* [Import/Export úložiště REST](/rest/api/storageimportexport)
