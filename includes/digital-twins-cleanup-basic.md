---
author: baanders
description: zahrnout soubor pro vyčištění základní instance digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 4c03ef942896dda63f678018cdd257024cfbb6d4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011245"
---
Pokud už prostředky vytvořené v tomto kurzu nepotřebujete, odstraňte je pomocí těchto kroků.

Pomocí [Azure Cloud Shell](https://shell.azure.com)můžete odstranit všechny prostředky Azure ve skupině prostředků pomocí příkazu [AZ Group Delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) . Tento příkaz odebere skupinu prostředků a instanci digitálních vláken Azure.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky.

Otevřete Azure Cloud Shell a spuštěním následujícího příkazu odstraňte skupinu prostředků a vše, co obsahuje.

```azurecli-interactive
az group delete --name <your-resource-group>
```