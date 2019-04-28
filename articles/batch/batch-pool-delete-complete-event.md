---
title: Událost dokončení odstranění fondu Azure Batch | Dokumentace Microsoftu
description: Referenční informace pro fond služby Batch odstranit událost complete.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
origin.date: 04/20/2017
ms.date: 05/14/2018
ms.author: v-junlch
ms.openlocfilehash: e715ccd0f5e79f9c640a3c060b0252b798748b4d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775759"
---
# <a name="pool-delete-complete-event"></a>Událost dokončení odstranění fondu

 Tato událost je vygenerován při dokončení operace odstranění fondu.

 Následující příklad ukazuje tělo událost dokončení odstranění fondu.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Element|Type|Poznámky|
|-------------|----------|-----------|
|id|String|Id fondu.|
|startTime|DateTime|Čas odstranění fondu spuštění.|
|endTime|DateTime|Čas odstranění fondu dokončit.|

## <a name="remarks"></a>Poznámky
Další informace o stavu a kódy chyb pro operace změny velikosti fondu najdete v tématu [odstranění fondu z účtu](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).

<!-- Update_Description: update metedata properties -->