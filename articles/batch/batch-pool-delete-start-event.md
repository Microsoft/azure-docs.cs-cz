---
title: Událost zahájení odstranění fondu Azure Batch
description: Referenční informace pro událost zahájení odstranění fondu Batch Tato událost je generována při spuštění operace odstranění fondu.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: 5246bb800973cf4ad6d11b88ebdc6c015ac7b463
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76929835"
---
# <a name="pool-delete-start-event"></a>Událost zahájení odstranění fondu

 Tato událost je generována při spuštění operace odstranění fondu. Vzhledem k tomu, že odstranění fondu představuje asynchronní událost, můžete očekávat, že událost odstranění fondu bude po dokončení operace odstranění vygenerována.

 Následující příklad ukazuje tělo události spuštění odstranění fondu.

```
{
    "id": "myPool1"
}
```

|Element|Typ|Poznámky|
|-------------|----------|-----------|
|`id`|Řetězec|ID fondu|
