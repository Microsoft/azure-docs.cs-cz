---
title: Podmíněný přístup pro uživatele spolupráce B2B – Azure AD
description: Naučte se vymáhat zásady služby Multi-Factor Authentication pro Azure Active Directory uživatelů B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: eccbbb22814788aaf06fa6fd10d8c376203c1d49
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892447"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Podmíněný přístup pro uživatele spolupráce B2B

## <a name="multi-factor-authentication-for-b2b-users"></a>Multi-Factor Authentication pro uživatele B2B
Díky spolupráci Azure AD B2B můžou organizace vyhovět zásadám vícefaktorového ověřování (MFA) pro uživatele B2B. Tyto zásady je možné vyhovět na úrovni tenanta, aplikace nebo jednotlivého uživatele stejným způsobem, jako jsou povolené pro zaměstnance a členy organizace v plném čase. Zásady vícefaktorového ověřování se uplatňují v organizaci poskytující prostředky.

Příklad:
1. Správce nebo informační pracovník ve společnosti A vyzývá uživatele od společnosti B k aplikaci *foo* v organizaci a.
2. *Foo* aplikace v organizaci A je nakonfigurovaná tak, aby při přístupu VYŽADOVALA vícefaktorové ověřování.
3. Když se uživatel z firmy B pokusí získat přístup k aplikaci *foo* v organizaci, zobrazí se výzva k dokončení ověřování MFA.
4. Uživatel může nastavit MFA pomocí firmy a a vybrat možnost MFA.
5. Tento scénář je vhodný pro jakoukoli identitu (Azure AD nebo MSA), například pokud uživatelé ve společnosti B ověřují pomocí sociálního ID).
6. Společnost A musí mít dostatečný nárok na licence Azure AD, které podporují MFA. Uživatel z firmy B tuto licenci využívá od společnosti A.

Pro uživatele z partnerské organizace je vždy zodpovědný za MFA, a to i v případě, že má partnerská organizace možnosti MFA.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Nastavení MFA pro uživatele spolupráce B2B
Pokud chcete zjistit, jak snadné je nastavit MFA pro uživatele spolupráce B2B, přečtěte si článek jak v tomto videu:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>Možnosti ověřování uživatelů B2B pro nabídku
Podívejte se na následující animaci, abyste viděli prostředí pro uplatnění:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>Resetování MFA pro uživatele spolupráce B2B
Správce v současné době může vyžadovat, aby se uživatelé spolupráce B2B znovu probrali pomocí následujících rutin PowerShellu:

1. Připojení k Azure AD

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Získat všechny uživatele pomocí metod kontroly pravopisu

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Tady je příklad:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Resetujte metodu MFA pro konkrétního uživatele, který bude vyžadovat, aby uživatel spolupráce B2B znovu nastavil metody pro kontrolu pravopisu. Příklad:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Proč provádíme vícefaktorové ověřování v architektuře prostředků?

V aktuální verzi je MFA vždy v architektuře prostředků, z důvodu předvídatelnosti. Řekněme například, že uživatel společnosti Contoso (Sally) je pozván společnosti Fabrikam a společnost Fabrikam povolila MFA pro uživatele B2B.

Pokud má společnost Contoso povolené zásady MFA pro app1, ale ne app2, pak se v případě, že se podíváme na deklaracích identity MFA společnosti Contoso v tokenu, se může zobrazit následující problém:

* Den 1: uživatel má v contoso přístup MFA a přistupuje k app1, ve společnosti Fabrikam se nezobrazuje žádná další výzva MFA.

* Den 2: uživatel získal přístup k aplikaci 2 ve společnosti Contoso, takže když teď přistupuje k společnosti Fabrikam, musí se tam zaregistrovat pro MFA.

Tento proces může být matoucí a může vést k poklesu dokončení přihlášení.

Kromě toho i v případě, že společnost Contoso má možnost MFA, není vždy v případě, že společnost Fabrikam důvěřuje zásadě MFA společnosti Contoso.

V konečném případě MFA tenanta prostředků funguje taky pro účty spravované služby a sociální ID a pro partnery organizace, kteří nemají nastavené MFA.

Proto bude doporučení pro MFA pro uživatele B2B vždy vyžadovat MFA v tenantovi pozvání. Tento požadavek může v některých případech způsobit dvojnásobnou MFA, ale při každém přístupu k tomuto tenantovi je prostředí koncových uživatelů předvídatelné: Sally se musí zaregistrovat pro MFA s pozváním klienta.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Podmíněný přístup založený na zařízeních, na základě polohy a rizik pro uživatele B2B

Když společnost Contoso povoluje zásady podmíněného přístupu na základě zařízení pro jejich podniková data, přístup se zabrání ze zařízení, která nespravuje společnost Contoso a která nevyhovují zásadám zařízení společnosti Contoso.

Pokud zařízení uživatele B2B nespravuje společnost Contoso, bude přístup uživatelů B2B od partnerských organizací blokovaný v jakémkoli kontextu, který tyto zásady vynutily. Contoso ale může vytvořit seznamy vyloučení, které obsahují konkrétní partnerské uživatele, aby je vyloučily ze zásad podmíněného přístupu na základě zařízení.

#### <a name="mobile-application-management-policies-for-b2b"></a>Zásady správy mobilních aplikací pro B2B

Zásady ochrany aplikací podmíněného přístupu nelze použít na uživatele B2B, protože subjekt pro pozvání k organizaci neobsahují žádné informace v domovské organizaci uživatele B2B.

#### <a name="location-based-conditional-access-for-b2b"></a>Podmíněný přístup založený na poloze pro B2B

Zásady podmíněného přístupu na základě umístění je možné vyhovět pro uživatele B2B, pokud je možné, že organizace pro pozvání vytvoří rozsah důvěryhodných IP adres, který definuje své partnerské organizace.

#### <a name="risk-based-conditional-access-for-b2b"></a>Podmíněný přístup na základě rizik pro B2B

V současné době nelze zásady přihlašování založené na rizicích použít na uživatele B2B, protože hodnocení rizika se provádí v domácí organizaci uživatele B2B.

## <a name="next-steps"></a>Další kroky

Přečtěte si následující články o spolupráci Azure AD B2B:

* [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
* [Externí identity – ceny](external-identities-pricing.md)
* [Nejčastější dotazy týkající se spolupráce Azure Active Directory s B2B](faq.md)
