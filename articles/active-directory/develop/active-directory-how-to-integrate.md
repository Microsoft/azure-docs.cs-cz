---
title: Postup při integraci s Azure Active Directory | Microsoft Docs
description: Seznamte se s výhodami integrace vaší aplikace s Azure Active Directory a získejte prostředky pro funkce, jako je zjednodušené přihlašování, Správa identit, vícefaktorové ověřování a řízení přístupu.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: d13bba54-96bd-4b81-bee9-c8025ffa1648
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48be16827da21643af485d1fb94b9a7b76728325
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374038"
---
# <a name="integrating-with-azure-active-directory"></a>Integrace s Azure Active Directory

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

V tomto článku se dozvíte o výhodách integrace aplikace s Azure Active Directory (Azure AD) a získání prostředků pro integraci. Azure AD poskytuje organizacím se správou identit na podnikové úrovni pro cloudové aplikace. Integrace Azure AD poskytuje uživatelům Zjednodušené přihlašování a pomáhá vaší aplikaci v souladu se zásadami IT.

## <a name="how-to-integrate"></a>Jak integrovat

Existuje několik způsobů, jak se vaše aplikace integruje s Azure AD. Využijte tolik než pár těchto scénářů, které jsou vhodné pro vaši aplikaci.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Podpora Azure AD jako způsobu přihlášení k aplikaci

**Snižte tření při přihlašování a snižte náklady na podporu.** Když ke své aplikaci přihlašujete pomocí Azure AD, uživatelé nebudou mít k zapamatování další jméno a heslo. Jako vývojář budete mít jedno méně hesla pro ukládání a ochranu. Nemusíte zpracovávat zapomenuté resetování hesla, ale jenom významné úspory. Pravomoci Azure AD se přihlásí pro některé z nejoblíbenějších cloudových aplikací světa, včetně Office 365 a Microsoft Azure. Se stovkami milionů uživatelů od milionů organizací je pravděpodobné, že se Váš uživatel už přihlásil ke službě Azure AD. Přečtěte si další informace o [Přidání podpory pro přihlášení ke službě Azure AD](v1-authentication-scenarios.md).

**Zjednodušte si registraci své aplikace.**  Při registraci vaší aplikace může Azure AD odeslat základní informace o uživateli, abyste mohli předem vyplnit formulář pro registraci, nebo ho úplně eliminovat. Uživatelé se můžou k vaší aplikaci zaregistrovat pomocí svého účtu Azure AD prostřednictvím známého prostředí pro vyjádření souhlasu, které se podobá těm, která se nacházejí v sociálních médiích a mobilních aplikacích. Každý uživatel se může zaregistrovat a přihlásit k aplikaci, která je integrovaná s Azure AD bez nutnosti zapojení IT. Přečtěte si další informace o [registraci aplikace pro přihlášení k účtu Azure AD](../../app-service/configure-authentication-provider-aad.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Vyhledat uživatele, spravovat zřizování uživatelů a řídit přístup k vaší aplikaci

**Vyhledejte uživatele v adresáři.**  Pomocí Graph API můžete uživatelům pomáhat při hledání a vyhledávání dalších uživatelů v jejich organizaci při pozvání k ostatním uživatelům nebo pro udělení přístupu a nemusíte jim zadávat e-mailové adresy. Uživatelé můžou procházet pomocí známého rozhraní stylu adresáře, včetně zobrazení podrobností o organizační hierarchii. Přečtěte si další informace o [Graph API](active-directory-graph-api.md).

**Opětovné použití skupin a distribučních seznamů služby Active Directory, které už váš zákazník spravuje**  Azure AD obsahuje skupiny, které váš zákazník už používá k distribuci e-mailů a správě přístupu. Pomocí Graph API znovu použijte tyto skupiny a nemusíte zákazníka vytvářet a spravovat samostatnou sadu skupin ve vaší aplikaci. Do své aplikace můžete také odeslat informace o skupině v tokenech přihlašování. Přečtěte si další informace o [Graph API](active-directory-graph-api.md).

**Pomocí Azure AD můžete řídit, kdo má přístup k vaší aplikaci.**  Správci a vlastníci aplikací v Azure AD můžou přiřazovat přístup k aplikacím pro konkrétní uživatele a skupiny. Pomocí Graph API můžete tento seznam přečíst a použít ho k řízení zřizování a rušení zřizování prostředků a přístupu v rámci aplikace.

**Použijte Azure AD pro role založené Access Control.**  Správci a vlastníci aplikací můžou přiřadit uživatele a skupiny rolím, které definujete při registraci aplikace ve službě Azure AD. Informace o roli se odesílají do vaší aplikace v tokenech přihlášení a dají se také číst pomocí Graph API. Přečtěte si další informace o [použití Azure AD k autorizaci](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Získání přístupu k profilu uživatele, kalendáři, e-mailu, kontaktům, souborům a dalším datům

**Azure AD je autorizační Server pro Office 365 a další služby Microsoftu pro firmy.**  Pokud pro přihlášení k vaší aplikaci nebo podpoře připojení vašich uživatelských účtů k účtům uživatelů Azure AD pomocí OAuth 2,0, můžete požádat o přístup pro čtení a zápis k profilu uživatele, kalendáři, e-mailu, kontaktům, souborům a dalším informacím. Můžete hladce zapisovat události do kalendáře uživatelů a číst soubory nebo zapisovat na jejich OneDrive. Přečtěte si další informace o [přístupu k rozhraním API Office 365](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Propagace vaší aplikace v tržištích Azure a Office 365

**Zvyšte svou aplikaci na miliony organizací, které už používají Azure AD.**  Uživatelé, kteří hledají a procházejí tyto tržiště, již používají jednu nebo více cloudových služeb a mají kvalifikované zákazníky s cloudovou službou. Přečtěte si další informace o podpoře aplikace v [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Když se uživatelé registrují do vaší aplikace, zobrazí se na svém přístupovém panelu Azure AD a ve Spouštěči aplikací Office 365.**  Uživatelé se budou moct rychle a později vrátit do vaší aplikace, což zlepšuje zapojení uživatelů. Přečtěte si další informace o [přístupovém panelu Azure AD](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Zabezpečení komunikace mezi zařízeními a službami

**Používání Azure AD pro správu identit služeb a zařízení snižuje kód, který je potřeba napsat, a umožňuje spravovat přístup.**  Služby a zařízení můžou získat tokeny z Azure AD pomocí OAuth a použít tyto tokeny pro přístup k webovým rozhraním API. Pomocí Azure AD se můžete vyhnout psaní složitého ověřovacího kódu. Vzhledem k tomu, že identity služeb a zařízení jsou uložené ve službě Azure AD, může na jednom místě spravovat klíče a odvolání a nemusíte to dělat samostatně ve své aplikaci.

## <a name="benefits-of-integration"></a>Výhody integrace

Integrace se službou Azure AD přináší výhody, které nevyžadují zápis dalšího kódu.

### <a name="integration-with-enterprise-identity-management"></a>Integrace se správou podnikových identit

**Pomůžete vaší aplikaci dodržovat zásady IT.**  Organizace integrují své podnikové systémy správy identit do služby Azure AD, takže když osoba opustí organizaci, automaticky ztratí přístup k vaší aplikaci, aniž by museli provést další kroky. Může spravovat, kdo může mít přístup k vaší aplikaci, a určit, jaké zásady přístupu se vyžadují – například vícefaktorové ověřování – zmenšení potřeb psaní kódu pro zajištění dodržování složitých podnikových zásad. Azure AD poskytuje správcům podrobný protokol auditu, který se přihlásil ke svojí aplikaci, aby mohl sledovat využití.

**Azure AD rozšiřuje službu Active Directory do cloudu, takže vaše aplikace může být integrovaná se službou AD.**  Mnoho organizací po celém světě používá službu Active Directory jako svůj hlavní přihlašování a systém správy identit a vyžaduje, aby aplikace spolupracovaly se službou AD. Integrace se službou Azure AD integruje vaši aplikaci se službou Active Directory.

### <a name="advanced-security-features"></a>Rozšířené funkce zabezpečení

**Multi-Factor Authentication.**  Azure AD poskytuje nativní službu Multi-Factor Authentication. Správci IT můžou pro přístup k vaší aplikaci vyžadovat vícefaktorové ověřování, takže tuto podporu nemusíte zakódovat sami. Přečtěte si další informace o [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Neobvyklé zjišťování přihlášení.**  Služba Azure AD zpracovává více než miliarda přihlášení denně a při použití algoritmů strojového učení ke zjištění podezřelých aktivit a oznamuje správcům IT možné problémy. Díky podpoře přihlášení Azure AD získá aplikace výhodu této ochrany. Přečtěte si další informace o [zobrazení sestavy Azure Active Directory Access](../active-directory-view-access-usage-reports.md).

**Podmíněný přístup.**  Kromě služby Multi-Factor Authentication můžou správci vyžadovat splnění určitých podmínek, než se uživatelé můžou přihlásit ke svojí aplikaci. Podmínky, které je možné nastavit, zahrnují rozsah IP adres klientských zařízení, členství v zadaných skupinách a stav zařízení, které se používá pro přístup. Přečtěte si další informace o [Azure Active Directory podmíněný přístup](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Snadný vývoj

**Standardní protokoly.**  Společnost Microsoft se zavazuje podporovat oborové standardy. Platforma Microsoft identity podporuje standardní protokoly OAuth 2,0 a OpenID Connect 1,0. Přečtěte si další informace o [ověřovacích protokolech Microsoft Identity Platform](active-directory-v2-protocols.md).

**Otevřete zdrojové knihovny.**  Microsoft poskytuje plně podporované knihovny open source pro oblíbené jazyky a platformy, které urychlují vývoj. Zdrojový kód je licencován v rámci Apache 2,0 a vy budete moci rozvětvit a přispívat zpátky k projektům. Přečtěte si další informace o [knihovně Microsoft Authentication Library (MSAL)](reference-v2-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Přítomnost po celém světě a vysoká dostupnost

**Služba Azure AD je nasazená v datových centrech po celém světě a je spravovaná a monitorovaná v reálném čase.**  Azure AD je systém správy identit pro Microsoft Azure a Office 365 a je nasazený ve 28 datacentrech po celém světě. Je zaručeno, že data adresáře budou replikována alespoň do tří datových center. Globální nástroje pro vyrovnávání zatížení zajišťují uživatelům přístup k nejbližší kopii služby Azure AD obsahující jejich data a při zjištění problému automaticky přesměrují požadavky do jiných datových center.

## <a name="next-steps"></a>Další kroky

Začněte [psát kód](v2-overview.md#getting-started).

[Podepisování uživatelů při používání platformy Microsoft Identity Platform](v1-authentication-scenarios.md)

