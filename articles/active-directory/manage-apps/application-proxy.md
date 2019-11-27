---
title: Vzdálený přístup k místním aplikacím – Azure Proxy aplikací služby AD
description: Proxy aplikace Azure Active Directory zajišťuje zabezpečený vzdálený přístup k místním webovým aplikacím. Po jednotném přihlášení ke službě Azure AD můžou uživatelé přistupovat ke cloudovým i místním aplikacím prostřednictvím externí adresy URL nebo interního portálu aplikací. Například proxy aplikací může poskytovat vzdálený přístup a jednotné přihlašování k aplikacím vzdálené plochy, SharePoint, teams, Tableau, Qlik a obchodním aplikacím (LOB).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4780786f0caea2c211b6b93fb0736feaade8de80
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274840"
---
# <a name="remote-access-to-on-premises-applications-through-azure-active-directorys-application-proxy"></a>Vzdálený přístup k místním aplikacím prostřednictvím proxy aplikace Azure Active Directory 

Proxy aplikace Azure Active Directory zajišťuje zabezpečený vzdálený přístup k místním webovým aplikacím. Po jednotném přihlášení ke službě Azure AD můžou uživatelé přistupovat ke cloudovým i místním aplikacím prostřednictvím externí adresy URL nebo interního portálu aplikací. Například proxy aplikací může poskytovat vzdálený přístup a jednotné přihlašování k aplikacím vzdálené plochy, SharePoint, teams, Tableau, Qlik a obchodním aplikacím (LOB).

Proxy aplikací Azure AD je:

- **Jednoduché použití**. Uživatelé mají přístup k místním aplikacím stejným způsobem jako při přístupu do O365 a dalších aplikací SaaS integrovaných se službou Azure AD. Není potřeba změnit nebo aktualizovat vaše aplikace pro práci s Proxy aplikací. 

- **Zabezpečení**. Místní aplikace můžou používat ověřovací ovládací prvky Azure a analýzu zabezpečení. Místní aplikace můžou například používat podmíněný přístup a dvoustupňové ověřování. Proxy aplikací nevyžaduje, abyste otevřeli příchozí připojení přes bránu firewall.
 
- **Nákladově efektivní**. Místní řešení obvykle vyžadují, abyste nastavili a zachovali demilitarizovaná zóny (zóny DMZ), hraniční servery nebo jiné komplexní infrastruktury. Proxy aplikace běží v cloudu, což usnadňuje jejich použití. Pokud chcete použít proxy aplikace, nemusíte měnit síťovou infrastrukturu ani instalovat další zařízení v místním prostředí.

## <a name="what-is-application-proxy"></a>Co je proxy aplikací?
Proxy aplikací je funkce služby Azure AD, která uživatelům umožňuje přístup k místním webovým aplikacím ze vzdáleného klienta. Proxy aplikace zahrnuje službu proxy aplikací, která běží v cloudu, a konektor proxy aplikací, který běží na místním serveru. Služba Azure AD, proxy server aplikace a konektor proxy aplikací společně spolupracují na tom, aby bylo možné bezpečně předat token uživatele ze služby Azure AD do webové aplikace.

Proxy aplikace funguje s:

* Webové aplikace, které pro ověřování používají [integrované ověřování systému Windows](application-proxy-configure-single-sign-on-with-kcd.md)  
* Webové aplikace, které používají formulář nebo přístup [na základě hlaviček](application-proxy-configure-single-sign-on-with-ping-access.md)  
* Webové rozhraní API, která chcete k tomu, aby bohaté aplikace na různých zařízeních  
* Aplikace hostované za [Brána vzdálené plochy](application-proxy-integrate-with-remote-desktop-services.md)  
* Bohaté klientských aplikací, které jsou integrovány s Active Directory Authentication Library (ADAL)

Proxy aplikace podporuje jednotné přihlašování. Další informace o podporovaných metodách najdete v tématu [Výběr metody jednotného přihlašování](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

Proxy aplikací se doporučuje pro poskytování přístupu vzdálených uživatelů k interním prostředkům. Proxy aplikace nahrazuje nutnost připojení VPN nebo reverzního proxy serveru. Není určený pro interní uživatele v podnikové síti.  Tito uživatelé, kteří zbytečně využívají proxy aplikací, mohou způsobit neočekávané a nežádoucí problémy s výkonem.

## <a name="how-application-proxy-works"></a>Jak funguje proxy aplikace

Následující diagram ukazuje, jak služba Azure AD a proxy aplikací společně poskytují jednotné přihlašování k místním aplikacím.

![Diagram Proxy aplikací Azure AD](./media/application-proxy/azureappproxxy.png)

1. Poté, co uživatel má získat přístup k aplikaci přes koncový bod, je uživatel přesměrován na přihlašovací stránku Azure AD. 
2. Po úspěšném přihlášení pošle služba Azure AD token do klientského zařízení uživatele.
3. Klient odešle token službě proxy aplikací, která načte hlavní název uživatele (UPN) a hlavní název zabezpečení (SPN) z tokenu. Proxy aplikace pak odešle požadavek do konektoru proxy aplikací.
4. Pokud jste nakonfigurovali jednotného přihlašování, konektor provádí další ověřování vyžaduje jménem uživatele.
5. Konektor odešle požadavek na místní aplikace.  
6. Odpověď je odeslána prostřednictvím konektoru a služby proxy aplikací k uživateli.

| Součást | Popis |
| --------- | ----------- |
| Koncový bod  | Koncový bod je adresa URL nebo [portál pro koncové uživatele](end-user-experiences.md). Uživatelé mohli spojit aplikace během mimo vaši síť díky přístupu do externí adresu URL. Uživatelé ve vaší síti přístup k aplikaci pomocí adresy URL nebo portálu pro koncové uživatele. Uživatelé přejít na jednu z těchto koncových bodů, ověřování ve službě Azure AD a pak se směrují prostřednictvím konektoru pro místní aplikace.|
| Azure AD | Azure AD provádí ověřování pomocí adresáře tenanta, který je uložený v cloudu. |
| Služba proxy aplikací | Tato služba proxy aplikací běží v cloudu jako součást služby Azure AD. Předá přihlašovací token od uživatele k konektoru proxy aplikací. Proxy aplikace přepošle všechny dostupné hlavičky na žádosti a nastaví hlavičky podle jejího protokolu na IP adresu klienta. Pokud příchozí požadavek na proxy již má tuto hlavičku, adresa IP klienta se přidá na konec seznamu odděleného čárkami, který je hodnotou záhlaví.|
| Konektor proxy aplikací | Konektor je jednoduchý agent, který běží na Windows serveru ve vaší síti. Konektor spravuje komunikaci mezi službou proxy aplikací v cloudu a místní aplikací. Konektor používá pouze odchozí připojení, takže nemusíte otevírat žádné příchozí porty ani nic vkládat do DMZ. Konektory jsou bezstavové a aktivního získávání informací z cloudu podle potřeby. Další informace o konektorech, jako je například vyrovnávání zatížení a ověřování, najdete v tématu [vysvětlení konektorů Azure proxy aplikací služby AD](application-proxy-connectors.md).|
| Služba Active Directory (AD) | Služba Active Directory pracuje místně a provádí ověřování pro doménové účty. Pokud je nakonfigurováno jednotné přihlašování, konektor komunikuje se službou AD za účelem provedení dalšího vyžadovaného ověřování.
| Místní aplikace | Nakonec může uživatel získat přístup k místní aplikaci. 

## <a name="next-steps"></a>Další kroky
Pokud chcete začít používat proxy aplikace, přečtěte si téma [kurz: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikací](application-proxy-add-on-premises-application.md). 

Nejnovější novinky a aktualizace najdete na [blogu proxy aplikací](https://blogs.technet.com/b/applicationproxyblog/) .


