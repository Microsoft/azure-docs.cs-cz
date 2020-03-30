---
title: Událost spuštění odstranění fondu dávek Azure
description: Odkaz na událost odstranění spuštění fondu dávek Tato událost je vyzařována při spuštění operace odstranění fondu.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022201"
---
# <a name="pool-delete-start-event"></a>Událost zahájení odstranění fondu

 Tato událost je vyzařována při spuštění operace odstranění fondu. Vzhledem k tomu, že odstranění fondu je asynchronní událost, můžete očekávat, že fond odstranit úplnou událost, která má být vyzařována po dokončení operace odstranění.

 Následující příklad ukazuje tělo události odstranění odstranění fondu.

```
{
    "id": "myPool1"
}
```

|Element|Typ|Poznámky|
|-------------|----------|-----------|
|`id`|Řetězec|ID bazénu.|
