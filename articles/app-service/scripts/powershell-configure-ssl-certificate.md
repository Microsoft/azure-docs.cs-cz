---
title: 'PowerShell: Nahrávání a svázání TLS/SSL'
description: Zjistěte, jak pomocí Azure PowerShellu automatizovat nasazení a správu služby App Service. Tato ukázka ukazuje, jak svázat vlastní certifikát TLS/SSL s aplikací.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, seodec18
ms.openlocfilehash: 5116585b701717a82b757cae70f938c321a1f7d1
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532490"
---
# <a name="bind-a-custom-tlsssl-certificate-to-a-web-app-using-powershell"></a>Vytvoření vytvoření vlastního certifikátu TLS/SSL s webovou aplikací pomocí PowerShellu

Tento ukázkový skript vytvoří webovou aplikaci ve službě App Service se souvisejícími prostředky a poté s váže certifikát TLS/SSL vlastního názvu domény. 

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview) a pak spuštěním rutiny `Connect-AzAccount` vytvořte připojení k Azure. Zajistěte také následující:

- Vytvoření připojení k Azure pomocí příkazu `az login`.
- Přístup ke konfigurační stránce DNS doménového registrátora.
- Máte platný . PFX a jeho heslo pro certifikát TLS/SSL, který chcete nahrát a svázat.

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
| [Nový-AzAppServicePlán](/powershell/module/az.websites/new-azappserviceplan) | Vytvoří plán služby App Service. |
| [Nový-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Vytvoří webovou aplikaci. |
| [Set-AzAppServicePlán](/powershell/module/az.websites/set-azappserviceplan) | Upraví plán služby App Service a změní jeho cenovou úroveň. |
| [Sada-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Upraví konfiguraci webové aplikace. |
| [Nová-AzWebAppSSLVazba](/powershell/module/az.websites/new-azwebappsslbinding) | Vytvoří vazbu certifikátu TLS/SSL pro webovou aplikaci. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure App Service Web Apps najdete v [ukázkách Azure PowerShellu](../samples-powershell.md).
