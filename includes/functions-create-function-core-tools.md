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
ms.openlocfilehash: fa1046e8d752e133813924957b439b63fc2acfbd
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987960"
---
## <a name="create-a-function"></a>Vytvoření funkce

Následující příkaz vytvoří funkci aktivovanou HTTP s názvem `MyHtpTrigger`.

```bash
func new --name MyHttpTrigger --template "HttpTrigger"
```

Při spuštění příkazu se zobrazí něco jako následující výstup:

```output
The function "MyHttpTrigger" was created successfully from the "HttpTrigger" template.
```