---
title: O připojení VPN typu Azure Point-to-Site | Microsoft Docs
description: Tento článek vám pomůže pochopit připojení Point-to-Site a vám pomůže zjistit, který typ ověřování brány P2S VPN používat.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/06/2018
ms.author: cherylmc
ms.openlocfilehash: 2668d92b5b933f7ccf8ebcccbe7ea77ea6ea1e86
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236429"
---
# <a name="about-point-to-site-vpn"></a>O Point-to-Site VPN

Připojení brány VPN typu Point-to-Site (P2S) umožňuje vytvořit zabezpečené připojení k virtuální síti z jednotlivých klientských počítačů. Připojení P2S se vytvoří jeho zahájením z klientského počítače. Toto řešení je užitečné pro osoby pracující z domova, které se chtějí k virtuálním sítím Azure připojit ze vzdáleného umístění, například z domova nebo z místa konání konference. Síť VPN P2S je také užitečným řešením nahrazujícím síť VPN S2S, pokud máte pouze několik klientů, kteří se potřebují připojit k virtuální síti. Tento článek se týká modelu nasazení Resource Manager.

## <a name="protocol"></a>Jaký protokol pro P2S používat?

Point-to-site VPN můžete použít jednu z těchto protokolů:

* Zabezpečte SSTP Socket Tunneling Protocol (), vlastnickým protokolem sítě VPN založené na protokolu SSL. Řešení s SSL VPN můžete proniknout brány firewall, protože většinu bran firewall otevřete port TCP 443, který používá protokol SSL. SSTP je podporována pouze na zařízení se systémem Windows. Azure podporuje všechny verze systému Windows, které mají SSTP (Windows 7 a novější).

* IKEv2 VPN, řešení IPsec VPN založené na standardech. IKEv2 VPN je možné použít k připojení ze zařízení se systémem Mac (OSX verze 10.11 a vyšší).

Pokud máte prostředí různých klientských skládající se z zařízení s Windows a Mac, nakonfigurujte SSTP a IKEv2.

>[!NOTE]
>IKEv2 pro P2S je k dispozici pro pouze modelu nasazení Resource Manager. Není k dispozici pro model nasazení classic.
>

## <a name="authentication"></a>Jak jsou klienti P2S VPN ověřuje?

Před Azure akceptuje připojení P2S VPN, uživatel musí nejdřív ověřit. Existují dva mechanismy, které Azure nabízí k ověření připojujícího uživatele.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Ověřování pomocí certifikátu nativní Azure ověřování

Při použití ověřování nativní Azure certifikátů, klientský certifikát, který se nachází na zařízení slouží k ověření připojujícího se uživatele. Klientské certifikáty jsou generovány z důvěryhodného kořenového certifikátu a následně je nainstalován na každém klientském počítači. Můžete použít kořenový certifikát, který byl vytvořen pomocí podnikové řešení, nebo můžete vygenerovat certifikát podepsaný svým držitelem.

Ověření certifikátu klienta se provádí pomocí služby VPN gateway a se stane při navazování připojení P2S VPN. Kořenový certifikát je vyžadován pro ověření a musí se nahrát do Azure.

### <a name="authenticate-using-active-directory-ad-domain-server"></a>Ověřování pomocí serveru domény Active Directory (AD)

Ověřování v doméně AD umožňuje uživatelům připojit se k Azure pomocí svých přihlašovacích údajů domény organizace. To vyžaduje server RADIUS, který se integruje s serveru služby AD. Organizace můžete využít i jejich stávající nasazení protokolu RADIUS.   
  RADIUS server, může být nasadit místně nebo ve vaší virtuální síti Azure. Při ověřování služby Azure VPN Gateway funguje jako předávání a předávání zpráv ověřování mezi serverem RADIUS a zařízení pro připojení. Proto je důležité dostupnosti brány se serverem RADIUS. Pokud RADIUS server je přítomen v místě, je nutné pro dostupnosti VPN S2S připojení z Azure do místní lokality.  
  RADIUS server, můžete také integrovat s AD CS. To vám umožní používat RADIUS server a vaše podnikové nasazení certifikátů pro ověřování pomocí certifikátu P2S jako alternativu k ověřování Azure certifikátu. Výhodou je, že nemusíte odeslání kořenové certifikáty a odvolané certifikáty do Azure.

RADIUS server můžete také integrovat s jinými systémy externí identity. Otevře se hodně možnosti ověřování pro P2S VPN, včetně možnosti vícefaktorového.

! [point-to-site]] (./media/point-to-site-about/p2s.png "Point-to-Site")

## <a name="what-are-the-client-configuration-requirements"></a>Jaké jsou požadavky na konfiguraci klienta?

>[!NOTE]
>Pro klienty systému Windows musí mít oprávnění správce v klientském zařízení za účelem zahájení připojení VPN z klientského zařízení do Azure.
>

Uživatelé používat nativní klienti VPN na zařízeních s Windows a Mac pro P2S. Azure poskytuje klienta VPN zip konfigurační soubor, který obsahuje nastavení, které jsou potřebné tyto nativní klienty pro připojení k Azure.

* Konfigurace klienta VPN se skládá z instalační balíček, který uživatelé instalují do svých zařízení pro zařízení s Windows.
* Pro zařízení se systémem Mac obsahuje mobileconfig souboru, který uživatelé instalují do svých zařízení.

Soubor zip také obsahuje hodnoty některých důležitých nastavení na straně Azure, můžete použít k vytvoření vlastního profilu pro tato zařízení. Některé hodnoty zahrnují adresu brány VPN, typy nakonfigurované tunelu, trasy a kořenový certifikát pro ověřování brány.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="gwsku"></a>Které P2S podporu SKU brány sítě VPN?

[!INCLUDE [p2s-skus](../../includes/vpn-gateway-table-point-to-site-skus-include.md)]

* Srovnávací test agregované propustnosti je založen na měření více tunelů agregovaných prostřednictvím jedné brány. Není zaručenou propustnost kvůli internetové přenosy podmínky a chování vaší aplikace.
* Informace o cenách najdete na stránce cena 
* Na stránce SLA naleznete informace o dokumentu SLA (smlouvy o úrovni služeb).

>[!NOTE]
>Skladová položka Basic nepodporuje ověřování IKEv2 ani RADIUS.
>

## <a name="configure"></a>Konfigurování připojení P2S

Konfigurace P2S vyžaduje několik určité kroky. V následujících článcích obsahovat postup vás provede procesem konfigurace P2S a odkazy na konfiguraci klientských zařízení sítě VPN:

* [Nakonfigurujte připojení P2S - ověřování pomocí protokolu RADIUS](point-to-site-how-to-radius-ps.md)

* [Nakonfigurujte připojení P2S - ověření Azure nativní certifikátu](vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="faqcert"></a>Nejčastější dotazy týkající se ověřování pomocí certifikátu nativní Azure

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faqradius"></a>Nejčastější dotazy týkající se ověřování pomocí protokolu RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Další kroky

* [Nakonfigurujte připojení P2S - ověřování pomocí protokolu RADIUS](point-to-site-how-to-radius-ps.md)

* [Nakonfigurujte připojení P2S - ověření Azure nativní certifikátu](vpn-gateway-howto-point-to-site-rm-ps.md)