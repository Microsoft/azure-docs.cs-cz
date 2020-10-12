---
title: 'Řešení potíží s připojením VPN typu Site-to-site, která nemůže připojit titleSuffix: Azure VPN Gateway'
description: Naučte se řešit potíže s připojením VPN typu Site-to-site, které náhle přestane fungovat a nelze je znovu připojit.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 09056846ee3e531724f597ee35f92d812ce2c335
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86037830"
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Řešení potíží: připojení VPN typu Site-to-Site VPN se nemůže připojit a přestane fungovat.

Po nakonfigurování připojení VPN typu Site-to-site mezi místní sítí a virtuální sítí Azure se připojení VPN náhle zastaví a nebude možné ho znovu připojit. Tento článek popisuje kroky pro řešení potíží, které vám pomůžou tento problém vyřešit. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Postup při řešení potíží

Problém vyřešíte tak, že se nejdřív pokusíte [resetovat bránu Azure VPN Gateway](vpn-gateway-resetgw-classic.md) a resetovat tunel z místního zařízení VPN. Pokud se problém opakuje, postupujte podle těchto kroků a Identifikujte příčinu problému.

### <a name="prerequisite-step"></a>Krok požadovaných součástí

Ověřte typ brány VPN Azure.

1. Přejděte na web [Azure Portal](https://portal.azure.com).

2. Informace o typu najdete na stránce s **přehledem** služby VPN Gateway.
    
    ![Přehled brány](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Krok 1. Zkontrolovat, jestli je místní zařízení VPN ověřené

1. Ověřte, zda používáte [ověřené zařízení VPN a verzi operačního systému](vpn-gateway-about-vpn-devices.md#devicetable). Pokud se nejedná o ověřené zařízení VPN, možná budete muset kontaktovat výrobce zařízení a zjistit, jestli došlo k potížím s kompatibilitou.

2. Ujistěte se, že je zařízení VPN správně nakonfigurované. Další informace najdete v tématu [Úprava ukázek konfigurace zařízení](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>Krok 2. Ověření sdíleného klíče

Porovnejte sdílený klíč pro místní zařízení VPN s Azure Virtual Network VPN, abyste se ujistili, že se klíče shodují. 

Pokud chcete zobrazit sdílený klíč pro připojení Azure VPN, použijte jednu z následujících metod:

**Azure Portal**

1. Přejít na připojení typu Site-to-site brány VPN, které jste vytvořili.

2. V části **Nastavení** klikněte na možnost **sdílený klíč**.
    
    ![Sdílený klíč](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pro model nasazení Azure Resource Manager:

```azurepowershell
Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>
```

Pro model nasazení Classic:

```azurepowershell
Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName
```

### <a name="step-3-verify-the-vpn-peer-ips"></a>Krok 3. Ověření IP adresy partnerského uzlu sítě VPN

-   Definice IP adresy v objektu **brány místní sítě** v Azure by měla odpovídat místní IP adrese zařízení.
-   Definice IP adresy brány Azure nastavená na místním zařízení by se měla shodovat s IP adresou brány Azure.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>Krok 4: Kontrolovat UDR a skupin zabezpečení sítě v podsíti brány

Zkontrolujte a odeberte uživatelsky definované směrování (UDR) nebo skupiny zabezpečení sítě (skupin zabezpečení sítě) v podsíti brány a pak otestujte výsledek. Pokud je problém vyřešen, ověřte nastavení, které UDR nebo NSG použili.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>Krok 5. Ověřte adresu externího rozhraní zařízení místní sítě VPN.

- Pokud je internetová IP adresa zařízení VPN zahrnutá v definici **místní sítě** v Azure, může docházet ke občasnému odpojení.
- Externí rozhraní zařízení musí být přímo na internetu. Mezi Internetem a zařízením by neměl být žádný překlad síťových adres ani brána firewall.
- Chcete-li nakonfigurovat clustery brány firewall tak, aby měly virtuální IP adresu, je nutné cluster přerušit a vystavit zařízení VPN přímo veřejnému rozhraní, se kterým může Brána používat rozhraní.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>Krok 6. Ověření, že se podsítě přesně shodují (brány založené na zásadách Azure)

-   Ověřte, jestli se adresní prostory virtuální sítě přesně shodují mezi virtuální sítí Azure a místními definicemi.
-   Ověřte, že se podsítě přesně shodují mezi **bránou místní sítě a místními** definicemi místní sítě.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>Krok 7. Ověření sondy stavu služby Azure Gateway

1. Otevřete test stavu tak, že přejdete na následující adresu URL:

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. Klikněte na upozornění certifikátu.
3. Pokud obdržíte odpověď, brána sítě VPN se považuje za v dobrém stavu. Pokud neobdržíte odpověď, brána nemusí být v pořádku nebo NSG v podsíti brány způsobuje problém. Následující text je ukázková odpověď:

    ```xml
    <?xml version="1.0"?>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string>
    ```

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>Krok 8. Ověřte, jestli má místní zařízení VPN povolenou funkci Perfect Forward Secrecy.

Funkce Perfect Forward Secrecy může způsobit problémy s odpojením. Pokud má zařízení VPN povolené PFS (Perfect Forward Secrecy), zakažte tuto funkci. Pak aktualizujte zásady protokolu IPsec brány VPN.

## <a name="next-steps"></a>Další kroky

-   [Konfigurace připojení typu Site-to-site k virtuální síti](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Konfigurace zásad IPsec/IKE pro připojení VPN typu Site-to-site](vpn-gateway-ipsecikepolicy-rm-powershell.md)
