---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: f9a45da2703518000aa464da067c5cf71a198fd4
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984966"
---
## <a name="start-your-powershell-session"></a>Spuštění relace prostředí PowerShell
 

Spustit [ **připojit Az Account** ](https://docs.microsoft.com/powershell/module/az.accounts/connect-azmaccount) rutinu se zobrazí přihlašovací obrazovku pro zadání přihlašovacích údajů. Použijte stejné přihlašovací údaje, pomocí kterých se přihlašujete na portál Azure.

```powershell
Connect-AzAccount
```

Pokud máte více předplatných, pomocí [ **Set-AzContext** ](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) rutiny vyberte, které předplatné má relace prostředí PowerShell použít. Chcete-li zjistit, jaké předplatné používá aktuální relace prostředí PowerShell, spusťte [ **Get-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext). Pokud chcete zobrazit všechna předplatná, spusťte [ **Get-AzSubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

