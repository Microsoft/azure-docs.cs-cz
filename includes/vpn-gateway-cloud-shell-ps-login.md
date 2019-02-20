---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: deabef0c2c3540e515fe72a161710c95a20fa86f
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418060"
---
Otevřete konzolu Powershellu se zvýšenými oprávněními.



Pokud používáte prostředí Azure PowerShell místně, připojte se ke svému účtu Azure. *Připojit AzAccount* rutina vás vyzve k zadání přihlašovacích údajů. Po ověření, stáhne nastavení účtu, aby byly k dispozici pro prostředí Azure PowerShell. Tento první krok přeskočte, pokud nejsou místní použití Powershellu a místo toho používáte Azure Cloud Shell "Vyzkoušejte si to" v prohlížeči. Připojíte se ke svému účtu Azure automaticky.

```azurepowershell
Connect-AzAccount
```

Pokud máte více předplatných, získejte seznam předplatných Azure.

```azurepowershell-interactive
Get-AzSubscription
```

Určete předplatné, které chcete použít.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```