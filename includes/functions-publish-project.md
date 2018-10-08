---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/27/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 1b553cbd720fcb76899844712ce5053af46f7ccb
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452951"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Nasazení projektu aplikace funkcí do Azure

Až se aplikace funkcí vytvoří v Azure, můžete kód projektu nasadit do Azure pomocí příkazu [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment).

```bash
func azure functionapp publish <FunctionAppName>
```

Zobrazí se vám něco, co se podobá následujícímu výstupu, který jsme kvůli čitelnosti zkrátili.

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

Teď můžete své funkce otestovat v Azure.