---
title: "Vytvořte standardní veřejné zatížení na vyrovnávání s zónově redundantní veřejná IP adresa front-endu pomocí prostředí PowerShell | Microsoft Docs"
description: "Naučte se vytvářet vyrovnávání standardní veřejné zatížení s zónově redundantní front-end veřejnou IP adresu pomocí prostředí PowerShell"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.openlocfilehash: abe970eb72be5ea6944acc253c2c3d9008b20c56
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-powershell"></a>Vytvořte standardní veřejné zatížení na vyrovnávání s zónově redundantní veřejná IP adresa front-endu pomocí prostředí PowerShell

Tento článek popisuje postup procesem vytvoření veřejné [standardní nástroje pro vyrovnávání zatížení](https://aka.ms/azureloadbalancerstandard) s zónově redundantní front-end pomocí standardní veřejné IP adresy.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>Registrace pro dostupnost zóny, standardní nástroje pro vyrovnávání zatížení a veřejnou IP adresu standardní Preview

Tento článek vyžaduje, že máte verzi 4.4.0 nebo vyšší modulu AzureRM nainstalovat. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete nainstalovat nebo upgradovat, nainstalujte nejnovější verzi modulu AzureRM z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureRM).

>[!NOTE]
[Standardní SKU pro vyrovnávání zatížení](https://aka.ms/azureloadbalancerstandard) je aktuálně ve verzi Preview. Během období Preview tato funkce nemusí dosahovat stejné úrovně dostupnosti a spolehlivosti jako funkce, které jsou ve verzi všeobecné dostupnosti. Další informace najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Použít obecně dostupná [základní SKU služby Vyrovnávání zatížení](load-balancer-overview.md) pro vaše produkční služby. 

> [!NOTE]
> Dostupnost zóny jsou ve verzi preview a jsou připraveny pro váš vývojový a testovací scénáře. Podpora je k dispozici pro vyberte prostředků Azure a oblasti a rodiny velikost virtuálního počítače. Další informace o tom, jak začít pracovat a které prostředky Azure, oblasti a rodiny velikost virtuálního počítače můžete zkusit dostupnost zóny s, najdete v části [přehled dostupnosti zón](https://docs.microsoft.com/azure/availability-zones/az-overview). Pokud budete potřebovat podporu, můžete kontaktovat [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) nebo [otevřít lístek podpory Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

Před výběrem zóny nebo zónově redundantní možnost pro veřejnou IP adresu front-endu nástroje pro vyrovnávání zatížení, musíte nejdřív dokončit kroky v [zaregistrovat verzi Preview zón dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí příkazu `Login-AzureRmAccount` a postupujte podle pokynů na obrazovce.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvoření skupiny prostředků pomocí následujícího příkazu:

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>Vytvoření veřejné standardní IP 
Vytvoření veřejné IP standardní pomocí následujícího příkazu:

```powershell
$publicIp = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Name 'myPublicIP' `
  -Location westeurope -AllocationMethod Static -Sku Standard 
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>Vytvořte konfiguraci front-end IP adresy pro web

Vytvoření konfigurace IP front-endu pomocí následujícího příkazu:

```powershell
$feip = New-AzureRmLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>Vytvořit fond adres back-end

Vytvořte fond adres back-end pomocí následujícího příkazu:

```powershell
$bepool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>Vytvořit sondu nástroje pro vyrovnávání zatížení na portu 80

Vytvořte test stavu na portu 80 pro vyrovnávání zatížení pomocí následujícího příkazu:

```powershell
$probe = New-AzureRmLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení
 Vytvořte pravidlo služby load balancer pomocí následujícího příkazu:

```powershell
   $rule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $feip -BackendAddressPool  $bepool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

## <a name="create-a-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení
Vytvoření zatížení vyrovnávání standardní pomocí následujícího příkazu:

```powershell
$lb = New-AzureRmLoadBalancer -ResourceGroupName myResourceGroup -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>Další postup
- Zjistěte, jak [vytvořit veřejnou IP adresu v zóně dostupnosti](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)



