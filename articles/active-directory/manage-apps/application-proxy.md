---
title: Vzdálený přístup k místním aplikacím – proxy aplikací Azure AD
description: Proxy aplikace služby Azure Active Directory poskytuje zabezpečený vzdálený přístup k místním webovým aplikacím. Po jednotném přihlášení k Azure AD mají uživatelé přístup ke cloudovým i místním aplikacím prostřednictvím externí adresy URL nebo interního portálu aplikace. Proxy aplikace může například poskytovat vzdálený přístup a jednotné přihlašování k aplikacím vzdálené plochy, sharepointu, teams, tableau, qlik a oboru podnikání (LOB).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274840"
---
# <a name="remote-access-to-on-premises-applications-through-azure-active-directorys-application-proxy"></a>Vzdálený přístup k místním aplikacím prostřednictvím aplikačního proxy služby Azure Active Directory 

Proxy aplikace služby Azure Active Directory poskytuje zabezpečený vzdálený přístup k místním webovým aplikacím. Po jednotném přihlášení k Azure AD mají uživatelé přístup ke cloudovým i místním aplikacím prostřednictvím externí adresy URL nebo interního portálu aplikace. Proxy aplikace může například poskytovat vzdálený přístup a jednotné přihlašování k aplikacím vzdálené plochy, sharepointu, teams, tableau, qlik a oboru podnikání (LOB).

Proxy aplikací služby Azure AD nabízí:

- **Jednoduché použití**. Uživatelé mají přístup k místním aplikacím stejným způsobem jako při přístupu k O365 a dalším aplikacím SaaS integrovaným do Azure AD. Aby aplikace fungovaly s Proxy aplikací, nemusíte je měnit nebo aktualizovat. 

- **Zabezpečte**. Místní aplikace můžou používat ovládací prvky autorizace Azure a analýzy zabezpečení. Například místní aplikace můžete použít podmíněný přístup a dvoustupňové ověření. Proxy aplikace nevyžaduje otevření příchozích připojení přes bránu firewall.
 
- **Nákladově efektivní**. Místní řešení obvykle vyžadují nastavení a údržbu demilitarizovaných zón (DMZ), hraničních serverů nebo jiných složitých infrastruktur. Proxy aplikace běží v cloudu, což usnadňuje použití. Chcete-li používat proxy aplikace, nemusíte měnit síťovou infrastrukturu nebo instalovat další zařízení v místním prostředí.

## <a name="what-is-application-proxy"></a>Co je proxy aplikací?
Proxy aplikace je funkce Azure AD, která umožňuje uživatelům přístup k místním webovým aplikacím ze vzdáleného klienta. Proxy aplikace zahrnuje jak službu Proxy aplikace, která běží v cloudu, tak konektor Proxy aplikace, který běží na místním serveru. Azure AD, služba Proxy aplikace a konektor proxy aplikace spolupracují na bezpečném předání tokenu pro přihlášení uživatele z Azure AD do webové aplikace.

Proxy aplikace pracuje s:

* Webové aplikace, které pro ověřování používají [integrované ověřování systému Windows](application-proxy-configure-single-sign-on-with-kcd.md)  
* Webové aplikace, které používají přístup založený na formulářích nebo [záhlaví](application-proxy-configure-single-sign-on-with-ping-access.md)  
* Webová api, která chcete vystavit bohatým aplikacím na různých zařízeních  
* Aplikace hostované za [bránou vzdálené plochy](application-proxy-integrate-with-remote-desktop-services.md)  
* Rozšířené klientské aplikace integrované s knihovnou ADAL (Active Directory Authentication Library)

Proxy aplikace podporuje jednotné přihlašování. Další informace o podporovaných metodách naleznete [v tématu Výběr metody jednotného přihlášení](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

Proxy aplikace se doporučuje pro poskytování přístupu vzdálených uživatelů k interním prostředkům. Proxy aplikace nahrazuje potřebu VPN nebo reverzní proxy. Není určen pro interní uživatele v podnikové síti.  Tito uživatelé, kteří zbytečně používají proxy aplikace může způsobit neočekávané a nežádoucí problémy s výkonem.

## <a name="how-application-proxy-works"></a>Jak funguje proxy aplikace

Následující diagram znázorňuje, jak Azure AD a proxy aplikace spolupracují na poskytování jednotného přihlášení k místním aplikacím.

![Diagram proxy aplikací AzureAD](./media/application-proxy/azureappproxxy.png)

1. Poté, co uživatel má přístup k aplikaci prostřednictvím koncového bodu, uživatel je přesměrován na přihlašovací stránku Azure AD. 
2. Po úspěšném přihlášení Azure AD odešle token do klientského zařízení uživatele.
3. Klient odešle token službě Proxy aplikace, která z tokenu načte hlavní název uživatele (UPN) a hlavní název zabezpečení (SPN). Proxy aplikace pak odešle požadavek na konektor proxy aplikace.
4. Pokud jste nakonfigurovali jednotné přihlašování, konektor provede jakékoli další ověřování požadované jménem uživatele.
5. Konektor odešle požadavek do místní aplikace.  
6. Odpověď je odeslána prostřednictvím konektoru a služby Proxy aplikace uživateli.

| Komponenta | Popis |
| --------- | ----------- |
| Koncový bod  | Koncový bod je adresa URL nebo [portál koncového uživatele](end-user-experiences.md). Uživatelé mohou přistupovat k aplikacím mimo vaši síť přístupem k externí adrese URL. Uživatelé v rámci vaší sítě mohou přistupovat k aplikaci prostřednictvím adresy URL nebo portálu koncového uživatele. Když uživatelé přejdou na jeden z těchto koncových bodů, ověří se ve službě Azure AD a pak jsou směrovány přes konektor do místní aplikace.|
| Azure AD | Azure AD provádí ověřování pomocí adresáře klienta uloženého v cloudu. |
| Služba Proxy aplikace | Tato služba Proxy aplikace běží v cloudu jako součást Azure AD. Předá přihlašovací token od uživatele konektoru proxy aplikace. Proxy aplikace předá všechny přístupné hlavičky na požadavek a nastaví záhlaví podle jeho protokolu, na IP adresu klienta. Pokud příchozí požadavek na proxy server již tuto hlavičku má, je adresa IP klienta přidána na konec seznamu odděleného čárkou, který je hodnotou hlavičky.|
| Konektor proxy aplikace | Konektor je lehký agent, který běží na windows serveru uvnitř sítě. Konektor spravuje komunikaci mezi službou Proxy aplikace v cloudu a místní aplikací. Konektor používá pouze odchozí připojení, takže není třeba otevírat žádné příchozí porty nebo dát nic v DMZ. Konektory jsou bezstavové a vyžádat informace z cloudu podle potřeby. Další informace o konektory, jako je například jejich vyrovnávání zatížení a ověřování, najdete [v tématu Principy konektorů proxy aplikací Azure AD](application-proxy-connectors.md).|
| Služba Active Directory (AD) | Služba Active Directory je spuštěna místně a provádí ověřování pro účty domény. Při konfiguraci jednotného přihlášení konektor komunikuje se službou AD a provádí další požadované ověřování.
| Místní aplikace | Nakonec má uživatel přístup k místní aplikaci. 

## <a name="next-steps"></a>Další kroky
Informace o spuštění proxy aplikace naleznete v [tématu Kurz: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace](application-proxy-add-on-premises-application.md). 

Nejnovější zprávy a aktualizace najdete v [blogu Proxy aplikace](https://blogs.technet.com/b/applicationproxyblog/)


