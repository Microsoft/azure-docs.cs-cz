---
author: baanders
description: zahrnout soubor pro vyčištění instance digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 0d8cc30c0511098caf7b6c47d7f7bd400dc32f1b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799880"
---
* **Pokud nepotřebujete žádné prostředky, které jste vytvořili v tomto kurzu**, můžete odstranit instanci digitálních vláken Azure a všechny další prostředky z tohoto článku pomocí příkazu [AZ Group Delete](/cli/azure/group#az_group_delete) . Tím se odstraní všechny prostředky Azure ve skupině prostředků i v samotné skupině prostředků.
    
    > [!IMPORTANT]
    > Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky.
    
    Otevřete [Azure Cloud Shell](https://shell.azure.com)a spuštěním následujícího příkazu odstraňte skupinu prostředků a vše, co obsahuje.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```