---
title: Nasazení brány Azure Firewall s více veřejnými IP adresami pomocí PowerShellu
description: V tomto článku se dozvíte, jak nasadit bránu Azure Firewall s více veřejnými IP adresami pomocí Azure PowerShellu.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: 21f645e64c9944ed102f538710ea6facc26c7e83
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314035"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Nasazení služby Azure Firewall s několika veřejnými IP adresami pomocí Azure PowerShellu

Tato funkce umožňuje následující scénáře:

- **DNAT** - Můžete přeložit více standardních instancí portů na back-endové servery. Máte-li například dvě veřejné adresy IP, můžete přeložit port TCP 3389 (RDP) pro obě adresy IP.
- **SNAT** - Další porty jsou k dispozici pro odchozí připojení SNAT, což snižuje potenciál vyčerpání portu SNAT. V tuto chvíli Azure Firewall náhodně vybere zdrojovou veřejnou IP adresu, která se má použít pro připojení. Pokud máte v síti nějaké následné filtrování, je třeba povolit všechny veřejné IP adresy přidružené k bráně firewall.
 
Azure Firewall s více veřejnými IP adresami je dostupný přes Azure Portal, Azure PowerShell, Azure CLI, REST a šablony. Azure Firewall můžete nasadit až se 100 veřejnými IP adresami.

Následující příklady Azure PowerShellu ukazují, jak můžete nakonfigurovat, přidat a odebrat veřejné IP adresy pro Azure Firewall.

> [!NOTE]
> První konfiguraci ipconfiguration nelze odebrat ze stránky konfigurace veřejných IP adres azure firewall. Pokud chcete upravit IP adresu, můžete použít Azure PowerShell.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Vytvoření brány firewall se dvěma nebo více veřejnými IP adresami

Tento příklad vytvoří bránu firewall připojenou k *virtuální síti* virtuální sítě se dvěma veřejnými IP adresami.

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

V tomto příkladu je k bráně firewall připojena veřejná IP adresa *azFwPublicIp1.*

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

V tomto příkladu je veřejná IP adresa *azFwPublicIp1* odpojena od brány firewall.

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

* [Úvodní příručka: Vytvoření brány Azure Firewall s více veřejnými IP adresami – šablona Správce prostředků](quick-create-multiple-ip-template.md)
