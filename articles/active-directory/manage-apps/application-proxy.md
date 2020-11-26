---
title: Vzdálený přístup k místním aplikacím – Azure Proxy aplikací služby AD
description: Proxy aplikace Azure Active Directory zajišťuje zabezpečený vzdálený přístup k místním webovým aplikacím. Po jednotném přihlášení ke službě Azure AD můžou uživatelé přistupovat ke cloudovým i místním aplikacím prostřednictvím externí adresy URL nebo interního portálu aplikací. Například proxy aplikací může poskytovat vzdálený přístup a jednotné přihlašování k aplikacím vzdálené plochy, SharePoint, teams, Tableau, Qlik a obchodním aplikacím (LOB).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 9d620342d53bdae0b3a520000f6d240eb0e28d15
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180588"
---
# <a name="remote-access-to-on-premises-applications-through-azure-ad-application-proxy"></a>Vzdálený přístup k místním aplikacím prostřednictvím Proxy aplikací služby Azure AD

Proxy aplikace Azure Active Directory zajišťuje zabezpečený vzdálený přístup k místním webovým aplikacím. Po jednotném přihlášení ke službě Azure AD můžou uživatelé přistupovat ke cloudovým i místním aplikacím prostřednictvím externí adresy URL nebo interního portálu aplikací. Například proxy aplikací může poskytovat vzdálený přístup a jednotné přihlašování k aplikacím vzdálené plochy, SharePoint, teams, Tableau, Qlik a obchodním aplikacím (LOB).

Proxy aplikací služby Azure AD nabízí:

- **Jednoduché použití**. Uživatelé mají přístup k místním aplikacím stejným způsobem jako přístup Microsoft 365 a dalších aplikací SaaS integrovaných se službou Azure AD. Aby aplikace fungovaly s Proxy aplikací, nemusíte je měnit nebo aktualizovat.

- **Zabezpečení**. Místní aplikace můžou používat ověřovací ovládací prvky Azure a analýzu zabezpečení. Místní aplikace můžou například používat podmíněný přístup a dvoustupňové ověřování. Proxy aplikací nevyžaduje, abyste otevřeli příchozí připojení přes bránu firewall.

- **Nákladově efektivní**. Místní řešení obvykle vyžadují, abyste nastavili a zachovali demilitarizovaná zóny (zóny DMZ), hraniční servery nebo jiné komplexní infrastruktury. Proxy aplikace běží v cloudu, což usnadňuje jejich použití. Pokud chcete použít proxy aplikace, nemusíte měnit síťovou infrastrukturu ani instalovat další zařízení v místním prostředí.

## <a name="what-is-application-proxy"></a>Co je proxy aplikací?
Proxy aplikací je funkce služby Azure AD, která uživatelům umožňuje přístup k místním webovým aplikacím ze vzdáleného klienta. Proxy aplikace zahrnuje službu proxy aplikací, která běží v cloudu, a konektor proxy aplikací, který běží na místním serveru. Služba Azure AD, proxy server aplikace a konektor proxy aplikací společně spolupracují na tom, aby bylo možné bezpečně předat token uživatele ze služby Azure AD do webové aplikace.

Proxy aplikace funguje s:

* Webové aplikace, které pro ověřování používají [integrované ověřování systému Windows](application-proxy-configure-single-sign-on-with-kcd.md)
* Webové aplikace, které používají formulář nebo přístup [na základě hlaviček](./application-proxy-configure-single-sign-on-with-headers.md)
* Webová rozhraní API, která chcete zpřístupnit pro rozšířené aplikace na různých zařízeních
* Aplikace hostované za [Brána vzdálené plochy](application-proxy-integrate-with-remote-desktop-services.md)
* Bohatých klientských aplikací integrovaných do knihovny Microsoft Authentication Library (MSAL)

Proxy aplikace podporuje jednotné přihlašování. Další informace o podporovaných metodách najdete v tématu [Výběr metody jednotného přihlašování](sso-options.md#choosing-a-single-sign-on-method).

Proxy aplikací se doporučuje pro poskytování přístupu vzdálených uživatelů k interním prostředkům. Proxy aplikace nahrazuje nutnost připojení VPN nebo reverzního proxy serveru. Není určený pro interní uživatele v podnikové síti.  Tito uživatelé, kteří zbytečně využívají proxy aplikací, mohou způsobit neočekávané a nežádoucí problémy s výkonem.

## <a name="how-application-proxy-works"></a>Jak funguje proxy aplikace

Následující diagram ukazuje, jak služba Azure AD a proxy aplikací společně poskytují jednotné přihlašování k místním aplikacím.

![Diagram proxy aplikace AzureAD](./media/application-proxy/azureappproxxy.png)

1. Po tom, co uživatel k aplikaci přistoupí prostřednictvím koncového bodu, se uživatel přesměruje na přihlašovací stránku služby Azure AD.
2. Po úspěšném přihlášení pošle služba Azure AD token do klientského zařízení uživatele.
3. Klient odešle token službě proxy aplikací, která načte hlavní název uživatele (UPN) a hlavní název zabezpečení (SPN) z tokenu. Proxy aplikace pak odešle požadavek do konektoru proxy aplikací.
4. Pokud jste nakonfigurovali jednotné přihlašování, konektor provede jakékoli další ověření vyžadované jménem uživatele.
5. Konektor odešle požadavek do místní aplikace.
6. Odpověď je odeslána prostřednictvím konektoru a služby proxy aplikací k uživateli.

> [!NOTE]
> Stejně jako u většiny hybridních agentů Azure AD konektor proxy aplikací nevyžaduje, abyste v bráně firewall otevírali příchozí připojení. Provoz uživatele v kroku 3 skončí na službě proxy aplikací (v Azure AD). Konektor proxy aplikací (místní) zodpovídá za zbytek komunikace.
>


| Komponenta | Popis |
| --------- | ----------- |
| Koncový bod  | Koncový bod je adresa URL nebo [portál pro koncové uživatele](end-user-experiences.md). Uživatelé můžou kontaktovat aplikace i mimo vaši síť přístupem k externí adrese URL. Uživatelé v rámci vaší sítě mají přístup k aplikaci prostřednictvím adresy URL nebo portálu pro koncové uživatele. Když uživatelé přejdou do některého z těchto koncových bodů, ověřují se ve službě Azure AD a potom jsou směrováni přes konektor do místní aplikace.|
| Azure AD | Azure AD provádí ověřování pomocí adresáře tenanta, který je uložený v cloudu. |
| Služba proxy aplikací | Tato služba proxy aplikací běží v cloudu jako součást služby Azure AD. Předá přihlašovací token od uživatele k konektoru proxy aplikací. Proxy aplikace přepošle všechny dostupné hlavičky na žádosti a nastaví hlavičky podle jejího protokolu na IP adresu klienta. Pokud příchozí požadavek na proxy již má tuto hlavičku, adresa IP klienta se přidá na konec seznamu odděleného čárkami, který je hodnotou záhlaví.|
| Konektor proxy aplikací | Konektor je jednoduchý agent, který běží na Windows serveru ve vaší síti. Konektor spravuje komunikaci mezi službou proxy aplikací v cloudu a místní aplikací. Konektor používá pouze odchozí připojení, takže nemusíte otevírat žádné příchozí porty ani nic vkládat do DMZ. V případě potřeby jsou konektory bezstavové a nabízené informace z cloudu. Další informace o konektorech, jako je například vyrovnávání zatížení a ověřování, najdete v tématu [vysvětlení konektorů Azure proxy aplikací služby AD](application-proxy-connectors.md).|
| Služba Active Directory (AD) | Služba Active Directory pracuje místně a provádí ověřování pro doménové účty. Pokud je nakonfigurováno jednotné přihlašování, konektor komunikuje se službou AD za účelem provedení dalšího vyžadovaného ověřování.
| Místní aplikace | Nakonec může uživatel získat přístup k místní aplikaci.

## <a name="next-steps"></a>Další kroky
Pokud chcete začít používat proxy aplikace, přečtěte si téma [kurz: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikací](application-proxy-add-on-premises-application.md).