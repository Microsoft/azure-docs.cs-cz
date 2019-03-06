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
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57410669"
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