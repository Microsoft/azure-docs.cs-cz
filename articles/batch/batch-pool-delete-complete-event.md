---
title: Událost dokončení odstranění fondu Azure Batch
description: Odkaz na událost dokončení odstranění fondu Batch Tato událost je vyvolána, když je dokončena operace odstranění fondu.
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: be6411a150ae6be424c0621eed768157154c7408
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97803727"
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

|Prvek|Typ|Poznámky|
|-------------|----------|-----------|
|`id`|Řetězec|ID fondu|
|`startTime`|DateTime|Čas zahájení odstranění fondu|
|`endTime`|DateTime|Čas dokončení odstranění fondu|

## <a name="remarks"></a>Poznámky

Další informace o stavech a kódech chyb pro operaci změny velikosti fondu najdete v tématu [odstranění fondu z účtu](/rest/api/batchservice/delete-a-pool-from-an-account).
