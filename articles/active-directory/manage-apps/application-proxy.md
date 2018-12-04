---
title: Jak poskytnout zabezpečený vzdálený přístup k místním aplikacím
description: Popisuje, jak poskytnout zabezpečený vzdálený přístup k místním aplikacím pomocí Azure AD Application Proxy.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 212628c0ec97524e91ab8eaeb766c3e405023aaf
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52846160"
---
# <a name="how-to-provide-secure-remote-access-to-on-premises-applications"></a>Jak poskytnout zabezpečený vzdálený přístup k místním aplikacím

Dnešní zaměstnanci chtějí být produktivní, ať jsou kdekoli, a to neustále a na jakémkoli zařízení. Chtějí pracovat na vlastních zařízeních, ať jde tabletech, telefonech nebo přenosné počítače. Navíc očekávají, bude mít přístup k jejich aplikací, i aplikace SaaS v cloudu a podnikové aplikace v místním. Poskytnutí přístupu k místním aplikacím tradičně zahrnovalo virtuální privátní sítě (VPN) nebo demilitarizované zóny (DMZ). Tato řešení jsou nejen složitá a obtížně zabezpečitelná, ale jsou také nákladná na nastavení a správu.

Existuje však lepší způsob.

Moderní pracovníci v prvním mobile světě upřednostňujícím musí řešení moderních vzdáleného přístupu. Azure AD Application Proxy je funkce služby Azure Active Directory, která nabízí vzdáleného přístupu jako služba. To znamená, že je snadno nasazovat a spravovat.

[!INCLUDE [identity](../../../includes/azure-ad-licenses.md)]

## <a name="what-is-azure-active-directory-application-proxy"></a>Co je Azure Active Directory Application Proxy?
Proxy aplikací Azure AD poskytuje jednotné přihlašování (SSO) a zabezpečený vzdálený přístup pro webové aplikace hostované místně. Některé aplikace, které chcete publikovat zahrnují weby, Outlook Web Access nebo jakékoli jiné obchodní webové aplikace, ke kterým máte služby SharePoint. Tyto místní webové aplikace jsou integrované s Azure AD, stejnou identitu a platforma, která používá O365. Koncoví uživatelé můžou používat u místních aplikací stejným způsobem jako aplikace O365 a další aplikace SaaS integrované s Azure AD. Nemusíte měnit síťovou infrastrukturu nebo vyžadovat VPN k poskytnutí toto řešení pro vaše uživatele.

## <a name="why-is-application-proxy-a-better-solution"></a>Proxy aplikací Proč je lepší řešení?
Proxy aplikací Azure AD poskytuje řešení jednoduchý, bezpečný a nákladově efektivní vzdálený přístup ke všem vašim místním aplikacím.

Proxy aplikací Azure AD je:

* **Jednoduché**
   * Není potřeba změnit nebo aktualizovat vaše aplikace pro práci s Proxy aplikací. 
   * Uživatelé získají konzistentní přihlašování. Chcete-li získat jednotné přihlašování pro SaaS aplikace v cloudu a vaše aplikace v místním použitím portálu MyApps. 
* **Zabezpečení**
   * Při publikování aplikací pomocí Azure AD Application Proxy můžete využít výhodu mnoha prvků pro ověřování a analýzy zabezpečení v Azure. Získáte zabezpečení cloudových a funkce zabezpečení Azure, jako je podmíněný přístup a dvoustupňové ověření.
   * Nemusíte otevřít žádná příchozí připojení přes bránu firewall, aby vaši uživatelé umožňují vzdálený přístup. 
* **Nákladově efektivní**
   * Proxy aplikací funguje v cloudu, takže vám šetří čas a peníze. Místní řešení obvykle vyžadují, abyste nastavili a spravovali zóny DMZ, hraniční servery nebo jiné komplexní infrastruktury.  

## <a name="what-kind-of-applications-work-with-application-proxy"></a>Jaký druh fungování aplikací pomocí Proxy aplikace?
S Azure AD Application Proxy můžete dostat různé druhy interních aplikací:

* Webové aplikace, které používají [integrované ověřování Windows](application-proxy-configure-single-sign-on-with-kcd.md) pro ověřování  
* Webové aplikace, které používají založené na formulářích nebo [založeným na hlavičkách](application-proxy-configure-single-sign-on-with-ping-access.md) přístup  
* Webové rozhraní API, která chcete k tomu, aby bohaté aplikace na různých zařízeních  
* Aplikací hostovaných za službou [Brána vzdálené plochy](application-proxy-integrate-with-remote-desktop-services.md)  
* Bohaté klientských aplikací, které jsou integrovány s Active Directory Authentication Library (ADAL)

## <a name="how-does-application-proxy-work"></a>Jak funguje Proxy aplikací?
Existují dvě součásti, které je nutné nakonfigurovat, aby se Proxy aplikací fungovat: konektor a externí koncový bod. 

Konektor je zjednodušené agent, který je umístěný na serveru systému Windows ve vaší síti. Tento konektor usnadňuje tok přenosů z službu Proxy aplikací v cloudu pro vaše aplikace místní. Využívá jenom odchozí připojení, tak nemusíte otevírat žádné příchozí porty ani umisťovat cokoliv hraniční sítě. Konektory jsou bezstavové a aktivního získávání informací z cloudu podle potřeby. Další informace o konektorech, jako je způsob jejich – nástroj pro vyrovnávání zatížení a ověření naleznete v tématu [pochopit Azure AD Application Proxy konektory](application-proxy-connectors.md). 

Externí koncový bod je, jak vaši uživatelé kontaktovat vaše aplikace během mimo síť. Buď můžete přejít přímo na externí adresu URL, která určíte, nebo jejich přístup k aplikaci prostřednictvím portálu MyApps. Uživatelé přejít na jednu z těchto koncových bodů, ověřování ve službě Azure AD a pak se směrují prostřednictvím konektoru pro místní aplikace.

 ![Diagram Proxy aplikací Azure AD](./media/application-proxy/azureappproxxy.png)

1. Uživatel má přístup k aplikaci prostřednictvím Proxy aplikací služby a je přesměruje na přihlašovací stránku Azure AD k ověření.
2. Token po úspěšně přihlášení, je generována a odeslání do klientského zařízení.
3. Klient odešle token do služby Proxy aplikace, která načte hlavní název uživatele (UPN) a název objektu zabezpečení (SPN) z tokenu, pak přesměruje požadavek na konektor Proxy aplikací.
4. Pokud jste nakonfigurovali jednotného přihlašování, konektor provádí další ověřování vyžaduje jménem uživatele.
5. Konektor odešle požadavek na místní aplikace.  
6. Odpověď se odesílá prostřednictvím Proxy aplikací služby a konektor pro uživatele.

### <a name="single-sign-on"></a>Jednotné přihlašování
Proxy aplikací Azure AD poskytuje jednotné přihlašování (SSO) aplikace, které používají integrované ověřování Windows (IWA) nebo s deklaracemi identity aplikace. Pokud vaše aplikace používá IWA, Proxy aplikací zosobňuje uživatele pomocí omezeného delegování protokolu Kerberos k zajištění jednotného přihlašování. Pokud máte aplikace s deklaracemi identity, která vztahy důvěryhodnosti služby Azure Active Directory, jednotné přihlašování funguje, protože již byl uživatel ověřený službou Azure AD.

Další informace o protokolu Kerberos najdete v tématu [všechny potřebujete vědět o Kerberos omezené delegování (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

### <a name="managing-apps"></a>Správa aplikací
Po publikování aplikace pomocí Proxy aplikace, můžete ji spravovat jako jakoukoli jinou aplikaci enterprise na webu Azure Portal. Můžete používat funkce zabezpečení Azure Active Directory jako podmíněný přístup a dvoustupňové ověření, řídit oprávnění a přizpůsobit branding pro vaši aplikaci. 

## <a name="get-started"></a>Začínáme

Než začnete konfigurovat Proxy aplikací, ujistěte se, že máte podporovanou [edice Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) a adresář služby Azure AD, u kterého jste globálním správcem.

Začínáme s Proxy aplikace ve dvou krocích:

1. [Proxy aplikací zapnout a nakonfigurovat konektor](application-proxy-enable.md).    
2. [Publikování aplikací](application-proxy-publish-azure-portal.md) – pomocí průvodce rychle a snadno získat vaše místní aplikace publikované a přístupné vzdáleně.

## <a name="whats-next"></a>Co dále?
Když publikujete svoji první aplikaci, existuje mnoho dalších úkonů, které vám pomůžou s Proxy aplikace:

* [Povolení jednoduchého přihlášení](application-proxy-configure-single-sign-on-with-kcd.md)
* [Publikování aplikací s použitím vlastního názvu domény](application-proxy-configure-custom-domain.md)
* [Seznamte se s konektory Proxy aplikací Azure AD](application-proxy-connectors.md)
* [Práce s existující místní Proxy servery](application-proxy-configure-connectors-with-proxy-servers.md) 
* [Nastavit vlastní domovskou stránku](application-proxy-configure-custom-home-page.md)

Nejnovější novinky a aktualizace naleznete na [blogu proxy aplikace](https://blogs.technet.com/b/applicationproxyblog/)

