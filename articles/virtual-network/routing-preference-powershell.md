---
title: Konfigurace předvolby směrování pro veřejnou IP adresu – Azure PowerShell
titlesuffix: Azure Virtual Network
description: Naučte se konfigurovat předvolby směrování pro veřejnou IP adresu pomocí Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: e856e3fc253800b71176dad3db40e7a13a7e6140
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84707867"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-powershell"></a>Konfigurace předvolby směrování pro veřejnou IP adresu pomocí Azure PowerShell

V tomto článku se dozvíte, jak nakonfigurovat předvolby směrování prostřednictvím sítě poskytovatele internetových služeb (možnost**Internet** ) pro veřejnou IP adresu pomocí Azure PowerShell. Po vytvoření veřejné IP adresy ji můžete přidružit k následujícím prostředkům Azure pro příchozí a odchozí provoz do Internetu:

* Virtuální počítač
* Škálovací sada virtuálních počítačů
* Azure Kubernetes Service (AKS)
* Internetový nástroj pro vyrovnávání zatížení
* Application Gateway
* Brána Azure Firewall

Ve výchozím nastavení je předvolby směrování pro veřejnou IP adresu nastavená na globální síť Microsoft pro všechny služby Azure a může být přidružená k jakékoli službě Azure.

> [!IMPORTANT]
> Předvolby směrování jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pokud ještě nemáte předplatné Azure, vytvořte si teď [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento článek verzi modulu Azure PowerShell 6.9.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="register-the-feature-for-your-subscription"></a>Registrace funkce pro vaše předplatné
Funkce předvolby směrování je aktuálně ve verzi Preview. Zaregistrujte funkci pro vaše předplatné následujícím způsobem:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowRoutingPreferenceFeature -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Tento příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

```azurepowershell
$rg = New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-public-ip-with-internet-routing-preference"></a>Vytvoření veřejné IP adresy pomocí předvolby směrování v Internetu

Následující příkaz vytvoří novou veřejnou IP adresu s typem předvolby směrování jako *Internet* v oblasti *východní USA* Azure:

```azurepowershell
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

Výše vytvořenou veřejnou IP adresu můžete přidružit k virtuálnímu počítači s [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Použijte oddíl CLI na stránce kurzu: [přidružte veřejnou IP adresu k virtuálnímu počítači](associate-public-ip-address-vm.md#azure-cli) a PŘIDRUŽTE veřejnou IP adresu k vašemu virtuálnímu počítači. Můžete také přidružit veřejnou IP adresu vytvořenou výše s [Azure Load Balancer](../load-balancer/load-balancer-overview.md), a to tak, že ji přiřadíte ke konfiguraci **front-endu** nástroje pro vyrovnávání zatížení. Veřejná IP adresa slouží jako virtuální IP adresa (VIP) s vyrovnáváním zatížení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít příkaz [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [předvolbách směrování ve veřejných IP adresách](routing-preference-overview.md).
- [Nakonfigurujte předvolby směrování pro virtuální počítač pomocí Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md).
