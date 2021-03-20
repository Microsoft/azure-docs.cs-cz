---
title: 'Vzdálená práce s P2S: Azure VPN Gateway'
description: Tato stránka popisuje, jak můžete využít VPN Gateway, aby bylo možné vzdáleně pracovat z Pandemic COVID-19.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: eb0291b18952efc643de18e111154ebe58f041af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880080"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Vzdálená práce s využitím Azure VPN Gateway Point-to-site

>[!NOTE]
>Tento článek popisuje, jak můžete využít Azure VPN Gateway, Azure, síť Microsoftu a partnerský ekosystém Azure k tomu, abyste mohli vzdáleně pracovat a zmírnit problémy se sítí, ke kterým jste se přiCOVIDi z důvodu krize v 19.
>

Tento článek popisuje možnosti, které jsou k dispozici organizacím k nastavení vzdáleného přístupu pro své uživatele, nebo k doplnění stávajících řešení s dodatečnou kapacitou během epidemie COVID-19.

Řešení Azure Point-to-site je založené na cloudu a dá se rychle zřídit pro službu stravování, aby se zvýšila poptávka uživatelů při práci z domova. Dá se snadno a rychle škálovat, a to stejně snadno a rychle, když už není potřeba vyšší kapacita.

## <a name="about-point-to-site-vpn"></a><a name="p2s"></a>O síti VPN typu Point-to-site

Připojení brány VPN typu Point-to-Site (P2S) umožňuje vytvořit zabezpečené připojení k virtuální síti z jednotlivých klientských počítačů. Připojení P2S se vytvoří jeho zahájením z klientského počítače. Toto řešení je užitečné pro zaměstnance, kteří se chtějí připojit k Azure virtuální sítě nebo místním datovým centrům ze vzdáleného umístění, například z domova nebo z konference. Tento článek popisuje, jak uživatelům povolit vzdálenou práci na základě různých scénářů.

V následující tabulce jsou uvedeny klientské operační systémy a možnosti ověřování, které jsou pro ně k dispozici. Je užitečné vybrat metodu ověřování založenou na operačním systému klienta, který se už používá. Vyberte například OpenVPN s ověřováním na základě certifikátu, pokud máte kombinaci klientských operačních systémů, které se potřebují připojit. Upozorňujeme také, že síť VPN typu Point-to-site je podporována pouze u bran sítě VPN založených na trasách.

![Snímek obrazovky se zobrazenými klientskými operačními systémy a dostupnými možnostmi ověřování.](./media/working-remotely-support/ostable.png "Operační systém")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a><a name="scenario1"></a>Scénář 1 – uživatelé potřebují přístup k prostředkům jenom v Azure

V tomto scénáři potřebují vzdálení uživatelé přístup jenom k prostředkům, které jsou v Azure.

![Diagram, který zobrazuje scénář Point-to-site pro uživatele, kteří potřebují přístup k prostředkům jenom v Azure.](./media/working-remotely-support/scenario1.png "Scénář 1")

Aby se uživatelé mohli bezpečně připojit k prostředkům Azure, je na nejvyšší úrovni potřeba provést tyto kroky:

1. Vytvořte bránu virtuální sítě (Pokud neexistuje).
2. Konfigurace sítě VPN typu Point-to-site v bráně.

   * Pro ověřování pomocí certifikátu použijte [Tento odkaz](vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw).
   * Pro OpenVPN použijte [Tento odkaz](vpn-gateway-howto-openvpn.md).
   * Pro ověřování Azure AD použijte [Tento odkaz](openvpn-azure-ad-tenant.md).
   * Pokud chcete řešit potíže s připojením Point-to-site, postupujte podle [tohoto odkazu](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
3. Stáhněte a distribuujte konfiguraci klienta VPN.
4. Distribuujte certifikáty (Pokud je vybrané ověřování certifikátů) pro klienty.
5. Připojte se k Azure VPN.

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a><a name="scenario2"></a>Scénář 2 – uživatelé potřebují přístup k prostředkům v Azure nebo v propremch prostředcích

V tomto scénáři potřebují vzdálení uživatelé přístup k prostředkům, které jsou v Azure, a v místních datových centrech.

![Diagram, který zobrazuje scénář pro uživatele, kteří potřebují přístup k prostředkům v Azure.](./media/working-remotely-support/scenario2.png "Scénář 2")

Aby se uživatelé mohli bezpečně připojit k prostředkům Azure, je na nejvyšší úrovni potřeba provést tyto kroky:

1. Vytvořte bránu virtuální sítě (Pokud neexistuje).
2. Konfigurace sítě VPN typu Point-to-site v bráně (viz [scénář 1](#scenario1)).
3. Konfigurace tunelového propojení typu Site-to-site v bráně virtuální sítě Azure s povoleným protokolem BGP
4. Nakonfigurujte místní zařízení pro připojení k bráně virtuální sítě Azure.
5. Stažení profilu Point-to-Site z Azure Portal a distribuce do klientů

Informace o nastavení tunelu VPN typu Site-to-site najdete na [tomto odkazu](./tutorial-site-to-site-portal.md).

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Nejčastější dotazy k nativnímu ověřování certifikátů Azure

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Nejčastější dotazy k ověřování RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Další kroky

* [Konfigurace připojení P2S – ověřování Azure AD](openvpn-azure-ad-tenant.md)

* [Konfigurace připojení P2S – ověřování pomocí protokolu RADIUS](point-to-site-how-to-radius-ps.md)

* [Konfigurace připojení P2S – ověření nativního certifikátu Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" je ochranná známka společnosti OpenVPN Inc.**