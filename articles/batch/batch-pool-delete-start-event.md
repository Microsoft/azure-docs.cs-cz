---
title: Událost zahájení odstranění fondu Azure Batch | Microsoft Docs
description: Referenční informace pro událost zahájení odstranění fondu Batch
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
ms.openlocfilehash: 76f3a15d88166160f28461c34ee03ff7c8880a59
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258413"
---
# <a name="pool-delete-start-event"></a>Událost zahájení odstranění fondu

 Tato událost je generována při spuštění operace odstranění fondu. Vzhledem k tomu, že odstranění fondu představuje asynchronní událost, můžete očekávat, že událost odstranění fondu bude po dokončení operace odstranění vygenerována.

 Následující příklad ukazuje tělo události spuštění odstranění fondu.

```
{
    "id": "myPool1"
}
```

|Prvek|type|Poznámky|
|-------------|----------|-----------|
|`id`|Řetězec|ID fondu|
