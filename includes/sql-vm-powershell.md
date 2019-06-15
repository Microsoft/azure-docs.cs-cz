---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: c6666f4417cde9e0f77cc965ded1d6bdb5dced34
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66165825"
---
## <a name="start-your-powershell-session"></a>Spuštění relace prostředí PowerShell
 

Spustit [ **připojit Az Account** ](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount) rutinu se zobrazí přihlašovací obrazovku pro zadání přihlašovacích údajů. Použijte stejné přihlašovací údaje, pomocí kterých se přihlašujete na portál Azure.

```powershell
Connect-AzAccount
```

Pokud máte více předplatných, pomocí [ **Set-AzContext** ](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) rutiny vyberte, které předplatné má relace prostředí PowerShell použít. Chcete-li zjistit, jaké předplatné používá aktuální relace prostředí PowerShell, spusťte [ **Get-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext). Pokud chcete zobrazit všechna předplatná, spusťte [ **Get-AzSubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

