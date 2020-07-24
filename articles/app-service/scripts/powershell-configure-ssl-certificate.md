---
title: 'PowerShell: nahrání a vázání TLS/SSL'
description: Naučte se používat Azure PowerShell k automatizaci nasazení a správy App Service. V této ukázce se dozvíte, jak vytvořit vazby vlastního certifikátu TLS/SSL k aplikaci.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, seodec18
ms.openlocfilehash: c0e68627691732e6cbcd08b423199164a69193d4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083340"
---
# <a name="bind-a-custom-tlsssl-certificate-to-a-web-app-using-powershell"></a>Vytvoření vazby vlastního certifikátu TLS/SSL k webové aplikaci pomocí PowerShellu

Tento ukázkový skript vytvoří ve App Service webovou aplikaci se souvisejícími prostředky a pak naváže certifikát TLS/SSL vlastního názvu domény. 

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/) a pak spuštěním rutiny `Connect-AzAccount` vytvořte připojení k Azure. Zajistěte také následující:

- Vytvoření připojení k Azure pomocí příkazu `az login`.
- Přístup ke konfigurační stránce DNS doménového registrátora.
- Máte platnou. Soubor PFX a jeho heslo pro certifikát TLS/SSL, který chcete nahrát a vytvořit.

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

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
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Upraví konfiguraci webové aplikace. |
| [New-AzWebAppSSLBinding](/powershell/module/az.websites/new-azwebappsslbinding) | Vytvoří vazbu certifikátu TLS/SSL pro webovou aplikaci. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/).

Další ukázky Azure PowerShellu pro Azure App Service Web Apps najdete v [ukázkách Azure PowerShellu](../samples-powershell.md).
