---
author: baanders
description: zahrnout soubor pro vyčištění instance digitálních vláken Azure
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: c3c1b814b357a2e4b724590261657e485852f99c
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575673"
---
* **Pokud nepotřebujete žádné prostředky, které jste vytvořili v tomto kurzu**, můžete odstranit instanci digitálních vláken Azure a všechny další prostředky z tohoto článku pomocí příkazu [AZ Group Delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) . Tím se odstraní všechny prostředky Azure ve skupině prostředků i v samotné skupině prostředků.
    
    > [!IMPORTANT]
    > Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky.
    
    Otevřete [Azure Cloud Shell](https://shell.azure.com)a spuštěním následujícího příkazu odstraňte skupinu prostředků a vše, co obsahuje.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```