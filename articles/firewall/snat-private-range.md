---
title: Rozsahy privátních IP adres Azure Firewall SNAT
description: Můžete nakonfigurovat privátní rozsahy IP adres, aby brána firewall nenarušila provoz na tyto IP adresy.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/09/2020
ms.author: victorh
ms.openlocfilehash: b190d07ceadea43ca572f5eb5be3eeeafa616971
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444456"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Rozsahy privátních IP adres Azure Firewall SNAT

Azure Firewall nesnat, pokud je cílová IP adresa v rozsahu privátních IP adres na [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Pokud vaše organizace používá pro privátní sítě rozsah veřejných IP adres, Azure Firewall na jednu z privátních IP adres firewallu v AzureFirewallSubnet. Můžete ale nakonfigurovat Azure Firewall **, aby** nesnat na svůj rozsah veřejných IP adres.

## <a name="configure-snat-private-ip-address-ranges"></a>Konfigurovat rozsahy privátních IP adres SNAT

Pomocí Azure PowerShell můžete zadat rozsah IP adres, které brána firewall nesnat.

### <a name="new-firewall"></a>Nová brána firewall

Pro novou bránu firewall Azure PowerShell příkaz:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges se rozšíří na aktuální výchozí hodnoty na Azure Firewall, zatímco se do nich přidají jiné rozsahy.

Další informace najdete v tématu [New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Existující brána firewall

Ke konfiguraci existující brány firewall použijte následující příkazy Azure PowerShell:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @(“IANAPrivateRanges”,“IPRange1”, “IPRange2”)
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Šablony

Do části `additionalProperties` můžete přidat následující:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nasadit a nakonfigurovat Azure firewall](tutorial-firewall-deploy-portal.md).