---
title: Řešení problémů s Azure VPN typu Site-to-Site se přerušovaně odpojuje | Dokumentace Microsoftu
description: Zjistěte, jak k vyřešení tohoto problému, ve kterém odpojené připojení Site-to-Site VPN pravidelně.
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
ms.openlocfilehash: 9d831f7f08af174dfc6ce429da4d0a0daaeda0e9
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50240014"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Řešení potíží: Azure VPN typu Site-to-Site se přerušovaně odpojuje

Můžete zaznamenat problém, že nové nebo existující připojení k síti Microsoft Azure Site-to-Site VPN není stabilní nebo odpojí pravidelně. Tento článek obsahuje řešení potíží s postup vám pomůže identifikovat a vyřešit příčinu problému. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Postup při řešení potíží

### <a name="prerequisite-step"></a>Požadovaný krok

Zkontrolujte typ brány virtuální sítě Azure:

1. Přejděte na [webu Azure portal](https://portal.azure.com).
2. Zkontrolujte, **přehled** stránku brány virtuální sítě pro informace o typu.
    
    ![Přehled brány](media\vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently\gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Krok 1 Zkontrolujte, zda je ověřen místního zařízení VPN

1. Zkontrolujte, jestli používáte [ověřit zařízení VPN a verze operačního systému](vpn-gateway-about-vpn-devices.md#devicetable). Pokud není ověřená zařízení VPN, budete muset kontaktovat výrobce zařízení, jestli jsou všechny potíže s kompatibilitou.
2. Ujistěte se, že je zařízení VPN správně nakonfigurovaný. Další informace najdete v tématu [ukázky úpravy konfigurace zařízení](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Krok 2 Zkontrolujte nastavení přidružení zabezpečení (pro brány založené na zásadách Azure virtuální sítě)

1. Ujistěte se, že virtuální sítě, podsítě a rozsahy adres ve **bránu místní sítě** definice v Microsoft Azure jsou stejné jako konfiguraci na místní zařízení VPN.
2. Ověřte, že odpovídají nastavení přidružení zabezpečení.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Krok 3 vyhledat trasy definované uživatelem nebo skupiny zabezpečení sítě v podsíti brány

Uživatelem definovaná trasa v podsíti brány může omezení část provozu a povolení dalších provozu. Díky tomu se zobrazí, že je připojení k síti VPN nespolehlivé pro část provozu a vhodné pro ostatní uživatele. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Krok 4 kontrola "Jeden tunel VPN za pár podsítě" nastavení (pro brány virtuální sítě na základě zásad)

Ujistěte se, že je nastavena na místním zařízení VPN mít **jeden tunel VPN za pár podsítě** pro brány virtuální sítě založené na zásadách.

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Krok 5 Kontrola omezení přidružení zabezpečení (pro brány virtuální sítě na základě zásad)

Brána virtuální sítě založené na zásadách má limit 200 přidružení zabezpečení dvojice podsítě. Pokud počet podsítí virtuální sítě Azure, které se vynásobené časy podle počtu místní podsítě je větší než 200 naleznete v tématu sporadické podsítě odpojení.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Krok 6 zkontrolujte místní adresa externí rozhraní zařízení VPN

- Pokud je součástí Internetové IP adresa zařízení VPN **bránu místní sítě** definice v Azure, se může vyskytnout sporadické odpojení.
- Externí rozhraní zařízení musí být přímo na Internetu. Musí být bez překladu adres (NAT) nebo bránu firewall mezi Internetem a zařízení.
-  Při konfiguraci brány Firewall Clustering mít virtuální IP adresu, musíte přerušit clusteru a vystavit zařízení VPN přímo do veřejného rozhraní, které brána lze rozhraní s.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Krok 7 zkontrolujte, jestli má metoda Perfect Forward Secrecy povolené místního zařízení VPN

**Perfect Forward Secrecy** funkce může způsobit problémy odpojení. Pokud má zařízení VPN **Zdokonalujete přeposílání** povolena, zakažte tuto funkci. Potom [aktualizovat bránu virtuální sítě zásady IPsec](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Další postup

- [Konfigurace připojení typu Site-to-Site k virtuální síti](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Konfigurace zásad IPsec/IKE pro připojení VPN typu Site-to-Site](vpn-gateway-ipsecikepolicy-rm-powershell.md)

