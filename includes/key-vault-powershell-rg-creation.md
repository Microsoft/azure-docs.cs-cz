---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 872164dfee9f35881fca97b0339bd95c64a2f5ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99070181"
---
Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Pomocí rutiny Azure PowerShell [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) vytvořte skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* . 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```
