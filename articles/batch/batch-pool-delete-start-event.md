---
title: Událost zahájení odstranění fondu sady Azure Batch | Dokumentace Microsoftu
description: Referenční informace pro událost zahájení odstranění fondu služby Batch.
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
ms.openlocfilehash: 2352971af3844b56f93c16ebaf6cb23bd5fd8a5a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774535"
---
# <a name="pool-delete-start-event"></a>Událost zahájení odstranění fondu

 Tato událost je vygenerován při spuštění operace odstranění fondu. Odstranění fondu je asynchronní události, můžete očekávat událost dokončení odstranění fondu emitování po dokončení operace odstranění.

 Následující příklad ukazuje tělo událost zahájení odstranění fondu.

```
{
    "id": "myPool1"
}
```

|Element|Type|Poznámky|
|-------------|----------|-----------|
|id|String|Id fondu.|

<!-- Update_Description: update metedata properties -->