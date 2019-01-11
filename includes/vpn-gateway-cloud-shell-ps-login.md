---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fee97b29f24d8bb4f50a2929c3ceb33af85a5e21
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201478"
---
Otevřete konzolu Powershellu se zvýšenými oprávněními.



Pokud používáte prostředí Azure PowerShell místně, připojte se ke svému účtu Azure. *Connect-AzureRmAccount* rutina vás vyzve k zadání přihlašovacích údajů. Po ověření, stáhne nastavení účtu, aby byly k dispozici pro prostředí Azure PowerShell. Tento první krok přeskočte, pokud nejsou místní použití Powershellu a místo toho používáte Azure Cloud Shell "Vyzkoušejte si to" v prohlížeči. Připojíte se ke svému účtu Azure automaticky.

```azurepowershell
Connect-AzureRmAccount
```

Pokud máte více předplatných, získejte seznam předplatných Azure.

```azurepowershell-interactive
Get-AzureRmSubscription
```

Určete předplatné, které chcete použít.

```azurepowershell-interactive
Select-AzureRmSubscription -SubscriptionName "Name of subscription"
```