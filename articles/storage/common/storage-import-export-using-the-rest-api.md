---
title: Pomocí REST API služby Azure Import/Export | Microsoft Docs
description: Zjistěte, kde můžete najít prostředky pro používání služby Azure Import/Export rozhraní REST API, včetně postupy a referenční materiál.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 233f80e9-2e7f-48e0-9639-5c7785e7d743
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 9a5a97a5d9f06aa73f1ad521e112fa25f215724f
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2017
ms.locfileid: "26690173"
---
# <a name="using-the-azure-importexport-service-rest-api"></a>Použití rozhraní REST API služby Azure Import/Export

Službu Microsoft Azure Import/Export zpřístupňuje rozhraní REST API umožňující programovací řízení úlohy importu a exportu. Rozhraní REST API můžete provádět všechny operace importu a exportu, které můžete provádět pomocí [portál Azure](https://portal.azure.com/). Kromě toho můžete použít rozhraní API REST k provádění některých granulární operací, jako je například dotazování procento dokončení úlohy, která není aktuálně k dispozici na portálu Azure.

V tématu [pomocí služby Microsoft Azure Import/Export přenos dat do úložiště objektů Blob](../storage-import-export-service.md) přehled službu Import/Export a kurz, který ukazuje, jak pomocí portálu pro vytváření a správu import a export úloh.

## <a name="service-endpoints"></a>Koncové body služby

Služba Azure Import/Export je poskytovatel prostředků pro Azure Resource Manager a poskytuje sadu rozhraní API REST v následující koncový bod HTTPS pro správu úlohy importu a exportu:

```
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ImportExport/jobs/<job-name>
```

## <a name="versioning"></a>Správa verzí

Musíte zadat žádosti o službu Import/Export `api-version` parametr a jeho hodnotu nastavte `2016-11-01`.

## <a name="importexport-service-operations"></a>Import a Export operací služby

[Vytvoření úlohy importu](../storage-import-export-creating-an-import-job.md)

[Vytvoření úlohy exportu](../storage-import-export-creating-an-export-job.md)

[Načítání informací o stavu úlohy](storage-import-export-retrieving-state-info-for-a-job.md)

[Vytváření výčtu úloh](../storage-import-export-enumerating-jobs.md)

[Rušení a odstraňování úloh](storage-import-export-cancelling-and-deleting-jobs.md)

[Manifesty zálohování jednotky](../storage-import-export-backing-up-drive-manifests.md)

[Diagnostika a zotavení z chyb pro úlohy Import/export](../storage-import-export-diagnostics-and-error-recovery.md)

## <a name="next-steps"></a>Další kroky

* [Import/Export úložiště REST](/rest/api/storageimportexport)
