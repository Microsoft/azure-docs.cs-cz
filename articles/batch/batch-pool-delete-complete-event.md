---
title: Událost dokončení odstranění fondu Azure Batch | Dokumentace Microsoftu
description: Referenční informace pro fond služby Batch odstranit událost complete.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: e715ccd0f5e79f9c640a3c060b0252b798748b4d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474544"
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