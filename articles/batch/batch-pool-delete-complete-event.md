---
title: Událost dokončení odstranění fondu Azure Batch
description: Odkaz na událost dokončení odstranění fondu Batch Tato událost je vyvolána, když je dokončena operace odstranění fondu.
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
ms.openlocfilehash: 56dc4c91566cc5c41de6c91618d09e7a0ebb7172
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76929859"
---
# <a name="pool-delete-complete-event"></a>Událost dokončení odstranění fondu

 Tato událost je vyvolána, když je dokončena operace odstranění fondu.

 Následující příklad ukazuje tělo události odstranění fondu po dokončení.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Element|Typ|Poznámky|
|-------------|----------|-----------|
|`id`|Řetězec|ID fondu|
|`startTime`|Datum a čas|Čas zahájení odstranění fondu|
|`endTime`|Datum a čas|Čas dokončení odstranění fondu|

## <a name="remarks"></a>Poznámky
Další informace o stavech a kódech chyb pro operaci změny velikosti fondu najdete v tématu [odstranění fondu z účtu](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).
