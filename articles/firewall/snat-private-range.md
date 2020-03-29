---
title: Rozsahy privátních IP adres Azure Firewall SNAT
description: Soukromé rozsahy IP adres můžete nakonfigurovat tak, aby brána firewall na tyto IP adresy nespořila provoz.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/20/2020
ms.author: victorh
ms.openlocfilehash: ed8cef00b7de67458c607373c724a3717f14a7cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064811"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Rozsahy privátních IP adres Azure Firewall SNAT

Azure Firewall není SNAT s pravidly sítě, pokud je cílová IP adresa v rozsahu privátní IP adresy podle [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Pravidla aplikace jsou vždy použita pomocí [transparentního proxy serveru](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) bez ohledu na cílovou adresu IP.

Pokud vaše organizace používá rozsah veřejných IP adres pro privátní sítě, Azure Firewall SNAT steska provozu na jednu z privátních IP adres brány firewall v AzureFirewallSubnet. Azure Firewall však můžete **not** nakonfigurovat tak, aby nespořila rozsah veřejných IP adres.

## <a name="configure-snat-private-ip-address-ranges"></a>Konfigurace rozsahů privátních IP adres SNAT

Azure PowerShell můžete použít k určení rozsahu IP adres, který brána firewall nebude SNAT.

### <a name="new-firewall"></a>Nová brána firewall

Pro novou bránu firewall je příkaz Azure PowerShell:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges se rozbalí na aktuální výchozí hodnoty na Azure Firewall, zatímco ostatní rozsahy jsou přidány do něj.

Další informace naleznete v [tématu New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Existující brána firewall

Chcete-li nakonfigurovat existující bránu firewall, použijte následující příkazy Prostředí Azure PowerShell:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Šablony

Do `additionalProperties` oddílu můžete přidat následující:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nasadit a nakonfigurovat bránu Azure Firewall](tutorial-firewall-deploy-portal.md).