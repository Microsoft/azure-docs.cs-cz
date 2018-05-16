---
title: Řešení potíží s Azure Site-to-Site VPN odpojí občas | Microsoft Docs
description: Zjistěte, jak k vyřešení tohoto problému, v němž odpojena pravidelně připojení Site-to-Site VPN.
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
ms.openlocfilehash: 9c827469080195054d4ff70ab72fc123365a73df
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Řešení potíží: Azure Site-to-Site VPN odpojí občas

Může dojít k potížím, že nové nebo existující připojení VPN typu Site-to-Site Microsoft Azure není stabilní nebo odpojí pravidelně. Tento článek obsahuje řešení potíží s kroky, které vám pomohou identifikovat a vyřešit příčinu problému. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Postup při řešení potíží

### <a name="prerequisite-step"></a>Požadovaný krok

Zkontrolujte typ brány virtuální sítě Azure:

1. Přejděte na [portál Azure](https://portal.azure.com).
2. Zkontrolujte **přehled** stránky bránu virtuální sítě pro informace o typu.
    
    ![Přehled brány](media\vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Krok 1 Zkontrolujte, zda byl ověřen místního zařízení VPN

1. Zkontrolujte, zda používáte [ověřit zařízení VPN a verzi operačního systému](vpn-gateway-about-vpn-devices.md#devicetable). Pokud není ověřená zařízení VPN, budete muset obraťte se na výrobce zařízení a jestli jsou všechny potíže s kompatibilitou.
2. Ujistěte se, zda je správně nakonfigurováno zařízení VPN. Další informace najdete v tématu [ukázky úpravy konfigurace zařízení](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Krok 2 Zkontrolujte nastavení přidružení zabezpečení (pro brány na základě zásad Azure virtuální sítě)

1. Ujistěte se, že virtuální sítě, podsítě a rozsahy v **brány místní sítě** definice v Microsoft Azure jsou stejné jako konfiguraci místního zařízení VPN.
2. Ověřte, že odpovídají nastavení přidružení zabezpečení.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Krok 3 kontroly pro trasy definované uživatelem nebo skupiny zabezpečení sítě na podsíť brány

Uživatelem definované trasy na podsíť brány mohou některé provozy, omezení a umožňuje ostatní provoz. Díky tomu se zobrazí, že je připojení k síti VPN pro některé provozy, nespolehlivé a vhodný pro ostatní. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Krok 4 kontrola "jeden tunelového připojení sítě VPN za pár podsítě" nastavení (pro brány virtuální sítě na základě zásad)

Ujistěte se, že místního zařízení VPN je nastaven tak, aby měl **jeden tunelového připojení sítě VPN za pár podsítě** pro brány virtuální sítě na základě zásad.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Krok 5 Kontrola omezení přidružení zabezpečení (pro brány virtuální sítě na základě zásad)

Brána virtuální sítě založené na zásadách má limit 200 párů podsítě přidružení zabezpečení. Pokud počet podsítí virtuální sítě Azure násobí časy podle počtu místní podsítě je větší než 200, najdete v části ojediněle podsítě odpojení.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Krok 6 zkontrolujte místní adresa externí rozhraní zařízení VPN

- Pokud je součástí Internetové IP adresa zařízení VPN **brány místní sítě** definice v Azure, se může vyskytnout občasné odpojení.
- Externí rozhraní zařízení musí být přímo na Internetu. Měla by existovat žádné překlad adres (NAT) nebo brány firewall mezi Internetu a zařízení.
-  Pokud nakonfigurujete Clustering brány Firewall tak, aby měl virtuální IP adresy, musí přerušení clusteru a vystavit zařízení VPN přímo k veřejné rozhraní, které můžete bránu rozhraní s.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Krok 7 zkontrolujte, jestli má metoda Perfect Forward Secrecy povoleno místního zařízení VPN

**Metoda Perfect Forward Secrecy** funkce může způsobit problémy odpojení. Pokud má zařízení VPN **ideální přeposílání** povolit, zakázat funkci. Potom [aktualizovat bránu virtuální sítě zásad protokolu IPsec](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Další postup

- [Konfigurace připojení typu Site-to-Site k virtuální síti.](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Konfigurace zásad protokolu IPsec/IKE pro připojení VPN typu Site-to-Site](vpn-gateway-ipsecikepolicy-rm-powershell.md)

