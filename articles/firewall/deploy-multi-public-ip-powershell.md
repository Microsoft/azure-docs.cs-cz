---
title: Nasazení brány Firewall na Azure s víc veřejných IP adres pomocí Azure Powershellu
description: V tomto článku se dozvíte, jak nasadit bránu Firewall Azure s víc veřejných IP adres pomocí Azure Powershellu.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/21/2019
ms.author: victorh
ms.openlocfilehash: 9ec37197376c815c4fb9072164520a707b02be2b
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312728"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Nasazení brány Firewall Azure s víc veřejných IP adres pomocí Azure Powershellu

> [!IMPORTANT]
> Brány Azure s víc veřejných IP adres je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Můžete nasadit bránu Firewall Azure s až 600 veřejné IP adresy.

Tato funkce umožňuje následující scénáře:

- **DNAT** -více instancí standardní port lze přeložit do back-end serverů. Například pokud máte dvě veřejné IP adresy, může překládat TCP port 3389 (RDP) pro obě IP adresy.
- **SNAT** – další porty jsou k dispozici pro odchozí připojení SNAT, snižuje riziko vyčerpání portů SNAT. Brána Firewall služby Azure v tuto chvíli náhodně vybere zdroj veřejnou IP adresu pro připojení. Pokud máte jakékoli podřízené filtrování ve vaší síti, budete muset povolit všechny veřejné IP adresy přidružené k vaší brány firewall.

Následující příklady Azure Powershellu ukazují, jak můžete přidávat, odebírat a konfigurovat veřejné IP adresy pro bránu Firewall Azure.

> [!NOTE]
> Ve verzi public preview je-li přidat nebo odebrat veřejnou IP adresu do spuštěného brány firewall, nemusí existující příchozí připojení pomocí pravidel pro DNAT fungovat 40 – 120 sekund. Nelze odebrat první veřejná IP adresa přiřazená bránu firewall, pokud je brána firewall bylo zrušeno přiřazení nebo odstranit.

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Přidejte veřejnou IP adresu pro existující bránu firewall

V tomto příkladu, veřejnou IP adresu *azFwPublicIp1* jako připojené do brány firewall.

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

V tomto příkladu, veřejnou IP adresu *azFwPublicIp1* jako odpojené od brány.

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

## <a name="next-steps"></a>Další postup

* [Kurz: Monitorujte protokoly brány Firewall na Azure](./tutorial-diagnostics.md)
