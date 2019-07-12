---
title: Nasazení brány Firewall na Azure s víc veřejných IP adres pomocí Azure Powershellu
description: V tomto článku se dozvíte, jak nasadit bránu Firewall Azure s víc veřejných IP adres pomocí Azure Powershellu.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: ce47612f18ee64caa3a053001deb5448f7c27bfd
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703988"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Nasazení brány Firewall Azure s víc veřejných IP adres pomocí Azure Powershellu

> [!IMPORTANT]
> Brány Azure s víc veřejných IP adres je k dispozici prostřednictvím Azure Powershellu, rozhraní příkazového řádku Azure, rozhraní REST a šablony. Portálu uživatelského rozhraní je přidáte do oblastí postupně a budou k dispozici ve všech oblastech po dokončení tohoto uvedení.

Můžete nasadit bránu Firewall Azure s až 100 veřejné IP adresy.

Tato funkce umožňuje následující scénáře:

- **DNAT** -více instancí standardní port lze přeložit do back-end serverů. Například pokud máte dvě veřejné IP adresy, může překládat TCP port 3389 (RDP) pro obě IP adresy.
- **SNAT** – další porty jsou k dispozici pro odchozí připojení SNAT, snižuje riziko vyčerpání portů SNAT. Brána Firewall služby Azure v tuto chvíli náhodně vybere zdroj veřejnou IP adresu pro připojení. Pokud máte jakékoli podřízené filtrování ve vaší síti, budete muset povolit všechny veřejné IP adresy přidružené k vaší brány firewall.

Následující příklady Azure Powershellu ukazují, jak můžete konfigurovat, přidávat a odebírat veřejné IP adresy pro bránu Firewall Azure.

> [!NOTE]
> První konfigurace ipConfiguration nelze odebrat ze Brána Firewall služby Azure veřejné IP adresy konfigurace stránky. Pokud chcete upravit IP adresu, můžete pomocí Azure Powershellu.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Vytvoření brány firewall se dvěma nebo více veřejných IP adres

Tento příklad vytvoří bránu firewall, připojený k virtuální síti *vnet* s dvě veřejné IP adresy.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Přidejte veřejnou IP adresu pro existující bránu firewall

V tomto příkladu, veřejnou IP adresu *azFwPublicIp1* je připojen k bráně firewall.

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Odebrat veřejnou IP adresu z existující brány firewall

V tomto příkladu, veřejnou IP adresu *azFwPublicIp1* je odpojená od brány.

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Další postup

* [Kurz: Monitorujte protokoly brány Firewall na Azure](./tutorial-diagnostics.md)
