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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "67174740"
---
 Pokud používáte Azure Cloud Shell, přihlaste se k účtu Azure automaticky po kliknutí na tlačítko vyzkoušet. Pokud se chcete přihlásit místně, otevřete konzolu PowerShellu se zvýšenými oprávněními a spusťte rutinu pro připojení.

```azurepowershell
Connect-AzAccount
```

Pokud máte více než jedno předplatné, získejte seznam předplatných Azure.

```azurepowershell-interactive
Get-AzSubscription
```

Určete předplatné, které chcete použít.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```