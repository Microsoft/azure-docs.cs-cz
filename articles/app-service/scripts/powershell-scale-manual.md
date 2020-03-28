---
title: 'PowerShell: Ruční škálování webové aplikace'
description: Zjistěte, jak pomocí Azure PowerShellu automatizovat nasazení a správu služby App Service. Tato ukázka ukazuje, jak ručně škálovat aplikaci.
author: msangapu-msft
tags: azure-service-management
ms.assetid: de5d4285-9c7d-4735-a695-288264047375
ms.topic: sample
ms.date: 03/20/2017
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: cb0af65f938baf24b2af574b21e09e099b70ba85
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80060286"
---
# <a name="scale-a-web-app-manually-using-powershell"></a>Ruční škálování webové aplikace pomocí PowerShellu

V tomto scénáři se dozvíte, jak vytvořit skupinu prostředků, plán služby App Service a webovou aplikaci. Pak budete plán služby App Service škálovat z jedné instance na několik instancí.

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview) a pak spuštěním rutiny `Connect-AzAccount` vytvořte připojení k Azure.

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/scale-manual/scale-manual.ps1 "Scale a web app manually")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků, webovou aplikaci a všechny související prostředky.

```powershell
Remove-AzResourceGroup -Name $ResourceGroupName -Force
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Nový-AzAppServicePlán](/powershell/module/az.websites/new-azappserviceplan) | Vytvoří plán služby App Service. |
| [Nový-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Vytvoří webovou aplikaci. |
| [Sada-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Upraví konfiguraci webové aplikace. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure App Service Web Apps najdete v [ukázkách Azure PowerShellu](../samples-powershell.md).
