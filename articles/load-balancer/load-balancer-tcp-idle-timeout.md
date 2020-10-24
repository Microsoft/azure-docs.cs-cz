---
title: Konfigurace resetování TCP pro vyrovnávání zatížení a časový limit nečinnosti v Azure
titleSuffix: Azure Load Balancer
description: V tomto článku se dozvíte, jak nakonfigurovat Azure Load Balancer časový limit nečinnosti protokolu TCP.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/09/2020
ms.author: allensu
ms.openlocfilehash: b507fbad4d9089d918ae7a85c07f30efcb118476
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487241"
---
# <a name="configure-tcp-idle-timeout-for-azure-load-balancer"></a>Konfigurace časového limitu nečinnosti protokolu TCP pro Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte prostředí PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

Azure Load Balancer má následující rozsah časového limitu nečinnosti:

4 minuty až 100 minut pro odchozí pravidla 4 minuty až 30 minut pro pravidla Load Balancer a příchozí pravidla NAT

Ve výchozím nastavení je nastaveno na 4 minuty. Pokud je období neaktivity delší než hodnota časového limitu, není nijak zaručeno, že relace TCP nebo HTTP mezi klientem a vaší cloudovou službou bude zachovaná. Další informace o [vypršení časového limitu nečinnosti protokolu TCP](load-balancer-tcp-reset.md)

Následující části popisují, jak změnit nastavení časového limitu nečinnosti pro prostředky veřejné IP adresy a nástroje pro vyrovnávání zatížení.


## <a name="configure-the-tcp-idle-timeout-for-your-public-ip"></a>Konfigurace časového limitu nečinnosti protokolu TCP pro veřejnou IP adresu

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

Parametr `IdleTimeoutInMinutes` je volitelný. Pokud není nastavené, výchozí časový limit je 4 minuty. 

## <a name="set-the-tcp-idle-timeout-on-rules"></a>Nastavení časového limitu nečinnosti protokolu TCP u pravidel

Chcete-li nastavit časový limit nečinnosti pro nástroj pro vyrovnávání zatížení, je v pravidle vyrovnávání zatížení nastaven atribut ' IdleTimeoutInMinutes '. Například:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```

## <a name="next-steps"></a>Další kroky

[Interní přehled nástroje pro vyrovnávání zatížení](load-balancer-internal-overview.md)

[Začínáme s konfigurací internetového nástroje pro vyrovnávání zatížení](quickstart-load-balancer-standard-public-powershell.md)

[Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md)
