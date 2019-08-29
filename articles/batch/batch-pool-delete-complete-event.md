---
title: Událost dokončení odstranění fondu Azure Batch | Microsoft Docs
description: Odkaz na událost dokončení odstranění fondu Batch
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 3ad48fdba298a10d94a32e31e432c0c88b35b658
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094842"
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

|Prvek|type|Poznámky|
|-------------|----------|-----------|
|id|Řetězec|ID fondu|
|startTime|DateTime|Čas zahájení odstranění fondu|
|endTime|DateTime|Čas dokončení odstranění fondu|

## <a name="remarks"></a>Poznámky
Další informace o stavech a kódech chyb pro operaci změny velikosti fondu najdete v tématu [odstranění fondu z účtu](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).