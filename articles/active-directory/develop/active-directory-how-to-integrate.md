---
title: Jak integrovat s platformou Microsoft identity | Azure
titleSuffix: Microsoft identity platform
description: Seznamte se s výhodami integrace aplikace s platformou Microsoft identity a získejte prostředky pro funkce, jako je zjednodušené přihlašování, Správa identit, Multi-Factor Authentication a řízení přístupu.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/01/2020
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, seoapril2019
ROBOTS: NOINDEX
ms.openlocfilehash: b7ee283ff61753a060e49a3340cd0a795b04faf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755933"
---
# <a name="integrating-with-the-microsoft-identity-platform"></a>Integrace s platformou Microsoft identity

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

V tomto článku se dozvíte o výhodách integrace aplikace s platformou Microsoft identity a získání prostředků pro integraci. Microsoft Identity Platform a Azure Active Directory (AD) poskytuje organizacím správu identit na podnikové úrovni pro cloudové aplikace. Microsoft Identity Platform Integration poskytuje uživatelům Zjednodušené přihlašování a pomáhá vaší aplikaci v souladu se zásadami IT.

## <a name="how-to-integrate"></a>Jak integrovat

Existuje několik způsobů, jak se vaše aplikace integruje s platformou Microsoft identity. Využijte tolik než pár těchto scénářů, které jsou vhodné pro vaši aplikaci.

### <a name="support-the-microsoft-identity-platform-as-a-way-to-sign-in-to-your-application"></a>Podpora platformy Microsoft Identity jako způsobu přihlášení k aplikaci

**Snižte tření při přihlašování a snižte náklady na podporu.** Když se k vaší aplikaci přihlašujete pomocí platformy Microsoft identity, nebudou mít uživatelé ještě další jméno a heslo, abyste si je pamatovali. Jako vývojář budete mít jedno méně hesla pro ukládání a ochranu. Nemusíte zpracovávat zapomenuté resetování hesla, ale jenom významné úspory. Microsoft Identity Platform se přihlašuje pro některé z nejoblíbenějších cloudových aplikací z celého světa, včetně Microsoft 365 a Microsoft Azure. Se stovkami milionů uživatelů od milionů organizací je pravděpodobné, že se Váš uživatel už přihlásil k platformě Microsoft identity. Přečtěte si další informace o [Přidání podpory pro přihlášení k platformě Microsoft Identity Platform](./authentication-vs-authorization.md).

**Zjednodušte si registraci své aplikace.**  Při registraci aplikace může platforma Microsoft Identity posílat základní informace o uživateli, abyste mohli předem vyplnit formulář pro registraci nebo ho úplně eliminovat. Uživatelé se můžou k vaší aplikaci zaregistrovat pomocí svého účtu Azure AD prostřednictvím známého prostředí pro vyjádření souhlasu, které se podobá těm, která se nacházejí v sociálních médiích a mobilních aplikacích. Každý uživatel se může zaregistrovat a přihlásit k aplikaci, která je integrovaná s platformou Microsoft identity, a to bez nutnosti jejího zapojení. Přečtěte si další informace o [registraci aplikace pro přihlášení k účtu Azure AD](../../app-service/configure-authentication-provider-aad.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Vyhledat uživatele, spravovat zřizování uživatelů a řídit přístup k vaší aplikaci

**Vyhledejte uživatele v adresáři.**  Pomocí rozhraní Microsoft Graph API můžete uživatelům pomáhat vyhledávat a vyhledávat ostatní lidi v jejich organizaci při pozvání k ostatním uživatelům nebo pro udělení přístupu a nemusíte jim zadávat e-mailové adresy. Uživatelé můžou procházet pomocí známého rozhraní stylu adresáře, včetně zobrazení podrobností o organizační hierarchii. Přečtěte si další informace o [rozhraní Microsoft Graph API](/graph/overview).

**Opětovné použití skupin a distribučních seznamů služby Active Directory, které už váš zákazník spravuje**  Azure AD obsahuje skupiny, které váš zákazník už používá k distribuci e-mailů a správě přístupu. Pomocí rozhraní Microsoft Graph API znovu použijte tyto skupiny a nemusíte od zákazníka vytvářet a spravovat samostatnou sadu skupin ve vaší aplikaci. Do své aplikace můžete také odeslat informace o skupině v tokenech přihlašování. Přečtěte si další informace o [rozhraní Microsoft Graph API](/graph/overview).

**Pomocí platformy Microsoft Identity můžete řídit, kdo má přístup k vaší aplikaci.**  Správci a vlastníci aplikací v Azure AD můžou přiřazovat přístup k aplikacím pro konkrétní uživatele a skupiny. Pomocí rozhraní Microsoft Graph API si můžete tento seznam přečíst a použít ho k řízení zřizování a rušení zřizování prostředků a přístupu v rámci aplikace.

**Použijte platformu Microsoft Identity Platform pro role založené Access Control.**  Správci a vlastníci aplikací můžou přiřadit role uživatelů a skupin k rolím, které definujete při registraci aplikace na platformě Microsoft identity. Informace o roli se odesílají do vaší aplikace v tokenech přihlášení a dají se také číst pomocí rozhraní Microsoft Graph API. Přečtěte si další informace o [použití platformy Microsoft identity pro autorizaci](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Získání přístupu k profilu, kalendáři, e-mailu, kontaktům, souborům a dalším uživatelům

**Microsoft Identity Platform je autorizační Server pro Microsoft 365 a další služby Microsoftu pro firmy.**  Pokud podporujete platformu Microsoft identity pro přihlášení k vaší aplikaci nebo chcete-li podporovat propojení vašich uživatelských účtů s uživatelskými účty Azure AD pomocí OAuth 2,0, můžete požádat o přístup pro čtení a zápis k profilu uživatele, kalendáři, e-mailu, kontaktům, souborům a dalším informacím. Můžete hladce zapisovat události do kalendáře uživatelů a číst soubory nebo zapisovat na jejich OneDrive. Přečtěte si další informace o [rozhraních api Microsoft 365](/graph/overview).

### <a name="promote-your-application-in-the-azure-and-microsoft-365-marketplaces"></a>Propagace vaší aplikace v tržištích Azure a Microsoft 365

**Zvyšte svou aplikaci na miliony organizací, které už používají Azure AD.**  Uživatelé, kteří hledají a procházejí tyto tržiště, již používají jednu nebo více cloudových služeb a mají kvalifikované zákazníky s cloudovou službou. Přečtěte si další informace o podpoře aplikace v [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Když se uživatel do aplikace zaregistruje, zobrazí se na svém přístupovém panelu Azure AD a ve spouštěči aplikace Microsoft 365.**  Uživatelé se budou moct rychle a později vrátit do vaší aplikace, což zlepšuje zapojení uživatelů. Přečtěte si další informace o [přístupovém panelu Azure AD](../user-help/my-apps-portal-end-user-access.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Zabezpečení komunikace mezi zařízeními a službami

**Používání platformy Microsoft identity pro správu identit služeb a zařízení snižuje kód, který je potřeba napsat, a umožňuje spravovat přístup.**  Služby a zařízení mohou získat tokeny od platformy Microsoft identity pomocí protokolu OAuth a tyto tokeny použít pro přístup k webovým rozhraním API. Pomocí platformy Microsoft identity se můžete vyhnout psaní složitého ověřovacího kódu. Vzhledem k tomu, že identity služeb a zařízení jsou uložené ve službě Azure AD, může na jednom místě spravovat klíče a odvolání a nemusíte to dělat samostatně ve své aplikaci.

## <a name="benefits-of-integration"></a>Výhody integrace

Integrace s platformou Microsoft identity je dodávána s výhodami, které nevyžadují zápis dalšího kódu.

### <a name="integration-with-enterprise-identity-management"></a>Integrace se správou podnikových identit

**Pomůžete vaší aplikaci dodržovat zásady IT.**  Organizace integrují své systémy správy podnikových identit s platformou Microsoft identity, takže když osoba opustí organizaci, automaticky ztratí přístup k vaší aplikaci, aniž by museli provést další kroky. Může spravovat, kdo může mít přístup k vaší aplikaci, a určit, jaké zásady přístupu se vyžadují – například vícefaktorové ověřování – zmenšení potřeb psaní kódu pro zajištění dodržování složitých podnikových zásad. Azure AD poskytuje správcům podrobný protokol auditu, který se přihlásil ke svojí aplikaci, aby mohl sledovat využití.

**Azure AD rozšiřuje službu Active Directory do cloudu, takže vaše aplikace může být integrovaná se službou AD.**  Mnoho organizací po celém světě používá službu Active Directory jako svůj hlavní přihlašování a systém správy identit a vyžaduje, aby aplikace spolupracovaly se službou AD. Integrace se službou Azure AD integruje vaši aplikaci se službou Active Directory.

### <a name="advanced-security-features"></a>Pokročilé funkce zabezpečení

**Multi-Factor Authentication.**  Platforma Microsoft Identity poskytuje nativní službu Multi-Factor Authentication. Správci IT můžou pro přístup k vaší aplikaci vyžadovat vícefaktorové ověřování, takže tuto podporu nemusíte zakódovat sami. Přečtěte si další informace o [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Neobvyklé zjišťování přihlášení.**  Platforma identity Microsoftu zpracovává více než miliarda přihlášení denně a při použití algoritmů strojového učení k detekci podezřelých aktivit a oznamuje správcům IT možné problémy. Díky podpoře přihlášení k platformě Microsoft Identity Platform získá vaše aplikace výhodu této ochrany. Přečtěte si další informace o [zobrazení sestavy Azure Active Directory Access](../reports-monitoring/overview-reports.md).

**Podmíněný přístup.**  Kromě služby Multi-Factor Authentication můžou správci vyžadovat splnění určitých podmínek, než se uživatelé můžou přihlásit ke svojí aplikaci. Podmínky, které je možné nastavit, zahrnují rozsah IP adres klientských zařízení, členství v zadaných skupinách a stav zařízení, které se používá pro přístup. Přečtěte si další informace o [Azure Active Directory podmíněný přístup](../conditional-access/overview.md).

### <a name="easy-development"></a>Snadný vývoj

**Standardní protokoly.**  Společnost Microsoft se zavazuje podporovat oborové standardy. Platforma Microsoft identity podporuje standardní protokoly OAuth 2,0 a OpenID Connect 1,0. Přečtěte si další informace o [ověřovacích protokolech Microsoft Identity Platform](active-directory-v2-protocols.md).

**Otevřete zdrojové knihovny.**  Microsoft poskytuje plně podporované knihovny open source pro oblíbené jazyky a platformy, které urychlují vývoj. Zdrojový kód je licencován v rámci Apache 2,0 a vy budete moci rozvětvit a přispívat zpátky k projektům. Přečtěte si další informace o [knihovně Microsoft Authentication Library (MSAL)](reference-v2-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Přítomnost po celém světě a vysoká dostupnost

**Služba Azure AD je nasazená v datových centrech po celém světě a je spravovaná a monitorovaná v reálném čase.**  Azure AD je systém správy identit pro Microsoft Azure a Microsoft 365 a je nasazený ve 28 datacentrech po celém světě. Je zaručeno, že data adresáře budou replikována alespoň do tří datových center. Globální nástroje pro vyrovnávání zatížení zajišťují uživatelům přístup k nejbližší kopii služby Azure AD obsahující jejich data a při zjištění problému automaticky přesměrují požadavky do jiných datových center.

## <a name="next-steps"></a>Další kroky

Začněte [psát kód](v2-overview.md#getting-started).

[Podepisování uživatelů pomocí platformy Microsoft identity](./authentication-vs-authorization.md)