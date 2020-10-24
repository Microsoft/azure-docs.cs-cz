---
author: baanders
description: zahrnout soubor pro vyčištění základní instance digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 29a07ffa917153c0cb062d34e2807d43d039a373
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494659"
---
Pokud už prostředky vytvořené v tomto kurzu nepotřebujete, odstraňte je pomocí těchto kroků.

Pomocí [Azure Cloud Shell](https://shell.azure.com)můžete odstranit všechny prostředky Azure ve skupině prostředků pomocí příkazu [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) . Tím se odebere skupina prostředků a instance digitálního vlákna Azure.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. 

Otevřete Azure Cloud Shell a spuštěním následujícího příkazu odstraňte skupinu prostředků a vše, co obsahuje.

```azurecli-interactive
az group delete --name <your-resource-group>
```