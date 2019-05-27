---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 26789a12053fa6275b09836e706c391e181c8efd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132153"
---
## <a name="create-a-function"></a>Vytvořit funkci

Následující příkaz vytvoří funkci aktivovanou HTTP s názvem `MyHttpTrigger`.

```bash
func new --name MyHttpTrigger --template "HttpTrigger"
```

Při spuštění příkazu se zobrazí něco jako následující výstup:

```output
The function "MyHttpTrigger" was created successfully from the "HttpTrigger" template.
```
