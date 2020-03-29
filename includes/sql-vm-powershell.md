---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: c6666f4417cde9e0f77cc965ded1d6bdb5dced34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174965"
---
## <a name="start-your-powershell-session"></a>Spuštění relace prostředí PowerShell
 

Spusťte rutinu [**účtu Connect-Az**](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount) a zobrazí se vám přihlašovací obrazovka pro zadání přihlašovacích údajů. Použijte stejné přihlašovací údaje, pomocí kterých se přihlašujete na portál Azure.

```powershell
Connect-AzAccount
```

Pokud máte více odběrů, použijte rutinu [**Set-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) k výběru předplatného, které by měla vaše relace Prostředí PowerShell použít. Chcete-li zjistit, jaké předplatné používá aktuální relace prostředí PowerShell, spusťte [**get-azcontext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext). Chcete-li zobrazit všechna vaše předplatná, spusťte [**Get-AzSubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

