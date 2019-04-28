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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60364740"
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