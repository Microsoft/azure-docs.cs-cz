---
title: Skript Azure Powershellu ukázkový – vytvoření aplikace a nasazení kódu do přípravného slotu | Dokumentace Microsoftu
description: Ukázkový skript Azure PowerShellu – Vytvoření webové aplikace a nasazení kódu do přípravného prostředí
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 27cf0680-c3a9-4a58-9f71-6dec09f6b874
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 79d5efc9e30bd5bd12e8ea89da942fd0b5ad160c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "66136423"
---
# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>Vytvoření webové aplikace a nasazení kódu do přípravného prostředí

Tento ukázkový skript vytvoří ve službě App Service webovou aplikaci s dalším slotem nasazení „staging“ a pak do slotu „staging“ nasadí ukázkovou aplikaci.

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview) a pak spuštěním rutiny `Connect-AzAccount` vytvořte připojení k Azure.

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.ps1?highlight=1 "Create a web app and deploy code to a staging environment")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků, webovou aplikaci a všechny související prostředky.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Vytvoří plán služby App Service. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Vytvoří webovou aplikaci. |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | Upraví plán služby App Service a změní jeho cenovou úroveň. |
| [New-AzWebAppSlot](/powershell/module/az.websites/new-azwebappslot) | Vytvoří slot nasazení pro webovou aplikaci. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Upraví prostředek ve skupině prostředků. |
| [Switch-AzWebAppSlot](/powershell/module/az.websites/switch-azwebappslot) | Přepne slot nasazení webové aplikace do produkčního prostředí. |

## <a name="next-steps"></a>Další postup

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure App Service Web Apps najdete v [ukázkách Azure PowerShellu](../samples-powershell.md).
