---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 24d146da7946176c92902698d0f52ae01baf79ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93061596"
---
Pokud používáte PowerShell místně, otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se ke svému účtu Azure. Rutina *Connect-AzAccount* vás vyzve k zadání přihlašovacích údajů. Po ověření se stáhne nastavení vašeho účtu, aby bylo možné Azure PowerShell.

Pokud používáte Azure Cloud Shell prostředí PowerShell místně, budete si všimnout, že nemusíte spouštět rutinu *Connect-AzAccount*. Po výběru **vyzkoušet** se Azure Cloud Shell ke svému účtu Azure automaticky připojí.

1. Pokud používáte PowerShell místně, přihlaste se.

   ```azurepowershell
   Connect-AzAccount
   ```

1. Pokud máte více než jedno předplatné, získejte seznam předplatných Azure.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. Určete předplatné, které chcete použít.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
