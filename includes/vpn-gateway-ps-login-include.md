---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d4d370e6b76fcfc502366642842bfeb923a13991
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313672"
---
Před zahájením této konfigurace, musíte se přihlásit k účtu Azure. Rutina vás vyzve k zadání pověření přihlášení k účtu Azure. Po přihlášení, se stáhne nastavení svého účtu, takže jsou k dispozici do prostředí Azure PowerShell. Další informace najdete v tématu [Použití prostředí Windows PowerShell s Resource Managerem](../articles/powershell-azure-resource-manager.md).

Pokud chcete přihlásit, otevřete konzolu prostředí PowerShell se zvýšenými oprávněními a připojení k vašemu účtu. Připojení vám usnadní následující ukázka:

```powershell
Connect-AzureRmAccount
```

Pokud máte více předplatných Azure, zkontrolujte předplatná pro daný účet.

```powershell
Get-AzureRmSubscription
```

Určete předplatné, které chcete použít.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```
