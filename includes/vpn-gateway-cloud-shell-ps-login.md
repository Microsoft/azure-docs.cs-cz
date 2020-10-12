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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77133607"
---
Otevřete konzolu PowerShellu se zvýšenými oprávněními.

Pokud používáte Azure PowerShell místně, připojte se k účtu Azure. Rutina *Connect-AzAccount* vás vyzve k zadání přihlašovacích údajů. Po ověření se stáhne nastavení vašeho účtu, aby bylo možné Azure PowerShell. Pokud místo toho používáte Azure Cloud Shell, nemusíte spouštět *Connect-AzAccount*. Azure Cloud Shell se automaticky připojí k účtu Azure.

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