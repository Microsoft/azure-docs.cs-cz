---
title: 'Jak nakonfigurovat OpenVPN ve službě Azure VPN Gateway: Prostředí PowerShell | Dokumentace Microsoftu'
description: Postup konfigurace OpenVPN pro Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: cherylmc
ms.openlocfilehash: 1dad960b0877cddf3be9afc01e3e687ebe4702c0
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54357823"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>Konfigurace OpenVPN pro point-to-site Azure VPN Gateway (Preview)

Tento článek vám pomůže nastavit OpenVPN ve službě Azure VPN Gateway. Tento článek předpokládá, že jste již pracovní prostředí point-to-site. Pokud ho nevidíte, postupujte podle pokynů v kroku 1 k vytvoření sítě VPN point-to-site.

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register"></a>Registrace této funkce

Klikněte na tlačítko **TryIt** v následujícím postupu registrace tuto funkci snadno pomocí Azure Cloud Shell.

>[!NOTE]
>Pokud si tuto funkci nezaregistrujete, nebudete moct používat.
>

Po kliknutí na **Vyzkoušet** se otevře Azure Cloud Shell. Zkopírujte a vložte následující příkazy:

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```
 
```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

Jakmile bude tato funkce zaregistrovaná, znovu zaregistrujte předplatné do oboru názvů Microsoft.Network.

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1. Vytvoření sítě VPN point-to-site

Pokud ještě nemáte funkční prostředí point-to-site, postupujte podle pokynů a vytvořte si ho. Zobrazit [vytvořit síť point-to-site VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) vytváření a konfiguraci brány VPN typu point-to-site s ověřováním pomocí nativního certifikátu Azure. Všimněte si, že základní SKU není podporována pro IKEv2 point-to-site.

## <a name="cmdlets"></a>2. Instalace rutin PowerShellu

Nainstalujte nejnovější verzi rutin PowerShellu pro Resource Manager. Další informace o instalaci rutin prostředí PowerShell najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). To je důležité, protože starší verze rutin neobsahují aktuální hodnoty, které potřebujete pro toto cvičení.

## <a name="enable"></a>3. Povolit OpenVPN na bráně

Povolte OpenVPN na bráně. Ujistěte se, že brána je už nakonfigurovaná pro point-to-site (protokol IKEv2 nebo SSTP) před spuštěním následujících příkazů:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Další postup

Konfigurování klientů pro OpenVPN, naleznete v tématu [klienty nakonfigurovat OpenVPN](vpn-gateway-howto-openvpn-clients.md).
