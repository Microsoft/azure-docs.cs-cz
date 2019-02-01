---
title: Řešení potíží s Azure site-to-site pro připojení VPN, který nejde se připojit | Dokumentace Microsoftu
description: Informace o řešení potíží s připojením VPN site-to-site, která náhle přestane fungovat a nelze je připojit.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 18900c4a1dbc25526a3f60c7410ad87e7dd9a9fa
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55507295"
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Řešení potíží: Připojení k Azure VPN typu site-to-site se nemůže připojit a přestane fungovat

Po dokončení konfigurace připojení site-to-site VPN mezi místní sítí a virtuální síť Azure, připojení k síti VPN náhle přestane fungovat a nelze je připojit. Tento článek obsahuje postup řešení potíží, které vám pomohou vyřešit tento problém. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Postup při řešení potíží

Chcete-li problém vyřešit, nejprve zkuste [resetování brány Azure VPN](vpn-gateway-resetgw-classic.md) a obnovit tunelového propojení z místního zařízení VPN. Pokud se problém nevyřeší, použijte následující postup zjištění příčiny problému.

### <a name="prerequisite-step"></a>Požadovaný krok

Zkontrolujte typ brány Azure VPN.

1. Přejděte na [Azure Portal](https://portal.azure.com).

2. Zkontrolujte **přehled** stránku brány sítě VPN pro informace o typu.
    
    ![Přehled brány](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Krok 1. Zkontrolujte, zda je ověřen místního zařízení VPN

1. Zkontrolujte, jestli používáte [ověřit zařízení VPN a verze operačního systému](vpn-gateway-about-vpn-devices.md#devicetable). Pokud zařízení není ověřená zařízení VPN, budete muset obraťte na výrobce zařízení a zjistit, jestli potíže s kompatibilitou.

2. Ujistěte se, že je zařízení VPN správně nakonfigurovaný. Další informace najdete v tématu [upravit ukázky konfigurace zařízení](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>Krok 2. Ověřte sdílený klíč

Porovnejte sdílený klíč pro místní zařízení VPN k Azure virtuální sítě VPN abyste měli jistotu, že klíče shodují. 

Chcete-li zobrazit sdílený klíč pro připojení k síti VPN Azure, použijte jednu z následujících metod:

**Azure Portal**

1. Přejděte na připojení brány VPN typu site-to-site, který jste vytvořili.

2. V **nastavení** klikněte na tlačítko **sdílený klíč**.
    
    ![Sdílený klíč](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

Pro model nasazení Azure Resource Manageru:

    Get-AzureRmVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

Pro model nasazení classic:

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>Krok 3. Ověření IP adres partnerské sítě VPN

-   Definice IP ve **brány místní sítě** objektu v Azure by měl odpovídat IP v místním zařízení.
-   Definice IP brány Azure, který je nastaven na místním zařízení by měl odpovídat IP adresu brány Azure.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>Krok 4. Zkontrolujte směrování definovaného uživatelem a skupin zabezpečení sítě v podsíti brány

Zkontrolujte a odeberte uživatelem definované směrování (UDR) nebo skupiny zabezpečení sítě (Nsg) v podsíti brány a klikněte výsledek testu. Pokud se problém vyřeší, ověřte nastavení, která je použita uživatelem definovaná TRASA nebo skupiny zabezpečení sítě.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>Krok 5. Zkontrolujte adresu místní zařízení VPN externí rozhraní

- Pokud je součástí Internetová IP adresa zařízení VPN **místní sítě** definice v Azure, může dojít k občasnému odpojení.
- Externí rozhraní zařízení musí být přímo na Internetu. Musí být bez překladu adres nebo brány firewall mezi Internetem a zařízení.
- Ke konfiguraci clusteringu mít virtuální IP adresu brány firewall, musíte přerušit clusteru a vystavit zařízení VPN přímo do veřejného rozhraní, které brána lze rozhraní s.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>Krok 6. Ověřte, jestli přesně odpovídají podsítě (Azure založené na zásadách brány)

-   Ověřte, že prostory adres virtuální sítě odpovídají přesně mezi virtuální sítí Azure a místní definice.
-   Ověřte, že odpovídají přesně mezi podsítěmi **brány místní sítě** a místní definice v místní síti.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>Krok 7. Ověření Azure gateway sondu stavu

1. Sonda stavu otevřete tak, že přejdete na následující adresu URL:

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. Proklikejte se upozornění certifikátu.
3. Pokud se zobrazí odpověď, VPN gateway se považuje za v pořádku. Pokud jste neobdrželi odpověď, brána nemusí být v pořádku nebo skupinu zabezpečení sítě v podsíti brány je příčinou problému. Následující text je ukázkové odpovědi:

    &lt;? xml verze = "1.0"? > <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">primární Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6 < / string&gt;

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>Krok 8. Zkontrolujte, zda je povolena funkce metoda perfect forward secrecy místního zařízení VPN

Metoda perfect forward secrecy funkce může způsobit problémy odpojení. Pokud zařízení VPN metoda perfect forward secrecy povolena, zakažte tuto funkci. Pak aktualizujte zásady IPsec brány sítě VPN.

## <a name="next-steps"></a>Další postup

-   [Konfigurace připojení typu site-to-site k virtuální síti](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Konfigurace zásad IPsec/IKE pro připojení VPN typu site-to-site](vpn-gateway-ipsecikepolicy-rm-powershell.md)
