---
title: Rozsahy privátních IP adres Azure Firewall SNAT
description: Můžete nakonfigurovat privátní rozsahy IP adres, aby brána firewall nenarušila provoz na tyto IP adresy.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/20/2020
ms.author: victorh
ms.openlocfilehash: ed8cef00b7de67458c607373c724a3717f14a7cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80064811"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Rozsahy privátních IP adres Azure Firewall SNAT

Pokud je cílová IP adresa v rozsahu privátních IP adres na [IANA RFC 1918](https://tools.ietf.org/html/rfc1918), Azure firewall nesnat s pravidly sítě. Pravidla aplikací se vždycky aplikují pomocí [transparentního proxy serveru](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) bez ohledu na cílovou IP adresu.

Pokud vaše organizace používá pro privátní sítě rozsah veřejných IP adres, Azure Firewall SNATs provoz na jednu z privátních IP adres brány firewall v AzureFirewallSubnet. Můžete ale nakonfigurovat Azure Firewall **, aby** nesnat na svůj rozsah veřejných IP adres.

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
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Šablony

Do `additionalProperties` části můžete přidat následující:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nasadit a nakonfigurovat Azure firewall](tutorial-firewall-deploy-portal.md).