---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: e81cdb478a63e1e584aef2c32754bd321d245365
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440070"
---
## <a name="start-your-powershell-session"></a>Spuštění relace prostředí PowerShell
Nejdřív je potřeba mít na nejnovější verzi [prostředí Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) nainstalovaná a spuštěná. Podrobné informace najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> V příkladech v tomto tématu využívají [modelu nasazení Azure Resource Manageru](../articles/azure-resource-manager/resource-group-overview.md), takže se používají příklady [rutiny Azure Resource Manageru](https://msdn.microsoft.com/library/azure/mt125356.aspx). 
> 
> 

Spustit [ **Connect-AzureRmAccount** ](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) rutinu se zobrazí přihlašovací obrazovku pro zadání přihlašovacích údajů. Použijte stejné přihlašovací údaje, pomocí kterých se přihlašujete na portál Azure.

    Connect-AzureRmAccount

Pokud máte víc předplatných, pomocí rutiny [**Set-AzureRmContext**](https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermcontext) vyberte, které předplatné má relace prostředí PowerShell použít. Pokud chcete zjistit, jaké předplatné používá aktuální relace prostředí PowerShell, spusťte rutinu [**Get-AzureRmContext**](https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermcontext). Pokud chcete zobrazit všechna předplatná, spusťte rutinu [**Get-AzureRmSubscription**](https://docs.microsoft.com/powershell/module/servicemanagement/azurerm.profile/get-azurermsubscription).

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'

