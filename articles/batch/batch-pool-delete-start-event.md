---
title: Událost zahájení odstranění fondu Azure Batch
description: Referenční informace pro událost zahájení odstranění fondu Batch Tato událost je generována při spuštění operace odstranění fondu.
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
ms.openlocfilehash: 24a68c6656bd13f0c353d53870a51cdc940fd141
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022201"
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
