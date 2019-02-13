---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: 3dc799ecc75589279c8d1c73062a8f2157761330
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56212980"
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

