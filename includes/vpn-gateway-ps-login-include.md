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
ms.openlocfilehash: 188f70d17531a3a7da12444ce5f1c97385ea33bc
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418037"
---
Před zahájením této konfigurace, musíte se přihlásit ke svému účtu Azure. Rutina vás vyzve k zadání přihlašovací údaje ke svému účtu Azure. Po přihlášení se stáhne nastavení účtu, takže jsou k dispozici pro prostředí Azure PowerShell. Další informace najdete v tématu [Použití prostředí Windows PowerShell s Resource Managerem](../articles/powershell-azure-resource-manager.md).

Pro přihlášení, otevřete konzolu Powershellu se zvýšenými oprávněními a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

```powershell
Connect-AzAccount
```

Pokud máte více předplatných Azure, zkontrolujte předplatná pro daný účet.

```powershell
Get-AzSubscription
```

Určete předplatné, které chcete použít.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```
