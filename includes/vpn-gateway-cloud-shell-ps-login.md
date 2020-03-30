---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 50ce8530aca40eed07741f35be1a57bbd7cc1868
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77133607"
---
Otevřete konzolu PowerShell se zvýšenými oprávněními.

Pokud používáte Azure PowerShell místně, připojte se ke svému účtu Azure. Rutina *Connect-AzAccount* zobrazí výzvu k zadání pověření. Po ověření stáhne nastavení vašeho účtu, aby byly dostupné pro Azure PowerShell. Pokud místo toho používáte Azure Cloud Shell, nemusíte spouštět *Connect-AzAccount*. Azure Cloud Shell se automaticky připojí k vašemu účtu Azure.

```azurepowershell
Connect-AzAccount
```

Pokud máte víc než jedno předplatné, získejte seznam předplatných Azure.

```azurepowershell-interactive
Get-AzSubscription
```

Určete předplatné, které chcete použít.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```