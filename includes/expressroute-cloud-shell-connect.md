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
ms.openlocfilehash: 1aca39a7ff162aa3c42fdb3ca5999c71091ec02e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119393"
---
 Pokud používáte Azure Cloud Shell, přihlásíte ke svému účtu Azure automaticky po kliknutí na tlačítko "Vyzkoušejte si to". Přihlásit místně, otevřete konzolu Powershellu se zvýšenými oprávněními a spusťte rutinu pro připojení.

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