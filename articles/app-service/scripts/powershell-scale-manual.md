---
title: 'PowerShell: ruční škálování webové aplikace'
description: Naučte se používat Azure PowerShell k automatizaci nasazení a správy App Service. V této ukázce se dozvíte, jak ručně škálovat aplikaci.
author: msangapu-msft
tags: azure-service-management
ms.assetid: de5d4285-9c7d-4735-a695-288264047375
ms.topic: sample
ms.date: 03/20/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurepowershell
ms.openlocfilehash: 1b88e5928d276b58339cb0266505295808bc7a7c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335479"
---
# <a name="scale-a-web-app-manually-using-powershell"></a>Ruční škálování webové aplikace pomocí PowerShellu

V tomto scénáři se dozvíte, jak vytvořit skupinu prostředků, plán služby App Service a webovou aplikaci. Pak budete plán služby App Service škálovat z jedné instance na několik instancí.

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/) a pak spuštěním rutiny `Connect-AzAccount` vytvořte připojení k Azure.

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
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Vytvoří plán služby App Service. |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | Upraví konfiguraci App Serviceho plánu. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Vytvoří webovou aplikaci. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Upraví konfiguraci webové aplikace. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/).

Další ukázky Azure PowerShellu pro Azure App Service Web Apps najdete v [ukázkách Azure PowerShellu](../samples-powershell.md).
