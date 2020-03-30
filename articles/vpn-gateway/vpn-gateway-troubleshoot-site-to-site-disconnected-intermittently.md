---
title: Poradce při potížích s azure site-to-site VPN odpojí občas
description: Přečtěte si, jak vyřešit problém, kdy se připojení VPN site-to-Site pravidelně odpojilo.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: chadmath
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 82054099a5a496e99c49135ab98ee1163af19784
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862556"
---
# <a name="troubleshooting-azure-site-to-site-vpn-disconnects-intermittently"></a>Řešení potíží: Azure Site-to-Site VPN odpojuje občas

Může se vyskytnat problém, že nové nebo stávající připojení VPN mezi lokalitami microsoft azure není stabilní nebo se pravidelně odpojuje. Tento článek obsahuje kroky pro řešení potíží, které vám pomohou identifikovat a vyřešit příčinu problému. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-steps"></a>Postup při řešení potíží

### <a name="prerequisite-step"></a>Předpokladkrok

Zkontrolujte typ brány virtuální sítě Azure:

1. Přejděte na [portál Azure](https://portal.azure.com).
2. Informace o typu na stránce **Přehled** brány virtuální sítě najdete.
    
    ![Přehled brány](media/vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently/gatewayoverview.png)

### <a name="step-1-check-whether-the-on-premises-vpn-device-is-validated"></a>Krok 1 Kontrola, zda je místní zařízení VPN ověřeno

1. Zkontrolujte, zda používáte [ověřené zařízení VPN a verzi operačního systému](vpn-gateway-about-vpn-devices.md#devicetable). Pokud zařízení VPN není ověřeno, bude pravděpodobně muset kontaktovat výrobce zařízení a zjistit, zda došlo k nějakému problému s kompatibilitou.
2. Zkontrolujte, zda je zařízení VPN správně nakonfigurováno. Další informace naleznete [v tématu Úpravy ukončovacích vzorků zařízení](vpn-gateway-about-vpn-devices.md#editing).

### <a name="step-2-check-the-security-association-settingsfor-policy-based-azure-virtual-network-gateways"></a>Krok 2 Zkontrolujte nastavení přidružení zabezpečení (pro brány virtuální sítě Azure založené na zásadách)

1. Ujistěte se, že virtuální síť, podsítě a rozsahy v **definici místní síťové brány** v Microsoft Azure jsou stejné jako konfigurace na místním zařízení VPN.
2. Ověřte, zda se nastavení přidružení zabezpečení shodují.

### <a name="step-3-check-for-user-defined-routes-or-network-security-groups-on-gateway-subnet"></a>Krok 3 Kontrola uživatelem definovaných tras nebo skupin zabezpečení sítě v podsíti brány

Uživatelem definovaná trasa v podsíti brány může omezovat určitý provoz a povolovat jiný provoz. Díky tomu se zdá, že připojení VPN je nespolehlivé pro některé přenosy a dobré pro ostatní. 

### <a name="step-4-check-the-one-vpn-tunnel-per-subnet-pair-setting-for-policy-based-virtual-network-gateways"></a>Krok 4 Zkontrolujte nastavení "jeden tunel ový tunel VPN na pár podsítě" (pro brány virtuálnísítě založené na zásadách)

Ujistěte se, že místní zařízení VPN je nastaveno tak, aby pro brány virtuálnísítě založené na zásadách bylo mít **jeden tunelové propojení VPN na dvojici podsítě.**

### <a name="step-5-check-for-security-association-limitation-for-policy-based-virtual-network-gateways"></a>Krok 5 Kontrola omezení přidružení zabezpečení (pro brány virtuálnísítě založené na zásadách)

Brána virtuální sítě založená na zásadách má limit 200 párů přidružení zabezpečení podsítě. Pokud je počet podsítí virtuální sítě Azure vynásobený časy počtem místních podsítí je větší než 200, zobrazí se sporadické odpojení podsítí.

### <a name="step-6-check-on-premises-vpn-device-external-interface-address"></a>Krok 6 Kontrola adresy externího rozhraní zařízení VPN v místním prostředí

- Pokud je ip adresa ip zařízení VPN, která se blíží internetu, zahrnuta v definici **místní síťové brány** v Azure, může docházet k sporadickým odpojením.
- Externí rozhraní zařízení musí být přímo na Internetu. Mezi Internetem a zařízením by neměl být žádný překlad síťových adres (NAT) ani brána firewall.
-  Pokud nakonfigurujete clustering brány firewall tak, aby měl virtuální IP adresu, musíte cluster přerušit a vystavit zařízení VPN přímo do veřejného rozhraní, se kterým může brána komunikovat.

### <a name="step-7-check-whether-the-on-premises-vpn-device-has-perfect-forward-secrecy-enabled"></a>Krok 7 Zkontrolujte, zda je v místním zařízení VPN povoleno dokonalé utajení vpřed

Funkce **Perfect Forward Secrecy** může způsobit problémy s odpojením. Pokud je v zařízení VPN povoleno **dokonalé utajení vpřed,** zakažte tuto funkci. Potom [aktualizujte zásady ipsec brány virtuální sítě](vpn-gateway-ipsecikepolicy-rm-powershell.md#managepolicy).

## <a name="next-steps"></a>Další kroky

- [Konfigurace připojení site-to-site k virtuální síti](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Konfigurace zásad Protokolu IPsec/IKE pro připojení VPN mezi lokalitami](vpn-gateway-ipsecikepolicy-rm-powershell.md)

