---
title: Jak poskytnout zabezpečený vzdálený přístup k místní aplikace
description: Popisuje, jak poskytnout zabezpečený vzdálený přístup k místním aplikacím pomocí proxy aplikace služby Azure AD.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: bb71bd0bcaae8d7f9cb3ae357e726ea1f1c00b07
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="how-to-provide-secure-remote-access-to-on-premises-applications"></a>Jak poskytnout zabezpečený vzdálený přístup k místním aplikacím

Zaměstnanci chtějí dnes zajistit produktivitu na všech místě, kdykoli a z jakéhokoli zařízení. Chtějí pracovat na vlastních zařízeních, ať jde tablety a telefony, přenosné počítače. A se očekává, že budou mít přístup všechny své aplikace, obě aplikace SaaS v cloudu a firemním aplikacím místně. Poskytuje přístup k místním aplikacím má tradičně zahrnuta, virtuálním privátním sítím (VPN) nebo demilitarizovaná zón (zóny DMZ). Nejen jsou tato řešení komplexní a pevné zabezpečit, ale jsou nákladná nastavit a spravovat.

Je lepší způsob!

Moderní pracovních sil v prvním mobilní, cloudové první world musí řešení moderní vzdáleného přístupu. Proxy aplikace služby Azure AD je funkce služby Azure Active Directory, která nabízí vzdáleného přístupu jako služba. To znamená, že je snadné ho nasadit, používat a spravovat.

[!INCLUDE [identity](../../../includes/azure-ad-licenses.md)]

## <a name="what-is-azure-active-directory-application-proxy"></a>Co je Azure Proxy aplikace služby Active Directory?
Proxy aplikace služby Azure AD poskytuje jednotné přihlašování (SSO) a zabezpečený vzdálený přístup pro webové aplikace hostované na místě. Některé aplikace, kterou chcete publikovat patří lokality, Outlook Web Access nebo jiné obchodní webové aplikace, které máte služby SharePoint. Tyto místní webové aplikace jsou integrované s Azure AD, stejnou identitu a řízení platforma, která je používána O365. Koncoví uživatelé získat přístup k aplikacím vaší místní stejným způsobem jako přístupu k O365 a jinými aplikacemi SaaS integrované s Azure AD. Nemusíte měnit síťovou infrastrukturu nebo vyžadovat VPN k zajištění tohoto řešení pro vaše uživatele.

## <a name="why-is-application-proxy-a-better-solution"></a>Proč je Proxy aplikace lepší řešení?
Proxy aplikace služby Azure AD poskytuje řešení jednoduchý, zabezpečenou a nákladově efektivní vzdáleného přístupu na všechny místní aplikace.

Proxy aplikace služby Azure AD je:

* **Jednoduché**
   * Nemusíte změníte nebo aktualizujete aplikace pro práci s Proxy aplikace. 
   * Uživatelé získají konzistentní přihlašování. Použitím MyApps portál k získání jednotného přihlašování pro obě aplikace SaaS v cloudu a aplikace místní. 
* **Zabezpečení**
   * Při publikování aplikací pomocí proxy aplikace služby Azure AD, můžete využít výhod ovládací prvky bohaté autorizace a analýza zabezpečení v Azure. Získáte cloudového škálovatelného zabezpečení a funkcí zabezpečení Azure, jako je podmíněný přístup a dvoustupňové ověření.
   * Nemáte otevřete všechna příchozí připojení přes bránu firewall, aby uživatelům zadat vzdálený přístup. 
* **Nákladově efektivní**
   * Proxy aplikací funguje v cloudu, takže můžete ušetřit čas a peníze. Místní řešení obvykle vyžadují, abyste nastavením a údržbou zóny DMZ, servery edge nebo jiné komplexní infrastruktury.  

## <a name="what-kind-of-applications-work-with-application-proxy"></a>Jaký druh pracovní aplikací pomocí Proxy aplikace?
S Azure AD Application Proxy se můžete dostat různé typy interní aplikace:

* Webové aplikace, které používají [integrované ověřování systému Windows](../active-directory-application-proxy-sso-using-kcd.md) pro ověřování  
* Webové aplikace, které používají založené na formulářích nebo [na základě záhlaví](../application-proxy-ping-access.md) přístup  
* Webové rozhraní API, která chcete vystavit bohaté aplikací na různých zařízeních  
* Aplikace hostované za [Brána vzdálené plochy](../application-proxy-publish-remote-desktop.md)  
* Bohaté klientských aplikací, které jsou integrované s Active Directory Authentication Library (ADAL)

## <a name="how-does-application-proxy-work"></a>Jak funguje Proxy aplikace?
Existují dvě součásti, které je nutné nakonfigurovat, aby Proxy aplikací fungovat: konektoru a externí koncový bod. 

Konektor je lightweight agenta, která se nachází na serveru Windows uvnitř vaší sítě. Konektor usnadňuje tok přenosů ze služby Proxy aplikace v cloudu pro aplikaci místní. Pouze používá odchozí připojení, takže nemusíte otevírat žádné příchozí porty, nebo nic uveden v hraniční síti. Konektory jsou bezstavové a načítat informace z cloudu podle potřeby. Další informace o konektory, podobně jako postupy jejich vyrovnávání zatížení a ověření, najdete v části [pochopit Azure AD Application Proxy konektory](../application-proxy-understand-connectors.md). 

Externí koncový bod je, jak uživatelé kontaktovat vaše aplikace při mimo vaši síť. Můžete buď přejít přímo na externí adresu URL, která určíte, nebo získají přístup k aplikaci prostřednictvím portálu MyApps. Když uživatelé přejít na jednu z těchto koncových bodů, ověřování ve službě Azure AD a potom jsou směrovány prostřednictvím konektoru pro místní aplikace.

 ![Diagram AzureAD Proxy aplikace](./media/application-proxy/azureappproxxy.png)

1. Uživatel přistupuje k aplikaci prostřednictvím Proxy aplikace služby a je přesměruje na přihlašovací stránku služby Azure AD k ověření.
2. Token je po úspěšného přihlášení, generovány a odeslány do klientského zařízení.
3. Klient odešle token do služby Proxy aplikace, která načte hlavní název uživatele (UPN) a název objektu zabezpečení (SPN) z tokenu a pak přesměruje požadavek na konektor Proxy aplikace.
4. Pokud jste nakonfigurovali jednotné přihlašování, provede konektor žádné další ověřování vyžaduje jménem uživatele.
5. Konektor odešle požadavek na lokální aplikace.  
6. Odpovědi se budou odesílat prostřednictvím Proxy aplikace služby a konektor pro uživatele.

### <a name="single-sign-on"></a>Jednotné přihlašování
Proxy aplikace služby Azure AD poskytuje jednotné přihlašování (SSO) pro aplikace, které používají integrované ověřování systému Windows (IWA) nebo deklaracemi identity aplikace. Pokud vaše aplikace používá integrované ověřování systému Windows, Proxy aplikace zosobňuje uživatele s využitím omezené delegování Kerberos zajištění jednotného přihlašování. Pokud máte deklaracemi identity aplikace, která vztahy důvěryhodnosti služby Azure Active Directory, jednotné přihlašování funguje, protože již byl uživatel ověřený službou Azure AD.

Další informace o protokolu Kerberos najdete v tématu [všechny budete chtít vědět o použitím protokolu Kerberos omezené delegování (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

### <a name="managing-apps"></a>Správa aplikací
Jakmile aplikace publikována pomocí Proxy aplikace, můžete jej spravovat stejně jako jiná podnikové aplikace na portálu Azure. Můžete použít funkce zabezpečení Azure Active Directory, jako je podmíněný přístup a dvoustupňové ověření, řídit oprávnění uživatele a přizpůsobit branding pro vaši aplikaci. 

## <a name="get-started"></a>Začínáme

Než začnete konfigurovat Proxy aplikace, zajistěte, aby byla podporována [edice služby Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) a adresář Azure AD, pro který jste globálním správcem.

Začínáme s Proxy aplikace ve dvou krocích:

1. [Povolení Proxy aplikace a nakonfigurovat konektor](../active-directory-application-proxy-enable.md).    
2. [Publikování aplikací](../application-proxy-publish-azure-portal.md) – pomocí Průvodce rychlé a snadné získat místní aplikace publikována a dostupné vzdáleně.

## <a name="whats-next"></a>Co dále?
Jakmile publikujete první aplikace, existuje mnoho dalších úkonů, které můžete provést pomocí Proxy aplikace:

* [Povolení jednoduchého přihlášení](../active-directory-application-proxy-sso-using-kcd.md)
* [Publikování aplikací s použitím vlastního názvu domény](../active-directory-application-proxy-custom-domains.md)
* [Další informace o Azure AD Application Proxy konektory](../application-proxy-understand-connectors.md)
* [Práce s existující místní Proxy servery](../application-proxy-working-with-proxy-servers.md) 
* [Nastavit vlastní domovskou stránku](../application-proxy-office365-app-launcher.md)

Nejnovější novinky a aktualizace naleznete na [blogu proxy aplikace](http://blogs.technet.com/b/applicationproxyblog/)

