---
title: Upgrade veřejných IP adres
titleSuffix: Azure Virtual Network
description: Upgradujte veřejné IP adresy z úrovně Basic na standard.
services: virtual-network
documentationcenter: na
author: blehr
editor: ''
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2020
ms.author: blehr
ms.custom: references_regions
ms.openlocfilehash: 33c767d847d9e70e95b3ee1648be7852aa5cec98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100522882"
---
# <a name="upgrade-public-ip-addresses"></a>Upgrade veřejných IP adres

Veřejné IP adresy Azure se vytvářejí pomocí SKU – buď Basic, nebo Standard, což určuje aspekty jejich funkcí (včetně metody přidělování, podpory funkcí a prostředků, ke kterým se dají přidružit). 

V tomto článku jsou revidovány následující scénáře:
* Postup upgradu veřejné IP adresy základní SKU na veřejnou IP adresu Standard SKU (pomocí PowerShellu nebo rozhraní příkazového řádku)
* Postup migrace klasického Vyhrazená IP adresa Azure do veřejné IP adresy Azure Resource Manager Basic SKU

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>Upgrade veřejné IP adresy z úrovně Basic na standard SKU

Aby bylo možné upgradovat veřejnou IP adresu, nesmí být přidružená k žádnému prostředku (Další informace o zrušení přidružení veřejných IP adres) najdete na [této stránce](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) .

>[!IMPORTANT]
>Veřejné IP adresy upgradované z úrovně Basic na standard SKU budou mít i nadále žádné garantované [zóny dostupnosti](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).  Ujistěte se prosím, že při výběru prostředků, ke kterým se má přidružit IP adresa, je potřeba mít na paměti.

---
# <a name="basic-to-standard---powershell"></a>[**Základní až standard – PowerShell**](#tab/option-upgrade-powershell)

Následující příklad předpokládá předchozí vytvoření veřejné IP adresy základní SKU pomocí příkladu uvedeného na [této stránce](./create-public-ip-powershell.md?tabs=option-create-public-ip-basic) se základní veřejnou IP adresou **myBasicPublicIP** v **myResourceGroup**.

Aby bylo možné upgradovat IP adresu, jednoduše spusťte níže uvedené příkazy pomocí PowerShellu.  Poznámka: Pokud je IP adresa už staticky přidělená, může se tato část přeskočit.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'
$newsku = 'Standard'
$pubIP = Get-AzPublicIpAddress -name $name -ResourceGroupName $rg

## This section is only needed if the Basic IP is not already set to Static ##
$pubIP.PublicIpAllocationMethod = 'Static'
Set-AzPublicIpAddress -PublicIpAddress $pubIP

## This section is for conversion to Standard ##
$pubIP.Sku.Name = $newsku
Set-AzPublicIpAddress -PublicIpAddress $pubIP
```

# <a name="basic-to-standard---cli"></a>[**Základní na standard – CLI**](#tab/option-upgrade-cli)

Následující příklad předpokládá předchozí vytvoření veřejné IP adresy základní SKU pomocí příkladu uvedeného na [této stránce](./create-public-ip-cli.md?tabs=option-create-public-ip-basic) se základní veřejnou IP adresou **myBasicPublicIP** v **myResourceGroup**.

Aby bylo možné upgradovat IP adresu, jednoduše spusťte níže uvedené příkazy pomocí Azure CLI.  Poznámka: Pokud je IP adresa už staticky přidělená, může se tato část přeskočit.

```azurecli-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'

## This section is only needed if the Basic IP is not already set to Static ##
az network public-ip update \
--resource-group $rg \
--name $name \
--allocation-method Static 

## This section is for conversion to Standard ##
az network public-ip update \
--resource-group $rg \
--name $name \
--sku Standard
```
---

## <a name="upgrade-migrate-a-classic-reserved-ip-to-a-static-public-ip"></a>Upgrade (migrace) klasického Vyhrazená IP adresa na statickou veřejnou IP adresu

Pokud chcete využívat nové funkce v Azure Resource Manager, můžete migrovat stávající veřejnou statickou IP adresu, která se nazývá rezervované IP adresy – od modelu Classic až po moderní model Azure Resource Manager.  Migrovaná veřejná IP adresa bude základní typ SKU.


---

# <a name="reserved-to-basic---powershell"></a>[**Vyhrazeno pro základní – PowerShell**](#tab/option-migrate-powershell)

Následující příklad předpokládá předchozí vytvoření klasického **MyReservedIP** Azure vyhrazená IP adresa v **myResourceGroup**. Další podmínkou pro migraci je zajistit, aby bylo předplatné služby Azure Resource Manager zaregistrované pro migraci. Tento postup je popsaný v podrobnostech o krocích 3 a 4 [této stránky](../virtual-machines/migration-classic-resource-manager-ps.md).

Aby bylo možné migrovat Vyhrazená IP adresa, spusťte následující příkazy pomocí prostředí PowerShell.  Poznámka: Pokud IP adresa není přidružená k žádné službě (pod ní je služba s názvem **mojesluzba**), tento krok se dá přeskočit.

```azurepowershell-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$serviceName = 'myService'
Remove-AzureReservedIPAssociation -ReservedIPName $name -ServiceName $service

$validate = Move-AzureReservedIP -ReservedIPName $name -Validate
$validate.ValidationMessages
```
Předchozí příkaz zobrazí všechna upozornění a chyby, které blokují migraci. Pokud je ověření úspěšné, můžete pokračovat podle následujících kroků a připravit a potvrdit migraci:
```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName $name -Prepare
Move-AzureReservedIP -ReservedIPName $name -Commit
```
V Azure Resource Manager se vytvoří nová skupina prostředků s použitím názvu migrovaného Vyhrazená IP adresa (v předchozím příkladu se jedná o skupinu prostředků **myReservedIP-migrováno**).

# <a name="reserved-to-basic---cli"></a>[**Vyhrazeno pro základní-CLI**](#tab/option-migrate-cli)

Následující příklad předpokládá předchozí vytvoření klasického **MyReservedIP** Azure vyhrazená IP adresa v **myResourceGroup**. Další podmínkou pro migraci je zajistit, aby bylo předplatné služby Azure Resource Manager zaregistrované pro migraci. Tento postup je popsaný v podrobnostech o krocích 3 a 4 [této stránky](../virtual-machines/migration-classic-resource-manager-cli.md).

Aby bylo možné migrovat Vyhrazená IP adresa, spusťte níže uvedené příkazy pomocí Azure CLI.  Poznámka: Pokud IP adresa není přidružená k žádné službě (pod ní je služba s názvem **mojesluzba** and Deployment **myDeployment**), tento krok se dá přeskočit.

```azurecli-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$service = 'myService'
$deployment = 'myDeployment'
azure network reserved-ip disassociate $name $service $deployment

azure network reserved-ip validate-migration $name
```
Předchozí příkaz zobrazí všechna upozornění a chyby, které blokují migraci. Pokud je ověření úspěšné, můžete pokračovat podle následujících kroků a připravit a potvrdit migraci:
```azurecli-interactive
azure network reserved-ip prepare-migration $name
azure network reserved-ip commit-migration $name
```
V Azure Resource Manager se vytvoří nová skupina prostředků s použitím názvu migrovaného Vyhrazená IP adresa (v předchozím příkladu se jedná o skupinu prostředků **myReservedIP-migrováno**).

---

## <a name="limitations"></a>Omezení

* Aby bylo možné upgradovat základní veřejnou IP adresu, nelze ji přidružit k žádnému prostředku Azure.  Přečtěte si prosím [tuto stránku](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) , kde najdete další informace, jak zrušit přidružení veřejných IP adres.  Podobně, aby bylo možné migrovat Vyhrazená IP adresa, nelze je přidružit k žádné cloudové službě.  Další informace o tom, jak zrušit přidružení rezervovaných IP adres, najdete na [této stránce](./remove-public-ip-address-vm.md) .  
* Veřejné IP adresy upgradované z úrovně Basic na standard SKU budou mít i nadále žádné [zóny dostupnosti](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) , a proto se nedají přidružit k prostředku Azure, který je buď redundantní v zóně nebo v oblasti.  Všimněte si, že to platí jenom pro oblasti, které nabízí zóny dostupnosti.
* Nemůžete downgradovat z úrovně Standard na Basic.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [veřejných IP adresách](./public-ip-addresses.md#public-ip-addresses) v Azure, včetně rozdílu mezi typy SKU a [nastavení veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Přečtěte si, jak [upgradovat veřejné nástroje pro vyrovnávání zatížení Azure z úrovně Basic na standard](../load-balancer/upgrade-basic-standard.md).
- Pochopte [rezervované IP adresy Azure Classic](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) a [migraci klasických prostředků na Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md).
