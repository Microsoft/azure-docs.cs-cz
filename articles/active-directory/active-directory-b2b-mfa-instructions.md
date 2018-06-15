---
title: Podmíněný přístup pro uživatele spolupráce Azure Active Directory s B2B | Microsoft Docs
description: Spolupráce Azure Active Directory s B2B podporuje vícefaktorové ověřování (MFA) pro výběrový přístup k podnikovým aplikacím
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 09/11/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 2fed28bb7f7b61556a36b11b325ace9773f89991
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33931085"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Podmíněný přístup pro uživatele spolupráce B2B

## <a name="multi-factor-authentication-for-b2b-users"></a>Víceúrovňové ověřování pro uživatele s B2B
S spolupráce Azure AD B2B organizace můžete vynutit zásady vícefaktorového ověřování (MFA) pro uživatele B2B. Tyto zásady lze vynutit na klienta, aplikace nebo na úrovni jednotlivých uživatelů, stejným způsobem, že jsou povoleny pro zaměstnance na plný úvazek a členy organizace. Zásady vícefaktorového ověřování se vynucují v organizaci poskytující prostředky.

Příklad:
1. Správce nebo informace pracovního procesu ve společnosti A vyzývá uživatele ze společnosti B k aplikaci *Foo* ve společnosti A.
2. Aplikace *Foo* ve společnosti je nakonfigurován A aby se vyžadovalo při přístupu.
3. Když uživatel ze společnosti B pokusí o přístup k aplikaci *Foo* ve společnosti klienta, zobrazí se výzva k dokončení výzvu vícefaktorového ověřování.
4. Uživatel můžete nastavit jejich MFA s společnosti A a vybere možnost jejich vícefaktorového ověřování.
5. Tento scénář se dá použít pro všechny identity (Azure AD nebo účet spravované služby, například, pokud uživatelé ve společnosti B ověření pomocí sociálních ID)
6. Společnost A musí mít dostatek licencí Azure AD Premium, které podporují vícefaktorového ověřování. Uživatel ze společnosti B využívá tuto licenci od společnosti A.

Pozváním klientů je zodpovědná za vícefaktorového ověřování pro uživatele od organizace partnera vždy, i v případě, že partnerské organizace má možnosti vícefaktorového ověřování.

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Nastavení vícefaktorového ověřování pro uživatele spolupráce B2B
Chcete-li zjistit, jak je snadné pro nastavení vícefaktorového ověřování pro uživatele spolupráce B2B, přečtěte si téma Jak v následujícím videu:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>MFA prostředí pro uživatele B2B nabízejí uplatnění kódu.
Podívejte se na následující animace zobrazíte uplatnění prostředí:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>MFA resetovat pro uživatele spolupráce B2B
V současné době správce můžete vyžadovat spolupráce B2B uživatelům ověření až znovu jen pomocí následující rutiny prostředí PowerShell:

1. Připojení k Azure AD

  ```
  $cred = Get-Credential
  Connect-MsolService -Credential $cred
  ```
2. Získání všech uživatelů s potvrzením až metody

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  Zde naleznete příklad:

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. Reset – metoda MFA pro konkrétního uživatele, aby vyžadovala od uživatele spolupráce B2B se znovu nastavit metody výš. Příklad:

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Proč jsme provedu MFA v klientů prostředků?

V aktuální verzi MFA je vždy v prostředku klientů, z důvodů předvídatelnost. Řekněme například, uživatel Contoso (Jan) vyzván k Fabrikam a Fabrikam se povolit MFA pro uživatele B2B.

Pokud společnosti Contoso má povolenou zásadu vícefaktorového ověřování pro App1, ale není na počítači App2, pak pokud se podíváme na Contoso MFA deklarace identity v tokenu, jsme setkat s následujícím problémem:

* Den 1: Uživatel má MFA ve společnosti Contoso a přistupuje k počítači App1 a pak žádné další MFA řádku se zobrazí ve firmě Fabrikam.

* Den 2: Uživatel přistupoval 2 aplikace ve společnosti Contoso, takže teď při přístupu k Fabrikam, se musí v MFA zaregistrovat existuje.

Tento proces může být matoucí a může vést k vyřadit v dokončených přihlášení.

Kromě toho i v případě, že Contoso má schopnost MFA, není vždy, že tento případ Fabrikam by důvěřovat zásad vícefaktorového ověřování Contoso.

Nakonec prostředků klienta MFA funguje taky pro účty spravované služby a sociálních ID a orgs partnera, které nemají MFA nastavit.

Doporučení pro MFA pro uživatele B2B je proto vždy vyžadovat vícefaktorové ověřování v pozváním klientovi. Tento požadavek může vést k dvojité vícefaktorového ověřování v některých případech, ale vždy, když přistupují k pozváním klienta, se koncovým uživatelům prostředí předvídatelný: Jan musí v MFA zaregistrovat s pozváním klienta.

### <a name="device-based-location-based-and-risk-based-conditional-access-for-b2b-users"></a>Na zařízení, na základě polohy a na základě riziko podmíněného přístupu pro uživatele s B2B

Pokud Contoso povolí zásady podmíněného přístupu na základě zařízení pro svoje firemní data, je přístup zabránila zařízení, která nejsou spravovaná ve společnosti Contoso a není kompatibilní se zásadami společnosti Contoso zařízení.

Pokud uživatel B2B zařízení nespravuje ve společnosti Contoso, zablokuje se přístup B2B uživatelů z partnerských organizací v jakémkoli kontextu tyto zásady se vynucují. Contoso však můžete vytvořit seznamy vyloučení obsahující konkrétní partnerskou uživatele z nich vyloučit ze zásad podmíněného přístupu podle zařízení.

#### <a name="location-based-conditional-access-for-b2b"></a>Na základě polohy podmíněný přístup pro B2B

Zásady podmíněného přístupu na základě umístění můžete vynutit pro B2B uživatele, pokud pozváním organizace je možné vytvořit důvěryhodný rozsah IP adres, který definuje jejich organizace partnera.

#### <a name="risk-based-conditional-access-for-b2b"></a>Podmíněný přístup využívající riziko pro B2B

V současné době přihlášení zásad založených na riziko nelze použít uživatelům B2B, protože vyhodnocení rizik se provádí na domovskou organizaci B2B uživatele.

## <a name="next-steps"></a>Další postup

Na spolupráci Azure AD B2B najdete v následujících článcích:

* [Co je spolupráce B2B ve službě Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Licencování Azure AD s B2B spolupráce](active-directory-b2b-licensing.md)
* [Spolupráce Azure Active Directory s B2B nejčastější dotazy (FAQ)](active-directory-b2b-faq.md)
