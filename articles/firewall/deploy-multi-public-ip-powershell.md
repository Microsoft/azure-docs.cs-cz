---
title: Nasazení Azure Firewall s více veřejnými IP adresami pomocí prostředí PowerShell
description: V tomto článku se dozvíte, jak nasadit Azure Firewall s více veřejnými IP adresami pomocí Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: cbad025a0d0c4d679ea9cdc7557c81b5145798fd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023665"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Nasazení služby Azure Firewall s několika veřejnými IP adresami pomocí Azure PowerShellu

Tato funkce umožňuje následující scénáře:

- **DNAT** – můžete přeložit několik standardních instancí portů na servery back-end. Například pokud máte dvě veřejné IP adresy, můžete pro obě IP adresy překládat port TCP 3389 (RDP).
- **SNAT** – pro odchozí připojení SNAT jsou k dispozici další porty, což snižuje potenciál vyčerpání portů SNAT. V tuto chvíli Azure Firewall náhodně vybere zdrojovou veřejnou IP adresu, která se má použít pro připojení. Pokud ve své síti využíváte následné filtrování, musíte povolit všechny veřejné IP adresy přidružené k vaší bráně firewall. Pro zjednodušení této konfigurace zvažte použití [předpony veřejných IP adres](../virtual-network/public-ip-address-prefix.md) .
 
Azure Firewall s více veřejnými IP adresami jsou k dispozici prostřednictvím Azure Portal, Azure PowerShell, Azure CLI, REST a šablon. Azure Firewall můžete nasadit s až 250 veřejnými IP adresami.

Následující příklady Azure PowerShell ukazují, jak můžete nakonfigurovat, přidat a odebrat veřejné IP adresy pro Azure Firewall.

> [!NOTE]
> První ipConfiguration nelze odebrat ze stránky Azure Firewall konfigurace veřejné IP adresy. Pokud chcete změnit IP adresu, můžete použít Azure PowerShell.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Vytvoření brány firewall se dvěma nebo více veřejnými IP adresami

Tento příklad vytvoří bránu firewall připojenou k virtuální síti *VNet* se dvěma veřejnými IP adresami.

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

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Přidání veřejné IP adresy do existující brány firewall

V tomto příkladu je veřejná IP adresa *azFwPublicIp1* připojená k bráně firewall.

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

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Odebrání veřejné IP adresy z existující brány firewall

V tomto příkladu je veřejná IP adresa *azFwPublicIp1* odpojená od brány firewall.

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

## <a name="next-steps"></a>Další kroky

* [Rychlý Start: vytvoření Azure Firewall s více veřejnými IP adresami – šablona Správce prostředků](quick-create-multiple-ip-template.md)
