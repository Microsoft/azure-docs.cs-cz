---
title: Řešení potíží s Azure připojení VPN typu site-to-site, která se nemůže připojit | Microsoft Docs
description: Zjistěte, jak řešení připojení site-to-site VPN, který najednou, přestane modul fungovat a nelze znovu připojit.
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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: dfd29e0956793cf776b9c0ea5ddbd4689ebcb015
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="troubleshooting-an-azure-site-to-site-vpn-connection-cannot-connect-and-stops-working"></a>Řešení potíží: Připojení k Azure site-to-site VPN se nemůže připojit a zastaví práce

Po dokončení konfigurace připojení site-to-site VPN mezi místní sítí a virtuální síť Azure, připojení k síti VPN najednou přestane fungovat a nelze znovu připojit. Tento článek obsahuje postup pro odstraňování potíží při řešení tohoto problému. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Postup při řešení potíží

Chcete-li problém vyřešit, zkuste je napřed [resetování brány Azure VPN](vpn-gateway-resetgw-classic.md) a resetovat tunelové propojení z místního zařízení VPN. Pokud potíže potrvají, postupujte podle těchto kroků zjistit příčinu problému.

### <a name="prerequisite-step"></a>Požadovaný krok

Zkontrolujte typ brány Azure VPN.

1. Přejděte na [portál Azure](https://portal.azure.com).

2. Zkontrolujte **přehled** stránka služby VPN gateway pro informace o typu.
    
    ![Přehled brány](media\vpn-gateway-troubleshoot-site-to-site-cannot-connect\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Krok 1. Zkontrolujte, zda je ověřen místního zařízení VPN

1. Zkontrolujte, zda používáte [ověřit zařízení VPN a verzi operačního systému](vpn-gateway-about-vpn-devices.md#devicetable). Pokud zařízení není ověřená zařízení VPN, budete možná muset obraťte se na výrobce zařízení a jestli jsou potíže s kompatibilitou.

2. Ujistěte se, zda je správně nakonfigurováno zařízení VPN. Další informace najdete v tématu [upravit ukázky konfigurace zařízení](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-verify-the-shared-key"></a>Krok 2. Ověřte sdílený klíč

Porovnání se sdílený klíč pro místní zařízení VPN Azure virtuální sítě VPN a ujistěte se, jestli se klíče shodují. 

Chcete-li zobrazit sdílený klíč pro připojení k síti VPN Azure, použijte jednu z následujících metod:

**Azure Portal**

1. Přejděte na připojení site-to-site VPN brány, kterou jste vytvořili.

2. V **nastavení** klikněte na tlačítko **sdílený klíč**.
    
    ![Sdílený klíč](media/vpn-gateway-troubleshoot-site-to-site-cannot-connect/sharedkey.png)

**Azure PowerShell**

Pro model nasazení Azure Resource Manager:

    Get-AzureRmVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group name>

Pro model nasazení classic:

    Get-AzureVNetGatewayKey -VNetName -LocalNetworkSiteName

### <a name="step-3-verify-the-vpn-peer-ips"></a>Krok 3. Ověřte sdílené sítě VPN IP adresy

-   V definici IP **bránu místní sítě** objektu v Azure by měl odpovídat IP místní zařízení.
-   Služba Azure gateway IP by měl odpovídat definici IP brány Azure, který je nastavený na místní zařízení.

### <a name="step-4-check-udr-and-nsgs-on-the-gateway-subnet"></a>Krok 4. Zkontrolujte UDR a skupiny Nsg na podsítě brány

Kontrolovat a odeberte uživatelem definované směrování (UDR) nebo skupiny zabezpečení sítě (Nsg) pro podsíť brány a poté otestujte výsledek. Pokud byl problém vyřešen, ověřte nastavení použité UDR nebo NSG.

### <a name="step-5-check-the-on-premises-vpn-device-external-interface-address"></a>Krok 5. Zkontrolujte adresu místního zařízení VPN externí rozhraní

- Pokud je součástí internetového IP adresa zařízení VPN **místní sítě** definice v Azure, můžete zaznamenat ojediněle odpojení.
- Externí rozhraní zařízení musí být přímo na Internetu. Měla by existovat žádné překlad síťových adres nebo brány firewall mezi Internetu a zařízení.
- Konfigurace brány firewall clustering tak, aby měl virtuální IP adresy, musí přerušení clusteru a vystavit zařízení VPN přímo k veřejné rozhraní, které můžete bránu rozhraní s.

### <a name="step-6-verify-that-the-subnets-match-exactly-azure-policy-based-gateways"></a>Krok 6. Ověřte, že podsítě přesně odpovídají (Azure na základě zásad brány)

-   Ověřte, že virtuální sítě adres mezerou (mezerami) odpovídají přesně mezi virtuální sítí Azure a místními definice.
-   Ověřte, že podsítí mezi přesně odpovídají **bránu místní sítě** a místní definice pro místní sítě.

### <a name="step-7-verify-the-azure-gateway-health-probe"></a>Krok 7. Ověřte test stavu služba Azure gateway

1. Test stavu otevřete procházením následující adresu URL:

    `https://<YourVirtualNetworkGatewayIP>:8081/healthprobe`

2. Proklikejte se prostřednictvím upozornění certifikátu.
3. Pokud se zobrazí odpověď, považuje za bránu sítě VPN v pořádku. Pokud jste neobdrželi odpověď, nemusí být brána v pořádku nebo skupinu NSG na podsítě brány je příčinou problému. Tento text je ukázková odpověď:

    &lt;? xml verze = "1.0"? > <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">primární Instance: GatewayTenantWorker_IN_1 GatewayTenantVersion: 14.7.24.6 < / řetězec&gt;

### <a name="step-8-check-whether-the-on-premises-vpn-device-has-the-perfect-forward-secrecy-feature-enabled"></a>Krok 8. Zkontrolujte, zda má místního zařízení VPN povolena funkce metoda perfect forward secrecy

Metoda perfect forward secrecy funkce může způsobit problémy odpojení. Pokud zařízení VPN má metoda perfect forward secrecy povolit, zakážete funkci. Aktualizujte bránu VPN typu zásad protokolu IPsec.

## <a name="next-steps"></a>Další postup

-   [Konfigurace připojení typu site-to-site k virtuální síti.](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
-   [Konfigurace zásady protokolu IPsec/IKE pro připojení VPN typu site-to-site](vpn-gateway-ipsecikepolicy-rm-powershell.md)
