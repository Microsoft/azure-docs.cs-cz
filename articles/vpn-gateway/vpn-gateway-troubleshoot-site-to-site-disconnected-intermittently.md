---
title: Řešení potíží s připojením VPN typu Site-to-site k výpadku
description: Naučte se, jak řešit potíže s tím, že připojení VPN typu Site-to-site se pravidelně odpojí.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 02/10/2021
ms.author: genli
ms.openlocfilehash: 582077c46f5fc7176b457cf0d392af48fbe7d40b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100369335"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Řešení potíží: Připojení site-to-site VPN k Azure se přerušovaně odpojuje

Může dojít k problému s tím, že nové nebo existující Microsoft Azure připojení VPN typu Site-to-site není pravidelně stabilní nebo se odpojuje. Tento článek popisuje kroky pro řešení potíží, které vám pomůžou identifikovat a vyřešit příčinu problému. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Postup při řešení potíží

### <a name="prerequisite-step"></a>Krok požadovaných součástí

Ověřte typ brány virtuální sítě Azure:

1. Přejít na [Azure Portal](https://portal.azure.com).
2. Informace o typu najdete na stránce s **přehledem** brány virtuální sítě.
    
    ![Přehled brány](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Krok 1: ověření, jestli je místní zařízení VPN ověřené

1. Ověřte, zda používáte [ověřené zařízení VPN a verzi operačního systému](vpn-gateway-about-vpn-devices.md#devicetable). Pokud se zařízení VPN neověřuje, možná budete muset kontaktovat výrobce zařízení a zjistit, jestli došlo k nějakému problému s kompatibilitou.
2. Ujistěte se, že je zařízení VPN správně nakonfigurované. Další informace najdete v tématu [Úprava ukázek konfigurace zařízení](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Krok 2: ověření nastavení přidružení zabezpečení (pro brány virtuální sítě Azure založené na zásadách)

1. Zajistěte, aby byla virtuální síť, podsítě a rozsahy v definici **brány místní sítě** v Microsoft Azure stejné jako konfigurace na místním zařízení VPN.
2. Ověřte, že se nastavení přidružení zabezpečení shodují.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Krok 3 – vyhledání tras User-Defined nebo skupin zabezpečení sítě v podsíti brány

Trasa definovaná uživatelem v podsíti brány může omezit určitý provoz a povolit další provoz. Tím se zobrazí, že připojení VPN není pro určitý provoz a dobré pro ostatní funkční. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Krok 4: nastavení jedné dvojice tunelového propojení VPN na jednu podsíť (pro brány virtuální sítě založené na zásadách)

Ujistěte se, že místní zařízení VPN je nastavené tak, aby pro brány virtuální sítě založené na zásadách mělo **jednu dvojici tunelu VPN na podsíť** .

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Krok 5: ověření omezení přidružení zabezpečení (pro brány virtuální sítě založené na zásadách)

Brána virtuální sítě založená na zásadách má omezení 200 párů přidružení zabezpečení podsítě. Pokud je počet podsítí virtuální sítě Azure vynásobený časem podle počtu místních podsítí větší než 200, zobrazí se občas odpojování podsítí.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Krok 6: místní adresa externího rozhraní zařízení VPN

- Pokud je internetová IP adresa zařízení VPN zahrnutá v definici **brány místní sítě** v Azure, může docházet ke občasnému odpojení.
- Externí rozhraní zařízení musí být přímo na internetu. Mezi Internetem a zařízením by neměl být překlad síťových adres (NAT) ani brána firewall.
-  Pokud nakonfigurujete clustering brány firewall tak, aby měl virtuální IP adresu, musíte cluster přerušit a vystavit zařízení VPN přímo veřejnému rozhraní, se kterým může Brána rozhraní používat.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Krok 7: ověření, zda má místní zařízení VPN povolené Perfect Forward Secrecy

Funkce **Perfect Forward Secrecy** může způsobit problémy s odpojením. Pokud má zařízení VPN povolené PFS ( **Perfect Forward Secrecy** ), zakažte tuto funkci. Pak [Aktualizujte zásady protokolu IPSec brány virtuální sítě](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Další kroky

- [Konfigurace připojení typu Site-to-site k virtuální síti](./tutorial-site-to-site-portal.md)
- [Konfigurace zásad IPsec/IKE pro připojení VPN typu Site-to-site](vpn-gateway-ipsecikepolicy-rm-powershell.md)
