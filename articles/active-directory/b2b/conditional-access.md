---
title: Podmíněný přístup pro uživatele spolupráce B2B – Azure AD
description: Spolupráce Azure Active Directory B2B podporuje vícefaktorové ověřování (MFA) pro selektivní přístup k podnikovým aplikacím
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
ms.openlocfilehash: c0b6ceba4c3c9202e2024b5c163c0e98bb6cbf55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272998"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Podmíněný přístup pro uživatele spolupráce B2B

## <a name="multi-factor-authentication-for-b2b-users"></a>Vícefaktorové ověřování pro uživatele B2B
Díky spolupráci Azure AD B2B můžou organizace vynucovat vícefaktorové ověřování (MFA) pro uživatele B2B. Tyto zásady lze vynutit na úrovni klienta, aplikace nebo jednotlivých uživatelů stejným způsobem, jakým jsou povoleny pro zaměstnance na plný úvazek a členy organizace. Zásady vícefaktorové a finanční ho disponitova jsou vynuceny v organizaci prostředků.

Příklad:
1. Administrátor nebo informační pracovník ve společnosti A pozve uživatele ze společnosti B do aplikace *Foo* ve společnosti A.
2. Aplikace *Foo* ve společnosti A je nakonfigurována tak, aby vyžadovala vícefaktorové přístupy.
3. Když se uživatel ze společnosti B pokusí o přístup k aplikaci *Foo* ve společnosti Tenant, bude požádán o dokončení výzvy vícefaktorové ověřování.
4. Uživatel může nastavit své vícefaktorové ověřování ve společnosti A a zvolí možnost vícefaktorové ověřování.
5. Tento scénář funguje pro všechny identity (Azure AD nebo MSA, například pokud uživatelé ve společnosti B ověřit pomocí id sociální)
6. Společnost A musí mít dostatečné licence Premium Azure AD, které podporují vícefaktorové povolení. Uživatel ze společnosti B spotřebovává tuto licenci od společnosti A.

Pozvání nájmu je vždy zodpovědný za vícefaktorové informace pro uživatele z partnerské organizace, i v případě, že partnerská organizace má možnosti vícefaktorové žádosti.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Nastavení mfa pro uživatele spolupráce B2B
Chcete-li zjistit, jak snadné je nastavit vícefaktorové informace pro uživatele spolupráce B2B, podívejte se na následující video:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>B2B uživatelé MFA zkušenosti pro nabídku vykoupení
Podívejte se na následující animaci a podívejte se na možnost ipoje:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>Obnovení vícefaktorové informace pro uživatele spolupráce B2B
V současné době může správce vyžadovat, aby uživatelé spolupráce B2B znovu zostřili pouze pomocí následujících rutin prostředí PowerShell:

1. Připojení k Azure AD

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Získejte všechny uživatele s metodami korektního

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Zde naleznete příklad:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Resetujte metodu MFA pro konkrétního uživatele tak, aby uživatel spolupráce B2B znovu nastavil metody ověření. Příklad:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Proč provádíme vícefaktorové finanční zdroje v nájmu prostředků?

V aktuální verzi mfa je vždy v nájmu prostředků, z důvodu předvídatelnosti. Řekněme například, že uživatel contoso (Sally) je pozván na Fabrikam a společnost Fabrikam povolila vícefaktorové ověřování pro uživatele B2B.

Pokud má Contoso povoleny zásady MFA pro App1, ale ne App2, pak pokud se podíváme na deklaraci Contoso MFA v tokenu, může se zobrazit následující problém:

* Den 1: Uživatel má Vícefaktorové ověřování v Contoso a je přístup App1, pak žádné další výzvu MFA se zobrazí ve Fabrikam.

* Den 2: Uživatel má přístup k App 2 v Contoso, takže nyní při přístupu Fabrikam, musí se zaregistrovat pro Vícefaktor tam.

Tento proces může být matoucí a může vést k poklesu dokončení přihlášení.

Navíc i v případě, že Contoso má schopnost vícefaktorové finanční hod, není to vždy případ, kdy by společnost Fabrikam důvěřovala zásadě Contoso MFA.

Nakonec prostředek klienta MFA funguje také pro MSA a id sociální chod a pro partnerské organizace, které nemají mfa nastavit.

Proto doporučení pro vícefaktorové finanční hodu pro uživatele B2B je vždy vyžadovat vícefaktorové vyzvučitného klienta. Tento požadavek může vést k dvojité vícefaktorové v některých případech, ale vždy, když přístup k pozvání klienta, prostředí koncových uživatelů je předvídatelné: Sally musí zaregistrovat pro vícefaktorové povolení s pozvání mau.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Podmíněný přístup založený na zařízení, na poloze a na základě rizika pro uživatele B2B

Když společnost Contoso povolí zásady podmíněného přístupu založené na zařízení pro jejich podniková data, přístup je zabráněno zařízení, která nejsou spravována společností Contoso a nejsou kompatibilní se zásadami zařízení Contoso.

Pokud zařízení uživatele B2B není spravováno společností Contoso, přístup uživatelů B2B z partnerských organizací je blokován v jakémkoli kontextu, ve které jsou tyto zásady vynuceny. Společnost Contoso však může vytvořit seznamy vyloučení obsahující konkrétní partnerské uživatele, aby je vyloučila ze zásad podmíněného přístupu založeného na zařízení.

#### <a name="mobile-application-management-policies-for-b2b"></a>Zásady správy mobilních aplikací pro B2B

Zásady ochrany aplikací podmíněného přístupu nelze použít na uživatele B2B, protože zvoucí organizace nemá žádný přehled o domácí organizaci uživatele B2B.

#### <a name="location-based-conditional-access-for-b2b"></a>Podmíněný přístup založený na poloze pro B2B

Zásady podmíněného přístupu založené na poloze lze vynutit pro uživatele B2B, pokud zvoucí organizace je schopna vytvořit rozsah důvěryhodných IP adres, který definuje jejich partnerské organizace.

#### <a name="risk-based-conditional-access-for-b2b"></a>Podmíněný přístup založený na riziku pro B2B

V současné době zásady přihlášení na základě rizik nelze použít pro uživatele B2B, protože hodnocení rizika se provádí v domácí organizaci uživatele B2B.

## <a name="next-steps"></a>Další kroky

Podívejte se na následující články o spolupráci Azure AD B2B:

* [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
* [Licencování spolupráce B2B ve službě Azure AD](licensing-guidance.md)
* [Nejčastější dotazy týkající se spolupráce Azure Active Directory s B2B](faq.md)
