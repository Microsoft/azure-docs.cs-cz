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
ms.openlocfilehash: 83f3def8f4972f35df25dc98f4b818e703801d2e
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949937"
---
## <a name="create-a-function"></a>Vytvoření funkce

Následující příkaz vytvoří funkci aktivovanou HTTP s názvem `MyHttpTrigger`.

```command
func new --name MyHttpTrigger --template "HttpTrigger"
```

Při spuštění příkazu se zobrazí něco jako následující výstup:

```output
The function "MyHttpTrigger" was created successfully from the "HttpTrigger" template.
```
