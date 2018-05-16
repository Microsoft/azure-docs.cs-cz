---
title: Postup při integraci s Azure Active Directory | Microsoft Docs
description: Průvodce výhody a prostředky pro integraci s Azure Active Directory.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: d13bba54-96bd-4b81-bee9-c8025ffa1648
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: celested
ms.reviewer: bryanla
ms.custom: aaddev
ms.openlocfilehash: be7dec07597b0a82633d330a72274a94dbb9bf67
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="integrating-with-azure-active-directory"></a>Integrace se službou Azure Active Directory
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory poskytuje organizacím podnikové úrovni Správa identit pro cloudové aplikace. Integrace se službou Azure AD poskytuje uživatelům moderní prostředí přihlášení a pomáhá vaší aplikace v souladu s zásady IT.

## <a name="how-to-integrate"></a>Postup pro integraci
Existuje několik způsobů pro vaši aplikaci pro integraci s Azure AD. Využijte jako mnoho nebo několika z těchto scénářů, jako je vhodné pro vaši aplikaci.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Podpora Azure AD jako způsob přihlášení do aplikace
**Snižte přihlášení třením a snížit náklady na podporu.** Pomocí Azure AD pro přihlášení k aplikaci vaši uživatelé nebudou mít jeden další název a heslo pamatovat. Jako vývojář budete mít méně jedno heslo k ukládání a chránit. Nemusí zpracovávat zapomenuté heslo resetovat, může být důležité ušetříte samostatně. Azure AD poskytuje přihlášení pro některé na světě nejoblíbenější cloudových aplikací, včetně služeb Office 365 a Microsoft Azure. S stovky milionů uživatelů z milionů organizací, je možné, se uživatel už přihlásili k Azure AD. Další informace o [přidání podpory pro Azure AD přihlášení](active-directory-authentication-scenarios.md).

**Zjednodušení přihlašování nahoru pro vaši aplikaci.**  Během registrace pro vaši aplikaci Azure AD můžete odeslat základní informace o uživateli můžete předem vyplnit vaše přihlašovací formulář nebo zcela eliminuje. Uživatelé mohou zaregistrovat pro vaši aplikaci pomocí svého účtu Azure AD prostřednictvím známých souhlasu prostředí, který je podobné těm, které jsou v sociálních a mobilních aplikací. Každý uživatel, můžete si zaregistrovat a přihlásit k aplikaci, která je integrovaná s Azure AD bez nutnosti IT zapojení. Další informace o [zaregistrovali aplikaci pro účet Azure AD přihlášení](../../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Vyhledejte uživatele, spravovat zřizování uživatelů a řízení přístupu k aplikaci
**Procházením vyhledejte uživatele v adresáři.**  Pomocí rozhraní Graph API pomoci uživatelům vyhledávání a procházení jiní lidé ve své organizaci při vyzvání jiných uživatelů nebo k udělení přístupu, tedy nutné zadejte e-mailové adresy. Uživatelé mohou procházet pomocí známých adres kniha styl rozhraní, včetně zobrazení podrobností o hierarchie v organizaci. Další informace o [rozhraní Graph API](active-directory-graph-api.md).

**Znovu použijte skupiny služby Active Directory a distribuční seznamy, které už spravuje vašich zákazníků.**  Azure AD obsahuje skupiny už pomocí pro distribuci e-mailu a správa přístupu vašich zákazníků. Tyto skupiny, místo aby zákazník k vytváření a správě samostatnou sadu skupin v aplikaci pomocí rozhraní Graph API, znovu použijte. Informace o skupině můžete odesílá i do vaší aplikace v přihlášení tokeny. Další informace o [rozhraní Graph API](active-directory-graph-api.md).

**Pomocí Azure AD určí, kdo má přístup k vaší aplikaci.**  Správci a vlastníci aplikace ve službě Azure AD můžete přiřadit přístup k aplikacím u konkrétních uživatelů a skupin. Pomocí rozhraní Graph API, můžete tento seznam číst a používat k řízení zřizování a jeho rušení prostředků a přístup v rámci vaší aplikace.

**Řízení přístupu na základě rolí pomocí Azure AD.**  Správci a vlastníci aplikace můžete přiřadit uživatele a skupiny s rolemi, které definujete při registraci aplikace ve službě Azure AD. Informace o rolích je odeslán do vaší aplikace v přihlášení tokeny a můžete také načíst pomocí rozhraní Graph API. Další informace o [pomocí služby Azure AD pro autorizaci](http://blogs.technet.com/b/ad/archive/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles.aspx).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Získat přístup k profilu uživatele, kalendář, e-mailu, kontakty, soubory a další
**Azure AD je serveru ověřování pro Office 365 a jiné obchodní služby společnosti Microsoft.**  Pokud podporujete Azure AD pro přihlášení k aplikaci nebo podporu propojení vaše aktuální uživatelské účty s Azure AD uživatelské účty pomocí OAuth 2.0, může požádat o oprávnění ke čtení a zápisu do uživatelského profilu, kalendář, e-mailu, kontakty, soubory a další informace. Můžete bez problémů zapsat události do kalendáře uživatele a číst nebo zapisovat soubory na OneDrive. Další informace o [přístup k rozhraní API Office 365](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Zvýšení úrovně svoji aplikaci v Azure a Office 365 Tržiště
**Umožňuje zvýšit úroveň vaší aplikace na miliony organizacemi, které už používají Azure AD.**  Uživatelé, kteří vyhledávání a procházení tyto tržišť už používají jeden nebo více cloudových služeb, což je kvalifikovaný Zákazníci využívající službu cloudu. Další informace o povýšení svoji aplikaci v [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Když se uživatelé přihlásí k aplikaci, zobrazí se v jejich přístupový panel Azure AD a Spouštěč aplikace Office 365.**  Uživatelé budou moci rychle a snadno vrátit do vaší aplikace později, vylepšení zapojení uživatelů. Další informace o [přístupový panel Azure AD](../active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Zabezpečené komunikace služby a zařízení a Service-to-Service
**Pomocí služby Azure AD pro správu identit zařízení a služeb snižuje kód, který budete muset napsat a dává oddělení IT spravovat přístup.**  Umožňuje získat tokeny z Azure AD pomocí OAuth a používat tyto tokeny pro přístup k webové rozhraní API služby a zařízení. Pomocí služby Azure AD se můžete vyhnout zápis komplexní ověřovacího kódu. Vzhledem k tomu, že identit zařízení a služby jsou uložené ve službě Azure AD, IT může spravovat klíče a odvolání na jednom místě, aniž by museli provést samostatně ve vaší aplikaci.

## <a name="benefits-of-integration"></a>Výhody integrace
Integrace s Azure AD se dodává s výhody, které nevyžadují napsat další kód.

### <a name="integration-with-enterprise-identity-management"></a>Integrace s Enterprise Identity Management
**Nápověda aplikace v souladu se zásadami IT.**  Organizace integrovat Azure AD, jejich systémů správy identit enterprise, když uživatel opustí organizaci, budou automaticky ztratíte přístup k vaší aplikaci bez nutnosti udělat dodatečné kroky, IT. IT můžete spravovat, kdo může získat přístup k vaší aplikaci a určí, jaké zásady přístupu je potřeba – příklad službu Multi-Factor Authentication - snižuje vašim potřebám napsat kód pro dosažení souladu s komplexní podnikové zásady. Azure AD poskytuje správcům podrobné auditování protokolu kdo přihlášení k aplikaci tak IT můžete sledovat využití.

**Azure AD rozšiřuje služby Active Directory do cloudu, tak, aby vaše aplikace může integrovat AD.**  Mnoho organizací po celém světě použít služby Active Directory jako jejich hlavní přihlášení a systém správy identit a potřebují své aplikace pro práci s AD. Aplikace integraci s Azure AD integruje se službou Active Directory.

### <a name="advanced-security-features"></a>Funkce Rozšířené zabezpečení
**Služba Multi-Factor authentication.**  Azure AD, poskytuje nativní aplikace Multi-Factor authentication. Správci IT mohou vyžadovat vícefaktorové ověřování pro přístup k vaší aplikaci, tak, že nemáte code tato podpora sami. Další informace o [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Neobvyklé přihlašovací při zjišťování.**  Azure AD zpracovává více než miliardu přihlášení denně, při použití algoritmy strojového učení detekovat podezřelé aktivity a upozornit správci IT možných problémů. Díky podpoře přihlášení k Azure AD, aplikace získá Výhodou této ochrany. Další informace o [zobrazení sestav Azure Active Directory přístup](../active-directory-view-access-usage-reports.md).

**Podmíněný přístup.**  Kromě služby Multi-Factor authentication, můžou správci vyžadují konkrétních podmínek splnit, než uživatelé můžou přihlásit k vaší aplikaci. Podmínky, které lze nastavit zahrnují rozsah IP adres klientských zařízení, členství v zadaných skupinách a stav zařízení používá pro přístup. Další informace o [podmíněného přístupu Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Snadný vývoj
**Standardních protokolů.**  Společnost Microsoft se zavazuje podpora oborových standardů. Azure AD podporuje SAML 2.0, OpenID Connect 1.0, OAuth 2.0 a WS-Federation 1.2 ověřovací protokoly. Rozhraní Graph API je OData 4.0 kompatibilní. Pokud už vaše aplikace podporuje protokoly SAML 2.0 nebo OpenID Connect 1.0 pro federované přihlašování, může být přehledné přidání podpory pro Azure AD. Další informace o [podporované protokoly pro ověřování Azure AD](active-directory-authentication-protocols.md).

**Opensourcové knihovny.**  Společnost Microsoft poskytuje plně podporované opensourcové knihovny pro oblíbené jazyky a platformy urychlit vývoj. Zdrojový kód je licencováno v rámci Apache 2.0 a můžete rozvětvovat a přispívat zpět do projektů. Další informace o [knihovny ověřování služby Azure AD](active-directory-authentication-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Po celém světě přítomnosti a vysokou dostupnost
**Azure AD je nasazena v datových centrech po celém světě a je spravovat a sledovat po celý den.**  Azure AD je systém správy identit pro Microsoft Azure a Office 365 a je nasazena v 28 datových centrech po celém světě. Data adresáře záruku, že na replikovat aspoň tři datových centrech. Nástroje pro vyrovnávání zatížení globální zkontrolujte přístup uživatelů nejbližší kopii Azure AD obsahující data a automaticky znovu směrovat požadavky na jiných datových centrech v případě zjištění problému.

## <a name="next-steps"></a>Další kroky
[Začněte psát kód](active-directory-developers-guide.md#get-started).

[Přihlášení uživatelů pomocí služby Azure AD](active-directory-authentication-scenarios.md)

