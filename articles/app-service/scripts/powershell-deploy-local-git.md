---
title: 'PowerShell: nasazení z místního úložiště Git'
description: Naučte se používat Azure PowerShell k automatizaci nasazení a správy App Service. Tato ukázka ukazuje, jak nasadit kód z místního úložiště Git.
tags: azure-service-management
ms.assetid: 5a927f23-8e70-45fd-9aae-980d4e7a007d
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: 7355f2730cc54ca027e20c48e989e9f28dac7487
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "74684840"
---
# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>Vytvoření webové aplikace a nasazení kódu z místního úložiště Git

Tento ukázkový skript vytvoří ve službě App Service webovou aplikaci se souvisejícími prostředky a pak nasadí kód vaší webové aplikace z místního úložiště Git.

V případě potřeby aktualizujte Azure PowerShell na nejnovější verzi podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview) a pak spuštěním příkazu `Connect-AzAccount` vytvořte připojení k Azure. Kód vaší aplikace je také potřeba potvrdit do místního úložiště Git.

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-local-git/deploy-local-git.ps1?highlight=1 "Create a web app and deploy code from a local Git repository")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků, webovou aplikaci a všechny související prostředky.

```powershell
Remove-AzResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Vytvoří webovou aplikaci s nezbytnou skupinou prostředků a skupinou služby App Service. Pokud aktuální adresář obsahuje úložiště Git, přidejte také vzdálené připojení `azure`. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure App Service Web Apps najdete v [ukázkách Azure PowerShellu](../samples-powershell.md).
