---
title: Vytvoření Load Balancer s oblastmi front-endu-Azure PowerShell
titleSuffix: Azure Load Balancer
description: Naučte se vytvářet Standard Load Balancer s využitím s oblastmi front-endu pomocí Azure PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms.openlocfilehash: 8fa73479c5bbe73f0cb6c828a0552bb8dca80d3f
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894503"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-powershell"></a>Vytvoření Standard Load Balancer s použitím Zona front-endu pomocí Azure PowerShell

Tento článek vás provede vytvořením veřejné [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) s Zona front-end s použitím standardní IP adresy veřejné IP adresy. Vysvětlení fungování zón dostupnosti s Load balanceru úrovně Standard najdete v tématu [Load balanceru úrovně Standard a dostupnost zóny](load-balancer-standard-availability-zones.md). 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!NOTE]
> Podpora pro zóny dostupnosti je k dispozici pro vyberte prostředky Azure a oblasti a velikostní řady virtuálních počítačů. Další informace o tom, jak začít a které prostředky Azure, oblasti a velikostní řady virtuálních počítačů můžete vyzkoušet zóny dostupnosti s, najdete v části [přehledu zón dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview). Pokud budete potřebovat podporu, můžete kontaktovat [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) nebo [otevřít lístek podpory Azure](../azure-portal/supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Přihlaste se k předplatnému Azure pomocí příkazu `Connect-AzAccount` a postupujte podle pokynů na obrazovce.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí následujícího příkazu:

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupZLB -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>Vytvoření veřejného standardní IP 
Vytvořte veřejné IP Standard pomocí následujícího příkazu:

```azurepowershell-interactive
$publicIp = New-AzPublicIpAddress -ResourceGroupName myResourceGroupZLB -Name 'myPublicIPZonal' `
  -Location westeurope -AllocationMethod Static -Sku Standard -zone 1
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>Vytvořte front-endovou konfiguraci protokolu IP pro web

Vytvoření konfigurace IP front-endu pomocí následujícího příkazu:

```azurepowershell-interactive
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEnd' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>Vytvoření fondu back endových adres

Vytvořte fond back-end adres pomocí následujícího příkazu:

```azurepowershell-interactive
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>Vytvoření testu paměti nástroje pro vyrovnávání zatížení na portu 80

Vytvoření sondy stavu na portu 80 pro nástroj pro vyrovnávání zatížení pomocí následujícího příkazu:

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení
 Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí následujícího příkazu:

```azurepowershell-interactive
   $rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $feip -BackendAddressPool  $bepool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

## <a name="create-a-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení
Pomocí následujícího příkazu vytvořte Standard Load Balancer:

```azurepowershell-interactive
$lb = New-AzLoadBalancer -ResourceGroupName myResourceGroupZLB -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>Další kroky
- Další informace o [Load balanceru úrovně Standard a dostupnost zóny](load-balancer-standard-availability-zones.md).