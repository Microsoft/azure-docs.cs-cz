---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4c9b579534d9a7f2c55e9c589b1738fe060b1cf2
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
Při vývoji funkce místně, můžete nainstalovat rozšíření, která je nutné pomocí nástroje Azure funkce základní z terminálu nebo z příkazového řádku. Následující `func extensions install` příkaz nainstaluje rozšíření vazby Cosmos databázi Azure:

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

Nahraďte `<taget_version>` s určitou verzí balíčku. Neplatná verze jsou uvedené na stránkách individuální balíčku na [NuGet.org](https://nuget.org).
