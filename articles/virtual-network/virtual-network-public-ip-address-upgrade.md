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
ms.date: 10/07/2020
ms.author: blehr
ms.custom: references_regions , devx-track-azurecli
ms.openlocfilehash: 0c248149694c2bf66b8c94e9c0a29a8f7da9f4e4
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843736"
---
# <a name="upgrade-public-ip-addresses"></a>Upgrade veřejných IP adres

Veřejné IP adresy Azure se vytvářejí pomocí SKU – Basic nebo Standard – což určuje aspekty jejich funkcí (včetně metody přidělování, využití napříč zónami dostupnosti a k jakým prostředkům, se kterými se dají přidružit). 

V tomto článku jsou revidovány následující scénáře:
* Postup upgradu veřejné IP adresy základní SKU na veřejnou IP adresu Standard SKU (pomocí portálu, PowerShellu nebo rozhraní příkazového řádku)
* Postup migrace klasického Vyhrazená IP adresa Azure do veřejné IP adresy Azure Resource Manager Basic SKU

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>Upgrade veřejné IP adresy z úrovně Basic na standard SKU

>[!NOTE]
>Možnost upgradovat veřejné IP adresy z úrovně Basic na standard není dostupná ve všech oblastech.  Další podrobnosti najdete v tématu [**omezení**](#limitations) .

Aby bylo možné upgradovat veřejnou IP adresu, nesmí být přidružená k žádnému prostředku (Další informace o zrušení přidružení veřejných IP adres) najdete na [této stránce](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#view-modify-settings-for-or-delete-a-public-ip-address) .

>[!IMPORTANT]
>Veřejné IP adresy upgradované z úrovně Basic na standard SKU nadále nemají žádné [zóny dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).  To znamená, že není možné je přidružit k prostředku Azure, který je buď redundantní v zóně, nebo vázaný na předem stanovenou zónu v oblastech, kde je tato možnost nabízena.

---
# <a name="basic-to-standard---powershell"></a>[**Základní až standard – PowerShell**](#tab/option-upgrade-powershell)

Následující příklad předpokládá předchozí vytvoření veřejné IP adresy základní SKU pomocí příkladu uvedeného na [této stránce](https://docs.microsoft.com/azure/virtual-network/create-public-ip-powershell?tabs=option-create-public-ip-basic) se základní veřejnou IP adresou **myBasicPublicIP** v **myResourceGroup**.

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

Následující příklad předpokládá předchozí vytvoření veřejné IP adresy základní SKU pomocí příkladu uvedeného na [této stránce](https://docs.microsoft.com/azure/virtual-network/create-public-ip-cli?tabs=option-create-public-ip-basic) se základní veřejnou IP adresou **myBasicPublicIP** v **myResourceGroup**.

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

Následující příklad předpokládá předchozí vytvoření klasického **MyReservedIP** Azure vyhrazená IP adresa v **myResourceGroup**. Další podmínkou pro migraci je zajistit, aby bylo předplatné služby Azure Resource Manager zaregistrované pro migraci. Tento postup je popsaný v podrobnostech o krocích 3 a 4 [této stránky](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-ps).

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

Následující příklad předpokládá předchozí vytvoření klasického **MyReservedIP** Azure vyhrazená IP adresa v **myResourceGroup**. Další podmínkou pro migraci je zajistit, aby bylo předplatné služby Azure Resource Manager zaregistrované pro migraci. Tento postup je popsaný v podrobnostech o krocích 3 a 4 [této stránky](https://docs.microsoft.com/azure/virtual-machines/linux/migration-classic-resource-manager-cli).

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

* Tato funkce není aktuálně k dispozici v následujících oblastech:<br>
USA (Gov) – Virginia<br>
US DoD – východ<br>
US DoD – střed<br>
Čína – východ<br>
Čína – východ 2<br>
Čína – sever<br>
Čína – sever 2

* Aby bylo možné upgradovat základní veřejnou IP adresu, nelze ji přidružit k žádnému prostředku Azure.  Přečtěte si prosím [tuto stránku](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#view-modify-settings-for-or-delete-a-public-ip-address) , kde najdete další informace, jak zrušit přidružení veřejných IP adres.  Podobně, aby bylo možné migrovat Vyhrazená IP adresa, nelze je přidružit k žádné cloudové službě.  Další informace o tom, jak zrušit přidružení rezervovaných IP adres, najdete na [této stránce](https://docs.microsoft.com/azure/virtual-network/remove-public-ip-address-vm) .  
* Veřejné IP adresy upgradované z úrovně Basic na standard SKU budou mít i nadále žádné [zóny dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones) , a proto se nedají přidružit k prostředku Azure, který je buď redundantní v zóně nebo v oblasti.  Všimněte si, že to platí jenom pro oblasti, které nabízí zóny dostupnosti.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [veřejných IP adresách](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) v Azure, včetně rozdílu mezi typy SKU a [nastavení veřejné IP adresy](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Přečtěte si, jak [upgradovat veřejné nástroje pro vyrovnávání zatížení Azure z úrovně Basic na standard](https://docs.microsoft.com/azure/load-balancer/upgrade-basic-standard).
- Pochopte [rezervované IP adresy Azure Classic](https://docs.microsoft.com/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) a [migraci klasických prostředků na Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).
