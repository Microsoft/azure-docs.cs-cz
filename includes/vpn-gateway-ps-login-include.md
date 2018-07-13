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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38732670"
---
Před zahájením této konfigurace, musíte se přihlásit ke svému účtu Azure. Rutina vás vyzve k zadání přihlašovací údaje ke svému účtu Azure. Po přihlášení se stáhne nastavení účtu, takže jsou k dispozici pro prostředí Azure PowerShell. Další informace najdete v tématu [Použití prostředí Windows PowerShell s Resource Managerem](../articles/powershell-azure-resource-manager.md).

Pro přihlášení, otevřete konzolu Powershellu se zvýšenými oprávněními a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

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
