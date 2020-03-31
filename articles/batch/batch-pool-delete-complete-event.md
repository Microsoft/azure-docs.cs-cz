---
title: Událost dokončení odstranění fondu dávek Azure
description: Odkaz na batch fond odstranit úplnou událost. Tato událost je vyzařována po dokončení operace odstranění fondu.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: d317d7395a8246c109073a72338b55960cb50954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023612"
---
# <a name="pool-delete-complete-event"></a>Událost dokončení odstranění fondu

 Tato událost je vyzařována po dokončení operace odstranění fondu.

 Následující příklad ukazuje tělo fondu odstranit úplnou událost.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Element|Typ|Poznámky|
|-------------|----------|-----------|
|`id`|Řetězec|ID bazénu.|
|`startTime`|DateTime|Čas spuštění odstranění fondu.|
|`endTime`|DateTime|Čas dokončení odstranění fondu.|

## <a name="remarks"></a>Poznámky
Další informace o stavech a kódech chyb pro operaci změny velikosti fondu naleznete [v tématu Odstranění fondu z účtu](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).
