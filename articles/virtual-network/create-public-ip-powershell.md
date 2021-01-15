---
title: Vytvoření veřejné IP adresy – Azure PowerShell
description: Naučte se vytvářet veřejné IP adresy pomocí Azure PowerShell
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 5b79a777ba8f7e615e4637f94311cba39e8a7f6c
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223699"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-powershell"></a>Rychlý Start: vytvoření veřejné IP adresy pomocí Azure PowerShell

V tomto článku se dozvíte, jak vytvořit prostředek veřejné IP adresy pomocí Azure PowerShell. Další informace o tom, k jakým prostředkům může být přidružen, rozdíl mezi základní a standardní SKU a dalšími souvisejícími informacemi, najdete v tématu [veřejné IP adresy](./public-ip-addresses.md).  V tomto příkladu se zaměříme jenom na adresy IPv4. Další informace o adresách IPv6 najdete v tématu [IPv6 pro virtuální síť Azure](./ipv6-overview.md).

## <a name="prerequisites"></a>Požadavky

- Azure PowerShell lokálně nainstalované nebo Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.4.1 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Pokud používáte prostředí PowerShell místně, je také potřeba spustit příkaz `Connect-AzAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) s názvem **myResourceGroup** v umístění **eastus2** .

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'

New-AzResourceGroup -Name $rg -Location $loc
```
## <a name="create-public-ip"></a>Vytvoření veřejné IP adresy

---
# <a name="standard-sku---using-zones"></a>[**Standardní SKU – používání zón**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>Následující příkaz funguje pro rozhraní API verze 2020-08-01 nebo novější.  Další informace o aktuálně používané verzi rozhraní API najdete v tématu [poskytovatelé a typy prostředků](../azure-resource-manager/management/resource-providers-and-types.md).

Pomocí [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) vytvořte standardní veřejnou IP adresu v zóně s názvem **myStandardZRPublicIP** v **myResourceGroup**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZRPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 1,2,3

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```
> [!IMPORTANT]
> U verzí rozhraní API starších než 2020-08-01 spusťte výše uvedený příkaz bez zadání parametru zóny pro vytvoření redundantní IP adresy zóny. 
>

Pokud chcete vytvořit veřejnou IP adresu Standard Zona v Zóna 2 s názvem **myStandardZonalPublicIP** v **myResourceGroup**, použijte následující příkaz:

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZonalPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 2

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```

Všimněte si, že výše uvedené možnosti pro zóny jsou pouze výběr platný v oblastech s [zóny dostupnosti](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="standard-sku---no-zones"></a>[**Standardní SKU – žádné zóny**](#tab/option-create-public-ip-standard)

>[!NOTE]
>Následující příkaz funguje pro rozhraní API verze 2020-08-01 nebo novější.  Další informace o aktuálně používané verzi rozhraní API najdete v tématu [poskytovatelé a typy prostředků](../azure-resource-manager/management/resource-providers-and-types.md).

Použijte [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) k vytvoření standardní veřejné IP adresy jako prostředku mimo oblast s názvem **myStandardPublicIP** v **myResourceGroup**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardPublicIP'
$sku = 'Standard'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```

Tento výběr je platný ve všech oblastech a je výchozí volbou pro standardní veřejné IP adresy v oblastech bez [zóny dostupnosti](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="basic-sku"></a>[**Základní SKU**](#tab/option-create-public-ip-basic)

Použijte [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) k vytvoření základní statické veřejné IP adresy s názvem **myBasicPublicIP** v **myResourceGroup**.  Základní veřejné IP adresy nemají koncept zón dostupnosti.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myBasicPublicIP'
$sku = 'Basic'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```
Pokud je přijatelné, aby se IP adresa měnila v čase, je možné vybrat **dynamické** přiřazení IP adresy změnou element allocationmethod na Dynamic.

---

## <a name="additional-information"></a>Další informace 

Další podrobnosti o jednotlivých proměnných uvedených výše najdete v tématu [Správa veřejných IP adres](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Další kroky
- Přidružení [veřejné IP adresy k virtuálnímu počítači](./associate-public-ip-address-vm.md#azure-portal)
- Přečtěte si další informace o [veřejných IP adresách](./public-ip-addresses.md#public-ip-addresses) v Azure.
- Přečtěte si další informace o všech [nastaveních veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address).