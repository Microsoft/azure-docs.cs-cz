---
title: 'Poradce při potížích s připojením VPN azure site-to-site, které nemůže připojit titleSuffix: Azure VPN Gateway'
description: Přečtěte si, jak řešit potíže s připojením VPN mezi servery, které náhle přestane fungovat a nelze jej znovu připojit.
services: vpn-gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 4e827c5f6eedc819bc3635cb09a28f65df51312c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862573"
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Řešení potíží: Připojení VPN azure site-to-site se nemůže připojit a přestane fungovat

Po konfiguraci připojení VPN mezi místní sítí a virtuální sítí Azure připojení VPN náhle přestane fungovat a nelze je znovu připojit. Tento článek obsahuje postup řešení potíží, který vám pomůže tento problém vyřešit. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Postup při řešení potíží

Chcete-li tento problém vyřešit, zkuste nejprve [obnovit bránu Azure VPN](vpn-gateway-resetgw-classic.md) a obnovit tunelové propojení z místního zařízení VPN. Pokud problém přetrvává, postupujte podle následujících kroků k identifikaci příčiny problému.

### <a name="prerequisite-step"></a>Předpokladkrok

Zkontrolujte typ brány Azure VPN.

1. Přejděte na [portál Azure](https://portal.azure.com).

2. Informace o typu najdete na stránce **Přehled** brány VPN.
    
    ![Přehled brány](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Krok 1. Zkontrolujte, zda je místní zařízení VPN ověřeno

1. Zkontrolujte, zda používáte [ověřené zařízení VPN a verzi operačního systému](vpn-gateway-about-vpn-devices.md#devicetable). Pokud zařízení není ověřené zařízení VPN, budete muset kontaktovat výrobce zařízení a zjistit, zda došlo k problému s kompatibilitou.

2. Zkontrolujte, zda je zařízení VPN správně nakonfigurováno. Další informace naleznete v tématu [Úprava ukázek konfigurace zařízení](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>Krok 2. Ověření sdíleného klíče

Porovnejte sdílený klíč pro místní zařízení VPN s VPN virtuální sítě Azure a ujistěte se, že klíče odpovídají. 

Chcete-li zobrazit sdílený klíč pro připojení Azure VPN, použijte jednu z následujících metod:

**Portál Azure**

1. Přejděte na připojení mezi lokalitami brány VPN, které jste vytvořili.

2. V části **Nastavení** klikněte na **sdílený klíč**.
    
    ![Sdílený klíč](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pro model nasazení Azure Resource Manager:

    Get-AzVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

Pro klasický model nasazení:

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>Krok 3. Ověření IP adresy partnerské sítě VPN

-   Definice IP adresy v objektu **místní síťové brány** v Azure by měla odpovídat ip adrese místního zařízení.
-   Definice IP adresy brány Azure, která je nastavená na místním zařízení, by měla odpovídat IP službě brány Azure.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>Krok 4. Kontrola udr a skupin nsg v podsíti brány

Zkontrolujte a odeberte uživatelem definované směrování (UDR) nebo skupiny zabezpečení sítě (NSG) v podsíti brány a výsledek otestujte. Pokud je problém vyřešen, ověřte nastavení, které použilo UDR nebo NSG.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>Krok 5. Kontrola adresy externího rozhraní zařízení VPN pro místní prostředí

- Pokud je internetová IP adresa zařízení VPN zahrnuta v definici **místní sítě** v Azure, může dojít k sporadickému odpojení.
- Externí rozhraní zařízení musí být přímo na Internetu. Mezi Internetem a zařízením by neměl být žádný překlad síťových adres nebo brána firewall.
- Chcete-li nakonfigurovat clustering brány firewall tak, aby měl virtuální IP adresu, musíte cluster přerušit a vystavit zařízení VPN přímo do veřejného rozhraní, se kterým může brána komunikovat.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>Krok 6. Ověřte, zda se podsítě přesně shodují (brány založené na zásadách Azure)

-   Ověřte, zda adresní prostorvirtuální sítě přesně odpovídá virtuální síti Azure a místním definicím.
-   Ověřte, zda podsítě přesně odpovídají mezi **bránou místní sítě** a místními definicemi místní sítě.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>Krok 7. Ověření sondy stavu brány Azure

1. Otevřete sondu stavu procházením na následující adresu URL:

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. Proklikejte se upozorněním na certifikát.
3. Pokud obdržíte odpověď, brána VPN je považována za v pořádku. Pokud neobdržíte odpověď, brána nemusí být v pořádku nebo problém způsobuje skupina nsg v podsíti brány. Následující text je ukázková odpověď:

    ```xml
    <?xml version="1.0"?>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Primary Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6</string>
    ```

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>Krok 8. Zkontrolujte, zda je místní zařízení VPN vybaveno funkcí dokonalého utajení dopředu.

Dokonalá funkce utajení vpřed může způsobit problémy s odpojením. Pokud je zařízení VPN povoleno dokonalé utajení dopředu, zakažte tuto funkci. Pak aktualizujte zásadu IPsec brány VPN.

## <a name="next-steps"></a>Další kroky

-   [Konfigurace připojení mezi lokalitami k virtuální síti](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Konfigurace zásad Protokolu IPsec/IKE pro připojení VPN mezi lokalitami](vpn-gateway-ipsecikepolicy-rm-powershell.md)
