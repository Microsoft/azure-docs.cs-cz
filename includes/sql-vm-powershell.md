---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: 27da28073b01c6bc43868172d6c989d9518d8f53
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559788"
---
## <a name="start-your-powershell-session"></a>Spuštění relace prostředí PowerShell
 

Spusťte rutinu [**Connect-AZ Account**](/powershell/module/Az.Accounts/Connect-AzAccount) a zobrazí se vám přihlašovací obrazovka pro zadání vašich přihlašovacích údajů. Použijte stejné přihlašovací údaje, pomocí kterých se přihlašujete na portál Azure.

```powershell
Connect-AzAccount
```

Pokud máte více předplatných, pomocí rutiny [**set-AzContext**](/powershell/module/az.accounts/set-azcontext) vyberte, které předplatné má relace prostředí PowerShell použít. Pokud chcete zjistit, jaké Předplatné používá aktuální relace prostředí PowerShell, spusťte rutinu [**Get-AzContext**](/powershell/module/az.accounts/get-azcontext). Pokud chcete zobrazit všechna předplatná, spusťte rutinu [**Get-AzSubscription**](/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```