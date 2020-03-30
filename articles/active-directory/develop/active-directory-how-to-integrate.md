---
title: Jak integrovat službu Azure Active Directory | Dokumenty společnosti Microsoft
description: Seznamte se s výhodami integrace aplikace s Azure Active Directory a získejte prostředky pro funkce, jako je zjednodušené přihlášení, správa identit, vícefaktorové ověřování a řízení přístupu.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: d13bba54-96bd-4b81-bee9-c8025ffa1648
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 5e4c6fb91b93b1035c68350a5f77a3c16db7323f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300108"
---
# <a name="integrating-with-azure-active-directory"></a>Integrace s Azure Active Directory

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

V tomto článku se dozvíte o výhodách integrace vaší aplikace s Azure Active Directory (Azure AD) a získat prostředky pro integraci. Azure AD poskytuje organizacím správu identit na podnikové úrovni pro cloudové aplikace. Integrace Azure AD poskytuje uživatelům zjednodušené přihlašovací prostředí a pomáhá vaší aplikaci v souladu se zásadami IT.

## <a name="how-to-integrate"></a>Jak integrovat

Existuje několik způsobů, jak se vaše aplikace integrovat s Azure AD. Využijte tolik nebo tolik z těchto scénářů, jak je vhodné pro vaši aplikaci.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Podpora Azure AD jako způsob přihlášení k vaší aplikaci

**Snižte tření při přihlašování a náklady na podporu.** Pomocí Azure AD pro přihlášení k vaší aplikaci, vaši uživatelé nebudou mít ani jedno jméno a heslo k zapamatování. Jako vývojář budete mít o jedno heslo méně pro ukládání a ochranu. Není třeba zpracovávat zapomenuté resetování hesla může být významnou úsporou sám. Azure AD umožňuje přihlásit se k některým z nejpopulárnějších cloudových aplikací na světě, včetně Office 365 a Microsoft Azure. Se stovkami milionů uživatelů z milionů organizací je pravděpodobné, že váš uživatel je už přihlášený ke službě Azure AD. Další informace o [přidání podpory pro přihlášení k Azure AD](authentication-scenarios.md).

**Zjednodušte registraci pro vaši aplikaci.**  Během registrace pro vaši aplikaci, Azure AD můžete odeslat základní informace o uživateli, takže můžete předem vyplnit registrační formulář nebo zcela odstranit. Uživatelé se můžou zaregistrovat do vaší aplikace pomocí svého účtu Azure AD prostřednictvím známého prostředí souhlasu podobnému tomu, které se nacházejí v sociálních médiích a mobilních aplikacích. Každý uživatel se může zaregistrovat a přihlásit k aplikaci, která je integrovaná s Azure AD bez nutnosti zapojení IT. Přečtěte si další informace o [registraci aplikace pro přihlášení k účtu Azure AD](../../app-service/configure-authentication-provider-aad.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Vyhledejte uživatele, spravujte zřizování uživatelů a kontrolujte přístup k aplikaci.

**Vyhledejte uživatele v adresáři.**  Rozhraní Microsoft Graph API slouží uživatelům vyhledávat a vyhledávat další uživatele v jejich organizaci, když zvou jiné uživatele nebo udělují přístup, místo toho, aby museli zadávat e-mailové adresy. Uživatelé mohou procházet pomocí známého rozhraní stylu adresáře, včetně zobrazení podrobností organizační hierarchie. Další informace o [rozhraní Microsoft Graph API](https://docs.microsoft.com/graph/overview).

**Znovu použijte skupiny a distribuční seznamy služby Active Directory, které zákazník již spravuje.**  Azure AD obsahuje skupiny, které váš zákazník už používá pro distribuci e-mailu a správu přístupu. Pomocí rozhraní Microsoft Graph API, znovu použít tyto skupiny namísto požadavku, aby zákazník vytvořit a spravovat samostatnou sadu skupin ve vaší aplikaci. Informace o skupině lze také odeslat do vaší aplikace v tokenech přihlášení. Další informace o [rozhraní Microsoft Graph API](https://docs.microsoft.com/graph/overview).

**Azure AD slouží k řízení, kdo má přístup k vaší aplikaci.**  Správci a vlastníci aplikací ve službě Azure AD můžou aplikacím přiřadit přístup konkrétním uživatelům a skupinám. Pomocí rozhraní Microsoft Graph API si můžete přečíst tento seznam a použít jej k řízení zřizování a zrušení zřizování prostředků a přístupu v rámci vaší aplikace.

**Použití Azure AD pro řízení přístupu na základě rolí.**  Správci a vlastníci aplikací můžete přiřadit uživatele a skupiny k rolím, které definujete při registraci aplikace ve službě Azure AD. Informace o roli se odesílá do aplikace v tokenech přihlášení a lze také číst pomocí rozhraní Microsoft Graph API. Další informace o [používání Azure AD pro autorizaci](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Získání přístupu k profilu uživatele, kalendáři, e-mailu, kontaktům, souborům a dalším

**Azure AD je autorizační server pro Office 365 a další obchodní služby Microsoftu.**  Pokud podporujete Azure AD pro přihlášení k vaší aplikaci nebo podporu propojení vašich aktuálních uživatelských účtů s uživatelskými účty Azure AD pomocí OAuth 2.0, můžete požádat o přístup pro čtení a zápis do profilu uživatele, kalendář, e-mail, kontakty, soubory a další informace. Události můžete plynule zapisovat do kalendáře uživatele a číst nebo zapisovat soubory na jeho OneDrive. Přečtěte si další informace o [přístupu k apim Office 365](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Propagace vaší aplikace na Azure a tržištích Office 365

**Propagujte svou aplikaci na miliony organizací, které už používají Azure AD.**  Uživatelé, kteří prohledávají a procházejí tato tržiště, již používají jednu nebo více cloudových služeb, což z nich činí kvalifikované zákazníky cloudových služeb. Další informace o propagaci aplikace [na Webu Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Když se uživatelé zaregistrují do vaší aplikace, zobrazí se na přístupovém panelu Azure AD a spouštěči aplikací Office 365.**  Uživatelé se budou moci rychle a snadno vrátit do vaší aplikace později, což zlepší zapojení uživatelů. Další informace o [přístupovém panelu Azure AD](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Zabezpečená komunikace mezi zařízeními a službami

**Použití Azure AD pro správu identit služeb a zařízení snižuje kód, který potřebujete k zápisu a umožňuje IT spravovat přístup.**  Služby a zařízení můžete získat tokeny z Azure AD pomocí OAuth a používat tyto tokeny pro přístup k webovým apim. Pomocí Azure AD se můžete vyhnout psaní složitého ověřovacího kódu. Vzhledem k tomu, že identity služeb a zařízení jsou uloženy ve službě Azure AD, IT můžete spravovat klíče a odvolání na jednom místě namísto nutnosti to provést samostatně ve vaší aplikaci.

## <a name="benefits-of-integration"></a>Výhody integrace

Integrace s Azure AD přichází s výhodami, které nevyžadují, abyste napsali další kód.

### <a name="integration-with-enterprise-identity-management"></a>Integrace se správou podnikových identit

**Pomozte své aplikaci dodržovat zásady IT.**  Organizace integrují své podnikové systémy správy identit s Azure AD, takže když osoba opustí organizaci, automaticky ztratí přístup k vaší aplikaci, aniž by museli provést další kroky. ODDĚLENÍ IT může spravovat, kdo má přístup k vaší aplikaci, a určit, jaké zásady přístupu jsou vyžadovány – například vícefaktorové ověřování – což snižuje potřebu psát kód v souladu se složitými podnikovými zásadami. Azure AD poskytuje správcům podrobný protokol auditu o tom, kdo se přihlásil k vaší aplikaci, aby oddělení IT mohlo sledovat využití.

**Azure AD rozšiřuje službu Active Directory do cloudu, takže vaše aplikace může integrovat s AD.**  Mnoho organizací po celém světě používá službu Active Directory jako svůj hlavní systém přihlášení a správy identit a vyžaduje, aby jejich aplikace pracovaly se službou AD. Integrace s Azure AD integruje vaši aplikaci se službou Active Directory.

### <a name="advanced-security-features"></a>Pokročilé funkce zabezpečení

**Vícefaktorové ověřování.**  Azure AD poskytuje nativní vícefaktorové ověřování. Správci IT mohou vyžadovat vícefaktorové ověřování pro přístup k vaší aplikaci, takže není nutné kódovat tuto podporu sami. Další informace o [vícefaktorovém ověřování](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Anomální detekce přihlášení.**  Azure AD zpracovává více než miliardu přihlášení denně, zatímco pomocí algoritmů strojového učení zjistit podezřelou aktivitu a upozornit správce IT na možné problémy. Podporou přihlášení azure ad, vaše aplikace získá výhodu této ochrany. Přečtěte si další informace o [zobrazení sestavy přístupu služby Azure Active Directory](../active-directory-view-access-usage-reports.md).

**Podmíněný přístup.**  Kromě vícefaktorového ověřování mohou správci před přihlášením k vaší aplikaci vyžadovat splnění určitých podmínek. Podmínky, které lze nastavit, zahrnují rozsah IP adres klientských zařízení, členství v určených skupinách a stav zařízení používaného pro přístup. Další informace o [podmíněném přístupu služby Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Snadný vývoj

**Standardní protokoly.**  Společnost Microsoft se zavázala podporovat oborové standardy. Platforma identit microsoftu podporuje standardní protokoly OAuth 2.0 a OpenID Connect 1.0. Další informace o [ověřovacích protokolech platformy Identity Společnosti Microsoft](active-directory-v2-protocols.md).

**Open source knihovny.**  Společnost Microsoft poskytuje plně podporované open source knihovny pro oblíbené jazyky a platformy pro urychlení vývoje. Zdrojový kód je licencován pod Apache 2.0 a máte možnost rozdužovat a přispívat zpět k projektům. Další informace o [knihovně Ověřování společnosti Microsoft (MSAL)](reference-v2-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Celosvětová přítomnost a vysoká dostupnost

**Azure AD se nasazuje v datových centrech po celém světě a spravuje se a monitoruje nepřetržitě.**  Azure AD je systém správy identit pro Microsoft Azure a Office 365 a nasadí se ve 28 datových centrech po celém světě. Je zaručeno, že data adresáře budou replikována alespoň do tří datových center. Globální nástroje pro vyrovnávání zatížení zajišťují uživatelům přístup k nejbližší kopii Azure AD obsahující jejich data a automaticky přesměrovat požadavky do jiných datových center, pokud je zjištěn problém.

## <a name="next-steps"></a>Další kroky

[Začínáme psát kód](v2-overview.md#getting-started).

[Přihlášení uživatelů pomocí platformy microsoftidentity](authentication-scenarios.md)

