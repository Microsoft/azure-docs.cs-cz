---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: c6666f4417cde9e0f77cc965ded1d6bdb5dced34
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67174965"
---
## <a name="start-your-powershell-session"></a>Spuštění relace prostředí PowerShell
 

Spusťte rutinu [**Connect-AZ Account**](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount) a zobrazí se vám přihlašovací obrazovka pro zadání vašich přihlašovacích údajů. Použijte stejné přihlašovací údaje, pomocí kterých se přihlašujete na portál Azure.

```powershell
Connect-AzAccount
```

Pokud máte více předplatných, pomocí rutiny [**set-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) vyberte, které předplatné má relace prostředí PowerShell použít. Pokud chcete zjistit, jaké Předplatné používá aktuální relace prostředí PowerShell, spusťte rutinu [**Get-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext). Pokud chcete zobrazit všechna předplatná, spusťte rutinu [**Get-AzSubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

