---
title: 'Vzdálená práce s P2S: Brána AZURE VPN'
description: Tato stránka popisuje, jak můžete využít VPN Gateway, abyste umožnili vzdálenou práci kvůli pandemii COVID-19.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: alzam
ms.openlocfilehash: 2d07a13c654f30e48c37d2e8d3e801166e26f4f4
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886581"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Vzdálená práce s využitím bodu na pracoviště brány Azure VPN

>[!NOTE]
>Tento článek popisuje, jak můžete využít Azure VPN Gateway, Azure, microsoft network a ekosystém partnerů Azure k vzdálené práci a zmírnění problémů se sítí, kterým čelíte kvůli krizi COVID-19.
>

Tento článek popisuje možnosti, které jsou k dispozici organizacím nastavit vzdálený přístup pro své uživatele nebo doplnit jejich stávající řešení s další kapacitou během epidemie COVID-19.

Řešení Azure point-to-site je založené na cloudu a lze je rychle zřídit, aby bylo možné uspokojit zvýšenou poptávku uživatelů po práci z domova. To může škálovat až snadno a vypnout stejně snadno a rychle, když zvýšená kapacita není potřeba už.

## <a name="about-point-to-site-vpn"></a><a name="p2s"></a>O síti VPN z bodu na web

Připojení brány VPN typu Point-to-Site (P2S) umožňuje vytvořit zabezpečené připojení k virtuální síti z jednotlivých klientských počítačů. Připojení P2S se vytvoří jeho zahájením z klientského počítače. Toto řešení je užitečné pro pracovníky pracující na dálku, kteří se chtějí připojit k virtuálním sítím Azure nebo místním datovým centrům ze vzdáleného umístění, například z domova nebo z konference. Tento článek popisuje, jak povolit uživatelům pracovat vzdáleně na základě různých scénářů.

V následující tabulce jsou uvedeny klientské operační systémy a možnosti ověřování, které jsou k dispozici. Bylo by užitečné vybrat metodu ověřování na základě klientského operačního systému, který je již používán. Pokud máte například směs klientských operačních systémů, které se potřebují připojit, vyberte openvpn s ověřováním založeným na certifikátu. Upozorňujeme také, že vpn z bodu na místo je podporována pouze na branách VPN založených na trasách.

![bod na místo](./media/working-remotely-support/ostable.png "Operační systém")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a><a name="scenario1"></a>Scénář 1 – Uživatelé potřebují přístup k prostředkům jenom v Azure

V tomto scénáři připojení k vzdáleným uživatelům stačí k přístupu k prostředkům, které jsou v Azure.

![bod na místo](./media/working-remotely-support/scenario1.png "Scénář 1")

Na vysoké úrovni jsou potřeba následující kroky, které uživatelům umožní bezpečné připojení k prostředkům Azure:

1. Vytvořte bránu virtuální sítě (pokud neexistuje).
2. Konfigurace sítě VPN z bodu na místo na bráně.

   * Ověřování certifikátů naleznete na [tomto odkazu](vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw).
   * Pro OpenVPN, následujte [tento odkaz](vpn-gateway-howto-openvpn.md).
   * Pro ověřování Azure AD, postupujte podle [tohoto odkazu](openvpn-azure-ad-tenant.md).
   * Řešení potíží s připojením bodu k webu naleznete na [tomto odkazu](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
3. Stáhněte a distribuujte konfiguraci klienta VPN.
4. Distribuovat certifikáty (pokud je vybráno ověřování certifikátů) klientům.
5. Připojte se k Azure VPN.

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a><a name="scenario2"></a>Scénář 2 – uživatelé potřebují přístup k prostředkům v Azure a/nebo na prem prostředky

V tomto scénáři vzdálení uživatelé potřebují přístup k prostředkům, které jsou v Azure a v místních datových center.

![bod na místo](./media/working-remotely-support/scenario2.png "Scénář 2")

Na vysoké úrovni jsou potřeba následující kroky, které uživatelům umožní bezpečné připojení k prostředkům Azure:

1. Vytvořte bránu virtuální sítě (pokud neexistuje).
2. Konfigurace sítě VPN z bodu na místo na bráně (viz [scénář 1](#scenario1)).
3. Nakonfigurujte tunelové propojení mezi lokalitami na bráně virtuální sítě Azure s povoleným protokolem BGP.
4. Nakonfigurujte místní zařízení pro připojení k bráně virtuální sítě Azure.
5. Stažení profilu z bodu na web z portálu Azure a distribuce klientům

Informace o nastavení tunelového propojení VPN mezi lokalitami naleznete v [tomto odkazu](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Nejčastější dotazy k nativnímu ověřování certifikátů Azure

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Nejčastější dotazy k ověřování v rámci radius

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Další kroky

* [Konfigurace připojení P2S – ověřování Azure AD](openvpn-azure-ad-tenant.md)

* [Konfigurace připojení P2S – ověřování RADIUS](point-to-site-how-to-radius-ps.md)

* [Konfigurace připojení P2S – ověřování nativního certifikátu Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" je ochranná známka společnosti OpenVPN Inc.**