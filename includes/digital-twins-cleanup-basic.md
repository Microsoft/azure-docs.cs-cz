---
author: baanders
description: zahrnout soubor pro vyčištění instance digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 9a02c4f5c5699b4a6308bfaa519fa9eb776414d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102244754"
---
* **Pokud nepotřebujete žádné prostředky, které jste vytvořili v tomto kurzu**, můžete odstranit instanci digitálních vláken Azure a všechny další prostředky z tohoto článku pomocí příkazu [AZ Group Delete](/cli/azure/group#az-group-delete) . Tím se odstraní všechny prostředky Azure ve skupině prostředků i v samotné skupině prostředků.
    
    > [!IMPORTANT]
    > Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky.
    
    Otevřete [Azure Cloud Shell](https://shell.azure.com)a spuštěním následujícího příkazu odstraňte skupinu prostředků a vše, co obsahuje.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```