---
author: baanders
description: zahrnout soubor pro vyčištění základní instance digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 2e095a7b286a8860535d6b58fa93098735b30c87
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92372309"
---
Pokud už prostředky vytvořené v tomto kurzu nepotřebujete, odstraňte je pomocí těchto kroků.

Pomocí [Azure Cloud Shell](https://shell.azure.com)můžete odstranit všechny prostředky Azure ve skupině prostředků pomocí příkazu [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) . Tím se odebere skupina prostředků a instance digitálního vlákna Azure.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. 

Otevřete Azure Cloud Shell a spuštěním následujícího příkazu odstraňte skupinu prostředků a vše, co obsahuje.

```azurecli
az group delete --name <your-resource-group>
```