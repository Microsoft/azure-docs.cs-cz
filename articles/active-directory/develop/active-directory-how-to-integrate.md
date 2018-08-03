---
title: Postup při integraci s Azure Active Directory | Dokumentace Microsoftu
description: Příručka k výhody a materiály týkající se integrace s Azure Active Directory.
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
ms.openlocfilehash: 58431e43312046c5a30ebcd4c974849641c05156
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39446450"
---
# <a name="integrating-with-azure-active-directory"></a>Integrace s Azure Active Directory
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory poskytuje organizacím se správou identit na podnikové úrovni pro cloudové aplikace. Integrace se službou Azure AD poskytuje zjednodušené prostředí znak uživatelům a pomáhá zajistit u aplikací v souladu s zásady IT.

## <a name="how-to-integrate"></a>Jak integrovat
Existuje několik způsobů, jak pro vaši aplikaci pro integraci s Azure AD. Využijte výhod tolik nebo jako pár z těchto scénářů, jako je vhodná pro vaši aplikaci.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Podpora služby Azure AD jako způsob, jak se k aplikaci
**Snižte přihlášení třecí plochy a snížit náklady na podporu.** Pomocí služby Azure AD k přihlášení do vaší aplikace, vaši uživatelé nebudou mít jeden další název a heslo a mějte na paměti. Jako vývojář máte jeden menší heslo k ukládání a ochraně. Není potřeba zpracovat resetovat zapomenuté heslo může být významné úspory samostatně. Azure AD poskytuje přihlášení pro některé z celého světa nejoblíbenější cloudové aplikace, včetně Office 365 a Microsoft Azure. Stovky milionů uživatelů z milionům organizací, je pravděpodobné uživatel je už přihlášení do služby Azure AD. Další informace o [přidání podpory pro přihlášení Azure AD](active-directory-authentication-scenarios.md).

**Zjednodušte přihlašování až pro vaši aplikaci.**  Během registrace pro vaši aplikaci můžete služby Azure AD odeslat důležité informace o uživateli, takže můžete předběžně vyplnit registrace formuláře nebo zcela eliminuje. Uživatelé mohou zaregistrovat svoji aplikaci pomocí svého účtu Azure AD prostřednictvím prostředí pro známé vyjádření souhlasu podobné těm v sociálních a mobilních aplikací. Každý uživatel může zaregistrovat a přihlaste se k aplikaci, která je integrovaná s Azure AD bez zapojení IT. Další informace o [registrace vaší aplikace pro přihlášení k účtu Azure AD](../../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Procházet pro uživatele, spravovat zřizování uživatelů a řízení přístupu k aplikaci
**Projděte pro uživatele v adresáři.**  Používání rozhraní Graph API k poskytování pomoci uživatelům vyhledat a procházet jinými lidmi, kteří ve své organizaci při pozvání ostatních nebo udělení přístupu, namísto nutnosti jejich nutnost zadejte e-mailové adresy. Můžou uživatelé procházet pomocí stylu rozhraní známou adresu adresáře, včetně zobrazení podrobností o organizační hierarchie. Další informace o [rozhraní Graph API](active-directory-graph-api.md).

**Znovu použijte skupiny služby Active Directory a distribuční seznamy, které už spravuje vaše zákazníky.**  Azure AD obsahuje skupiny již pomocí e-mailu distribuci a správě přístupu vašich zákazníků. Tyto skupiny nemusíte mít vašich zákazníků můžete vytvořit a spravovat samostatnou sadu skupin ve vaší aplikaci pomocí rozhraní Graph API, znovu použijte. Informace o skupině je také odeslat do vaší aplikace v přihlášení tokeny. Další informace o [rozhraní Graph API](active-directory-graph-api.md).

**Pomocí Azure AD, aby ovládací prvek, který má přístup do vaší aplikace.**  Správci a vlastníci aplikace ve službě Azure AD můžete přiřadit přístup k aplikacím na konkrétní uživatele a skupiny. Pomocí rozhraní Graph API, může číst tento seznam a použít ho k řízení zřizování a zrušení zřizování prostředků a přístup v rámci vaší aplikace.

**Řízení přístupu na základě role pomocí Azure AD.**  Správci a vlastníci aplikace můžete přiřadit uživatele a skupiny k rolím, které můžete definovat při registraci aplikace ve službě Azure AD. Informace o rolích odeslané do vaší aplikace v přihlášení tokeny a může také číst pomocí rozhraní Graph API. Další informace o [pomocí služby Azure AD pro autorizaci](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Získejte přístup k profilu uživatele, kalendář, e-mailu, kontakty, soubory a další
**Azure AD je autorizační server pro Office 365 a dalším firemním službám společnosti Microsoft.**  Pokud podporujete Azure AD pro přihlášení do aplikace nebo podporu propojení aktuální uživatelské účty do uživatelských účtů služby Azure AD pomocí OAuth 2.0, můžete požádat pro čtení a zápisu do profilu uživatele, kalendář, e-mailu, kontakty, soubory a další informace. Může bezproblémově zapsat události do kalendáře uživatele a číst nebo zapisovat soubory do jejich Onedrivu. Další informace o [přístup k rozhraní API Office 365](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Propagace vaší aplikace v Azure a Office 365 tržišť s řešeními
**Propagace vaší aplikace pro miliony organizacemi, které už používáte Azure AD.**  Uživatelé, kteří vyhledat a procházet těchto tržišť už používají jeden nebo více cloudových služeb, díky kterým jsou kvalifikované zákazníci cloudové služby. Další informace o rozběhnutí vaši aplikaci v [na webu Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Když uživatelé registrují do vaší aplikace, zobrazí se v jejich přístupový panel služby Azure AD a Spouštěči aplikací Office 365.**  Uživatelé budou moct rychle a snadno vrátit do aplikace později, vylepšení zapojení uživatelů. Další informace o [přístupový panel služby Azure AD](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Zabezpečená komunikace zařízení služba a služba služba
**Používání služby Azure AD pro správu identit zařízení a služeb snižuje množství kódu, které potřebujete k zápisu a umožňuje IT spravovat přístup.**  Zařízení a služeb můžete získat tokeny z Azure AD pomocí OAuth a použít tyto tokeny pro přístup k webovým rozhraním API. Používání služby Azure AD můžete vyhnout, psaní komplexních ověřovacího kódu. Protože se ukládají identity služeb a zařízení ve službě Azure AD, IT může spravovat klíče a odvolávání na jednom místě namísto toho, aby k tomu samostatně ve vaší aplikaci.

## <a name="benefits-of-integration"></a>Výhody integrace
Integrace se službou Azure AD přináší výhody, které nevyžadují psát další kód.

### <a name="integration-with-enterprise-identity-management"></a>Integrace se správu Identity organizace
**Vaší aplikaci zajistit soulad se zásadami IT pomůžou.**  Organizace integrovat jejich podnikové systémy správy identit s Azure AD, takže když uživatel odejde z organizace, se automaticky ztratí přístup k vaší aplikaci bez služby IT, které by bylo nutné provést další kroky. IT může spravovat, kdo může získat přístup k vaší aplikaci a určí, jaké zásady přístupu je potřeba – třeba ověření službou Multi-Factor Authentication – snižuje potřeba psát kód pro dosažení souladu s komplexní podnikové zásady. Azure AD poskytuje správcům podrobnou auditní protokol kdo přihlášení do vaší aplikace tak IT můžete sledovat využití.

**Azure AD rozšiřuje služby Active Directory do cloudu tak, aby vaše aplikace můžete integrovat s AD.**  Mnoho organizací po celém světě používají službu Active Directory jako jejich hlavní přihlášení a systém správy identit a potřebují své aplikace pro práci s AD. Vaše aplikace integraci se službou Azure AD se integruje s Active Directory.

### <a name="advanced-security-features"></a>Rozšířené funkce zabezpečení
**Ověřování službou Multi-Factor Authentication.**  Azure AD poskytuje nativní služby Multi-Factor authentication. Správci IT můžou vyžadovat vícefaktorové ověřování pro přístup k aplikaci, takže není potřeba tato podpora kódu sami. Další informace o [ověřování službou Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Neobvyklé přihlášení zjišťování.**  Azure AD zpracovává více než miliard přihlášení denně, při použití algoritmů strojového učení ke zjištění podezřelé aktivity a upozornit správce IT o možných problémech. Díky podpoře přihlášení Azure AD, vaše aplikace získá Výhodou této ochrany. Další informace o [zobrazení sestav přístupu Azure Active Directory](../active-directory-view-access-usage-reports.md).

**Podmíněný přístup.**  Kromě ověřování službou Multi-Factor Authentication, můžou správci vyžadují určitých podmínek splnit, předtím než uživatelé můžou přihlásit do vaší aplikace. Podmínky, které je možné nastavit zahrnují rozsah IP adres z klientských zařízení, členství v zadaných skupinách a stav zařízení se používají pro přístup. Další informace o [podmíněného přístupu Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Snadný vývoj
**Standardní protokoly.**  Microsoft je odhodlána podporovat oborové standardy. Azure AD podporuje ověřovací protokoly SAML 2.0, OpenID Connect 1.0, OAuth 2.0 a WS-Federation 1.2. Rozhraní Graph API je kompatibilní s OData 4.0. Pokud už vaše aplikace podporuje protokol SAML 2.0 nebo OpenID Connect 1.0 pro federované přihlašování, může být jednoduchá přidání podpory pro službu Azure AD. Další informace o [podporované protokoly pro ověřování Azure AD](active-directory-authentication-protocols.md).

**Open source knihoven.**  Společnost Microsoft poskytuje plně podporované open source knihoven pro oblíbené jazyky a platformy k urychlení vývoje. Zdrojový kód je licencován Apache 2.0 a můžete libovolně udělovat rozvětvili a přispívat zpět k projektům. Další informace o [knihoven ověřování služby Azure AD](active-directory-authentication-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Po celém světě a vysoká dostupnost
**Azure AD se nasadí v datových centrech po celém světě a je spravovat a monitorovat neustále a zobrazuje.**  Azure AD je systém správy identit pro Microsoft Azure a Office 365 a je nasazený v 28 datových centrech po celém světě. Data adresáře je zaručeno, že se musí replikovat do alespoň tři datových center. Nástroje pro vyrovnávání zatížení globálního zajistit uživatelům přístup nejbližší kopii Azure AD obsahující data a automaticky znovu směrovat požadavky do jiných datových centrech Pokud byl zjištěn problém.

## <a name="next-steps"></a>Další kroky
[Začněte psát kód](azure-ad-developers-guide.md#get-started).

[Přihlášení uživatelů pomocí Azure AD](active-directory-authentication-scenarios.md)

