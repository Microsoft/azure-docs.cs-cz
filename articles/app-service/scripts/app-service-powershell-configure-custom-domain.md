---
title: Ukázkový skript Azure PowerShellu – Přiřazení vlastní domény k webové aplikaci | Microsoft Docs
description: Ukázkový skript Azure PowerShellu – Přiřazení vlastní domény k webové aplikaci
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 356f5af9-f62e-411c-8b24-deba05214103
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 52c4753ad60c88531f9d645b9133a4bdf440b2b0
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190029"
---
# <a name="assign-a-custom-domain-to-a-web-app-using-powershell"></a>Přiřazení vlastní domény do webové aplikace pomocí Powershellu

Tento ukázkový skript vytvoří ve službě App Service webovou aplikaci se souvisejícími prostředky a pak na ni namapuje doménu `www.<yourdomain>`. 

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview) a pak spuštěním rutiny `Connect-AzureRmAccount` vytvořte připojení k Azure. Potřebujete také přístup ke konfigurační stránce DNS doménového registrátora.

## <a name="sample-script"></a>Ukázkový skript

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/map-custom-domain/map-custom-domain.ps1?highlight=1 "Assign a custom domain to a web app")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků, webovou aplikaci a všechny související prostředky.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Vytvoří plán služby App Service. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Vytvoří webovou aplikaci. |
| [Set-AzureRmAppServicePlan](/powershell/module/azurerm.websites/set-azurermappserviceplan) | Upraví plán služby App Service a změní jeho cenovou úroveň. |
| [Set-AzureRmWebApp](/powershell/module/azurerm.websites/set-azurermwebapp) | Upraví konfiguraci webové aplikace. |

## <a name="next-steps"></a>Další postup

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure App Service Web Apps najdete v [ukázkách Azure PowerShellu](../app-service-powershell-samples.md).
