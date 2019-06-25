---
title: Událost zahájení odstranění fondu sady Azure Batch | Dokumentace Microsoftu
description: Referenční informace pro událost zahájení odstranění fondu služby Batch.
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
ms.openlocfilehash: 2352971af3844b56f93c16ebaf6cb23bd5fd8a5a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
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