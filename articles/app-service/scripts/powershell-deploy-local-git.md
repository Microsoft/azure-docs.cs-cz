---
title: Skript Azure Powershellu ukázkový – vytvoření aplikace a nasazení z místního úložiště Git | Dokumentace Microsoftu
description: Ukázkový skript Azure PowerShellu – Vytvoření webové aplikace a nasazení kódu z místního úložiště Git
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 5a927f23-8e70-45fd-9aae-980d4e7a007d
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 8103777b85d8e11416811c694103c58755f1a23a
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56114429"
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

## <a name="next-steps"></a>Další postup

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure App Service Web Apps najdete v [ukázkách Azure PowerShellu](../samples-powershell.md).
