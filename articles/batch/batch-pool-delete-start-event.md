---
title: Událost zahájení odstranění fondu Azure Batch
description: Referenční informace pro událost zahájení odstranění fondu Batch Tato událost je generována při spuštění operace odstranění fondu.
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: 86f6eb8e7b269cb45f692398e9e60390375ca073
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97803744"
---
# <a name="pool-delete-start-event"></a>Událost zahájení odstranění fondu

 Tato událost je generována při spuštění operace odstranění fondu. Vzhledem k tomu, že odstranění fondu představuje asynchronní událost, můžete očekávat, že událost odstranění fondu bude po dokončení operace odstranění vygenerována.

 Následující příklad ukazuje tělo události spuštění odstranění fondu.

```
{
   "id": "myPool1"
}
```

|Prvek|Typ|Poznámky|
|-------------|----------|-----------|
|`id`|Řetězec|ID fondu|
