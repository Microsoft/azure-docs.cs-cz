---
title: Vzdálený přístup k místním aplikacím – Azure Active Directory Application Proxy | Microsoft Docx
description: Proxy aplikací Azure Active Directory poskytuje zabezpečený vzdálený přístup k místním webovým aplikacím. Po jednotné přihlašování do služby Azure AD mohou uživatelé přistupovat cloudové a místní aplikace prostřednictvím externí adresu URL nebo interní aplikace portálu. Proxy aplikace může například poskytnout vzdálený přístup a jednotné přihlašování do vzdálené plochy, SharePoint, Teams, Tableau, Qlik a obchodní (LOB) aplikace.
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barbkess
ms.reviewer: japere
ms.openlocfilehash: 643ef7f9960b2a91c88b4e13fe318748d175e036
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692698"
---
# <a name="remote-access-to-on-premises-applications-through-azure-active-directorys-application-proxy"></a>Vzdálený přístup k místním aplikacím přes Azure Active Directory Application Proxy 

Proxy aplikací Azure Active Directory poskytuje zabezpečený vzdálený přístup k místním webovým aplikacím. Po jednotné přihlašování do služby Azure AD mohou uživatelé přistupovat cloudové a místní aplikace prostřednictvím externí adresu URL nebo interní aplikace portálu. Proxy aplikace může například poskytnout vzdálený přístup a jednotné přihlašování do vzdálené plochy, SharePoint, Teams, Tableau, Qlik a obchodní (LOB) aplikace.

Proxy aplikací Azure AD je:

- **Snadno se používá**. Uživatelé můžou používat vaše místní aplikace stejným způsobem jako aplikace O365 a další aplikace SaaS integrované s Azure AD. Není potřeba změnit nebo aktualizovat vaše aplikace pro práci s Proxy aplikací. 

- **Zabezpečení**. Prvků pro ověřování a analýzy zabezpečení Azure, můžete použít u místních aplikací. Například u místních aplikací můžete použít podmíněný přístup a dvoustupňové ověřování. Proxy aplikací není nutné otevřít příchozí připojení přes bránu firewall.
 
- **Nákladově efektivní**. Místní řešení obvykle vyžadují, abyste nastavili a spravovali demilitarizovaná zóny (zóny DMZ), hraniční servery nebo jiné komplexní infrastruktury. Proxy aplikace běží v cloudu, což umožňuje snadno používá. Pokud chcete použít Proxy aplikace, nemusíte měnit síťovou infrastrukturu nebo instalaci dalších zařízení ve vašem místním prostředí.

## <a name="what-is-application-proxy"></a>Co je Proxy aplikací?
Proxy aplikací je funkce Azure AD, která umožňuje uživatelům přistupovat k místním webovým aplikacím ze vzdáleného klienta. Proxy aplikací zahrnuje službu Proxy aplikací, která běží v cloudu i konektor Proxy aplikací, které běží na místním serveru. Azure AD, Proxy aplikace služby a pracovní konektor Proxy aplikací dohromady a bezpečně předat token přihlašování uživatele ze služby Azure AD do webové aplikace.

Proxy aplikací funguje s:

* Webové aplikace, které používají [integrované ověřování Windows](application-proxy-configure-single-sign-on-with-kcd.md) pro ověřování  
* Webové aplikace, které používají založené na formulářích nebo [založeným na hlavičkách](application-proxy-configure-single-sign-on-with-ping-access.md) přístup  
* Webové rozhraní API, která chcete k tomu, aby bohaté aplikace na různých zařízeních  
* Aplikací hostovaných za službou [Brána vzdálené plochy](application-proxy-integrate-with-remote-desktop-services.md)  
* Bohaté klientských aplikací, které jsou integrovány s Active Directory Authentication Library (ADAL)

Proxy aplikací podporuje jednotné přihlašování. Další informace o podporovaných metod najdete v tématu [volba jedinou metodu přihlašování](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

## <a name="how-application-proxy-works"></a>Jak funguje Proxy aplikací

Následující diagram ukazuje, jak Azure AD a Proxy aplikací společně poskytují jednotné přihlašování k místním aplikacím.

![Diagram Proxy aplikací Azure AD](./media/application-proxy/azureappproxxy.png)

1. Poté, co uživatel má získat přístup k aplikaci přes koncový bod, je uživatel přesměrován na přihlašovací stránku Azure AD. 
2. Azure AD poté, co úspěšném přihlášení se odešle token uživatele klientského zařízení.
3. Klient odešle token do služby Proxy aplikace, která načte hlavní název uživatele (UPN) a název objektu zabezpečení (SPN) z tokenu. Proxy aplikací pak odešle požadavek na konektor Proxy aplikací.
4. Pokud jste nakonfigurovali jednotného přihlašování, konektor provádí další ověřování vyžaduje jménem uživatele.
5. Konektor odešle požadavek na místní aplikace.  
6. Odpověď se odesílá prostřednictvím konektoru a Proxy aplikací služby pro uživatele.

| Komponenta | Popis |
| --------- | ----------- |
| Koncový bod  | Koncový bod je adresa URL nebo [portálu pro koncové uživatele](end-user-experiences.md). Uživatelé mohli spojit aplikace během mimo vaši síť díky přístupu do externí adresu URL. Uživatelé ve vaší síti přístup k aplikaci pomocí adresy URL nebo portálu pro koncové uživatele. Uživatelé přejít na jednu z těchto koncových bodů, ověřování ve službě Azure AD a pak se směrují prostřednictvím konektoru pro místní aplikace.|
| Azure AD | Azure AD provádí ověřování pomocí adresář tenanta uložená v cloudu. |
| Služba Proxy aplikace | Tato služba Proxy aplikace se spouští v cloudu jako součást služby Azure AD. Token přihlášení od uživatele předá do konektoru Proxy aplikace. Proxy aplikace předá jakékoli přístupné hlavičky v požadavku a nastaví záhlaví podle jeho protokol IP adresu klienta. Pokud je příchozí požadavek na server proxy už tuto hlavičku, IP adresa klienta se přidá na konec seznamu oddělených čárkami, který je hodnota hlavičky.|
| Konektor Proxy aplikace | Konektor je zjednodušené agent, který běží na Windows serveru ve vaší síti. Konektor skladuje komunikaci mezi službou Proxy aplikací v cloudu a místních aplikací. Konektor využívá jenom odchozí připojení, proto není nutné otevírat žádné příchozí porty ani umisťovat cokoliv hraniční sítě. Konektory jsou bezstavové a aktivního získávání informací z cloudu podle potřeby. Další informace o konektorech, jako je způsob jejich – nástroj pro vyrovnávání zatížení a ověření naleznete v tématu [pochopit Azure AD Application Proxy konektory](application-proxy-connectors.md).|
| Active Directory (AD) | Služba Active Directory spustí v místním provádět ověřování pro doménové účty. Když jednotného přihlašování je nakonfigurován, konektoru komunikuje se službou AD provádět žádné další ověření vyžaduje.
| Místní aplikace | Uživatel je nakonec mít přístup k místní aplikaci. 

## <a name="next-steps"></a>Další postup
Pokud chcete začít používat Proxy aplikací, najdete v článku [kurzu: Přidat místní aplikace pro vzdálený přístup prostřednictvím Proxy aplikací](application-proxy-add-on-premises-application.md). 

Nejnovější novinky a aktualizace, najdete v článku [blogu Proxy aplikace](https://blogs.technet.com/b/applicationproxyblog/)


