---
author: baanders
description: zahrnout soubor pro vyčištění základní instance digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 4c03ef942896dda63f678018cdd257024cfbb6d4
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95560900"
---
Pokud už prostředky vytvořené v tomto kurzu nepotřebujete, odstraňte je pomocí těchto kroků.

Pomocí [Azure Cloud Shell](https://shell.azure.com)můžete odstranit všechny prostředky Azure ve skupině prostředků pomocí příkazu [AZ Group Delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) . Tento příkaz odebere skupinu prostředků a instanci digitálních vláken Azure.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky.

Otevřete Azure Cloud Shell a spuštěním následujícího příkazu odstraňte skupinu prostředků a vše, co obsahuje.

```azurecli-interactive
az group delete --name <your-resource-group>
```