---
title: 'Vytvoření a nastavení vlastních zásad IPsec pro Point-to-site: PowerShell'
titleSuffix: Azure VPN Gateway
description: Tento článek vám pomůže vytvořit a nastavit vlastní zásady IPSec pro VPN Gateway konfigurace P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/09/2020
ms.author: cherylmc
ms.openlocfilehash: a77a870304e20c179e22b4a4ffe404315894d8a0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91743688"
---
# <a name="create-and-set-custom-ipsec-policies-for-point-to-site-preview"></a>Vytvoření a nastavení vlastních zásad IPsec pro Point-to-Site (Preview)

Pokud vaše prostředí vyžaduje vlastní zásadu IPsec pro šifrování, můžete snadno nakonfigurovat objekt zásad s požadovaným nastavením. Tento článek vám pomůže vytvořit vlastní objekt zásad a pak ho nastavit pomocí PowerShellu.

## <a name="before-you-begin"></a>Než začnete

### <a name="prerequisites"></a>Předpoklady

Ověřte, že vaše prostředí splňuje následující předpoklady:

* Máte funkční síť VPN typu Point-to-site, která je už nakonfigurovaná. Pokud to neuděláte, nakonfigurujte ho podle pokynů v článku **vytvoření sítě VPN typu Point-to-site**  pomocí [powershellu](vpn-gateway-howto-point-to-site-rm-ps.md)nebo [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="1-set-variables"></a><a name="signin"></a>1. nastavení proměnných

Deklarujte proměnné, které chcete použít. Použijte následující ukázku a v případě potřeby nahraďte vlastní hodnoty. Pokud během cvičení zavřete relaci PowerShell/Cloud Shell, stačí zkopírovat hodnoty a vložit je znovu a znovu deklarovat proměnné.

  ```azurepowershell-interactive
  $RG = "TestRG"
  $GWName = "VNet1GW"
  ```

## <a name="2-create-policy-object"></a><a name="create"></a>2. vytvoření objektu zásad

Vytvořte vlastní objekt zásad protokolu IPsec. Hodnoty můžete upravit tak, aby splňovaly kritéria, která požadujete.

```azurepowershell-interactive
$vpnclientipsecpolicy = New-AzVpnClientIpsecPolicy -IpsecEncryption AES256 -IpsecIntegrity SHA256 -SALifeTime 86471 -SADataSize 429496 -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup2 -PfsGroup PFS2
```

## <a name="3-update-gateway-and-set-policy"></a><a name="update"></a>3. aktualizace brány a nastavení zásad

V tomto kroku aktualizujte stávající bránu VPN P2S a nastavte zásadu IPsec.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway -VpnClientIpsecPolicy $vpnclientipsecpolicy
```

## <a name="next-steps"></a>Další kroky

Další informace o konfiguracích P2S najdete v tématu [o síti VPN typu Point-to-site](point-to-site-about.md).
