---
author: baanders
description: zahrnutí souboru pro vyčištění základní instance a registrace aplikace v rámci služby Azure Digital revláken
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 45d6a806e94ceca9574b0ed5f73c2b87ef438438
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88891487"
---
Pokud už prostředky vytvořené v tomto kurzu nepotřebujete, odstraňte je pomocí těchto kroků.

Pomocí [Azure Cloud Shell](https://shell.azure.com)můžete odstranit všechny prostředky Azure ve skupině prostředků pomocí příkazu [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) . Tím se odebere skupina prostředků a instance digitálního vlákna Azure.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. 

Otevřete Azure Cloud Shell a spuštěním následujícího příkazu odstraňte skupinu prostředků a vše, co obsahuje.

```azurecli-interactive
az group delete --name <your-resource-group>
```

V dalším kroku odstraňte registraci aplikace Azure Active Directory, kterou jste vytvořili pro klientskou aplikaci, pomocí tohoto příkazu:

```azurecli-interactive
az ad app delete --id <your-application-ID>
```