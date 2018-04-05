---
title: Události fondu počáteční delete Azure Batch | Microsoft Docs
description: Referenční informace pro odstranění fondu Batch, spusťte událost.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: danlep
ms.openlocfilehash: 8737b9ff6452730ff5a55fa7324e37f0fe715433
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="pool-delete-start-event"></a>Událost zahájení odstranění fondu

 Tato událost je vygenerované při operaci odstranění fondu bylo zahájeno. Vzhledem k tomu, že odstranění fondu je asynchronní událostí, můžete očekávat, že událost complete odstranění fondu pro vypuštění po dokončení operace odstranění.

 Následující příklad ukazuje tělo události spuštění odstranění fondu.

```
{
    "id": "myPool1"
}
```

|Element|Typ|Poznámky|
|-------------|----------|-----------|
|id|Řetězec|Id fondu.|