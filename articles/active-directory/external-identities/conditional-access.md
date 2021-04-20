---
title: Podmíněný přístup pro uživatele spolupráce B2B – Azure AD
description: Naučte se vymáhat zásady služby Multi-Factor Authentication pro Azure Active Directory uživatelů B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22a5388d15b18180539eb95990a29f7ddf4f1951
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739543"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Podmíněný přístup pro uživatele spolupráce B2B

Tento článek popisuje, jak můžou organizace nastavit zásady podmíněného přístupu (CA) pro uživatele typu Host B2B k přístupu ke svým prostředkům.
>[!NOTE]
>Tento tok ověřování nebo autorizace je trochu odlišný pro uživatele typu Host, než pro stávající uživatele tohoto zprostředkovatele identity (IdP).

## <a name="authentication-flow-for-b2b-guest-users-from-an-external-directory"></a>Tok ověřování pro uživatele typu Host B2B z externího adresáře

Následující diagram znázorňuje tok: ![ obrázek znázorňuje tok ověřování pro uživatele typu Host B2B z externího adresáře.](./media/conditional-access-b2b/authentication-flow-b2b-guests.png)

| Krok | Description |
|--------------|-----------------------|
| 1. | Uživatel typu Host B2B požaduje přístup k prostředku. Prostředek přesměruje uživatele do svého tenanta prostředků, důvěryhodného IdP.|
| 2. | Tenant prostředků identifikuje uživatele jako externí a přesměruje uživatele na IdP uživatele typu Host B2B. Uživatel provede primární ověřování v IdP.
| 3. | Uživatel typu hosta B2B IdP vydá token uživateli. Uživatel se přesměruje zpátky na tenanta prostředků s tokenem. Tenant ověřuje token a pak vyhodnocuje uživatele proti zásadám certifikační autority. Tenant prostředků může například vyžadovat, aby uživatel prováděl Multi-Factor Authentication Azure Active Directory (AD).
| 4. | Jakmile jsou splněné všechny zásady certifikační autority tenanta, tenant prostředku vydá svůj vlastní token a přesměruje uživatele na jeho prostředek.

## <a name="authentication-flow-for-b2b-guest-users-with-one-time-passcode"></a>Tok ověřování pro uživatele typu Host B2B s jedním časovým účtem

Následující diagram znázorňuje tok: ![ obrázek znázorňuje tok ověřování pro uživatele typu Host B2B s jedním časovým účtem.](./media/conditional-access-b2b/authentication-flow-b2b-guests-otp.png)

| Krok | Description |
|--------------|-----------------------|
| 1. |Uživatel požaduje přístup k prostředku v jiném tenantovi. Prostředek přesměruje uživatele do svého tenanta prostředků, důvěryhodného IdP.|
| 2. | Tenant prostředků identifikuje uživatele jako [externí e-mailové heslo (jednorázového hesla)](./one-time-passcode.md) a pošle mu E-mail s jednorázovým heslem.|
| 3. | Uživatel načte jednorázové heslo a odešle kód. Tenant prostředků vyhodnocuje uživatele proti zásadám certifikační autority.
| 4. | Jakmile jsou všechny zásady certifikační autority splněné, tenant prostředku vydá token a přesměruje uživatele na jeho prostředek. |

>[!NOTE]
>Pokud je uživatel z externího tenanta prostředků, není možné vyhodnotit také zásady certifikační autority IdP uživatele typu Host pro B2B. Od dnešního dne platí pro své hosty jenom zásady certifikační autority tenanta.

## <a name="azure-ad-multi-factor-authentication-for-b2b-users"></a>Multi-Factor Authentication Azure AD pro uživatele B2B

Organizace můžou vyhovět více zásadám Azure AD Multi-Factor Authentication pro své uživatele typu Host B2B. Tyto zásady je možné vyhovět na úrovni tenanta, aplikace nebo uživatele stejným způsobem, jakým jsou povolené pro zaměstnance a členy organizace v plném čase.
Pro uživatele, kteří jsou pro Azure AD Multi-Factor Authentication, je vždycky zodpovědný za uživatele, i když má organizace uživatele typu Host možnost Multi-Factor Authentication. Tady je příklad –

1. Správce nebo informační pracovník ve společnosti s názvem Fabrikam pozve uživatele z jiné společnosti s názvem contoso, aby používal své Woodgrove aplikace.

2. Aplikace Woodgrove ve společnosti Fabrikam je nakonfigurovaná tak, aby vyžadovala Azure AD Multi-Factor Authentication při přístupu.

3. Když se uživatel typu Host B2B od společnosti Contoso pokusí získat přístup k Woodgrove v tenantovi Fabrikam, zobrazí se jim výzva k dokončení výzvy Azure AD Multi-Factor Authentication.

4. Uživatel typu Host pak může nastavit Multi-Factor Authentication služby Azure AD pomocí společnosti Fabrikam a vybrat možnosti.

5. Tento scénář je vhodný pro jakoukoli identitu – Azure AD nebo osobní účet Microsoft (MSA). Například pokud se uživatel v contoso ověřuje pomocí sociálního ID.

6. Společnost Fabrikam musí mít dostatečný nárok na licence Azure AD, které podporují Multi-Factor Authentication služby Azure AD. Uživatel od společnosti Contoso pak tuto licenci spotřebuje od společnosti Fabrikam. Informace o licencích B2B najdete v tématu [Model fakturace pro externí identity Azure AD](./external-identities-pricing.md) .

>[!NOTE]
>Multi-Factor Authentication Azure AD se provádí v architektuře prostředků, aby se zajistila předvídatelnost. Když se uživatel typu Host přihlásí, uvidí přihlašovací stránku tenanta prostředků zobrazenou na pozadí a vlastní přihlašovací stránku a logo společnosti na svém domovském klientovi.

### <a name="set-up-azure-ad-multi-factor-authentication-for-b2b-users"></a>Nastavení Multi-Factor Authentication Azure AD pro uživatele B2B

Pokud chcete nastavit Multi-Factor Authentication Azure AD pro uživatele spolupráce B2B, podívejte se na toto video:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-azure-ad-multi-factor-authentication-for-offer-redemption"></a>Uživatelé B2B Azure AD Multi-Factor Authentication pro vyplacení nabídky

Pokud se chcete dozvědět víc o prostředí služby Azure AD Multi-Factor Authentication pro vyplacení, podívejte se na toto video:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="azure-ad-multi-factor-authentication-reset-for-b2b-users"></a>Resetování služby Azure AD Multi-Factor Authentication pro uživatele B2B

Nyní jsou k dispozici následující rutiny PowerShellu pro kontrolu nad uživateli typu Host B2B:

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

3. Resetování metody Multi-Factor Authentication služby Azure AD pro konkrétního uživatele, který bude vyžadovat, aby uživatel spolupráce B2B znovu nastavil metody kontroly pravopisu. 
   Tady je příklad:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

## <a name="conditional-access-for-b2b-users"></a>Podmíněný přístup pro uživatele B2B

Existují různé faktory, které mají vliv na zásady certifikační autority pro uživatele typu Host B2B.

### <a name="device-based-conditional-access"></a>Podmíněný přístup založený na zařízeních

V certifikační autoritě je k dispozici možnost vyžadovat, [aby zařízení uživatele vyhovovalo předpisům nebo byl připojen k hybridní službě Azure AD](../conditional-access/concept-conditional-access-conditions.md#device-state-preview). Uživatelé typu Host B2B můžou splnit dodržování předpisů jenom v případě, že je v tenantovi prostředků možné spravovat svoje zařízení. Zařízení se nedají spravovat v jednom okamžiku více než jedné organizaci. Uživatelé typu Host B2B nemůžou splnit připojení k hybridní službě Azure AD, protože nemají místní účet služby AD. Jenom v případě, že je zařízení uživatele typu Host nespravované, může zaregistrovat nebo zaregistrovat svoje zařízení v tenantovi prostředku a pak zajistit, aby zařízení odpovídalo. Uživatel pak může splnit řízení udělení.

>[!Note]
>Nedoporučujeme vyžadovat spravované zařízení pro externí uživatele.

### <a name="mobile-application-management-policies"></a>Zásady správy mobilních aplikací

Certifikační autorita udělí ovládací prvky, jako je **třeba vyžadovat schválené klientské aplikace** a **vyžadují zásady ochrany aplikací** , musí být zařízení zaregistrované v tenantovi. Tyto ovládací prvky se dají použít jenom pro [zařízení s iOS a Androidem](../conditional-access/concept-conditional-access-conditions.md#device-platforms). Ani jeden z těchto ovládacích prvků nelze použít u uživatelů typu Host B2B, pokud je zařízení uživatele již spravováno jinou organizací. Mobilní zařízení nelze zaregistrovat ve více než jednom klientovi současně. Pokud je mobilní zařízení spravováno jinou organizací, bude uživatel zablokován. Jenom v případě, že je zařízení uživatele typu Host nespravované, může zaregistrovat svoje zařízení v tenantovi prostředků. Uživatel pak může splnit řízení udělení.  

>[!NOTE]
>Nedoporučujeme vyžadovat zásady ochrany aplikací pro externí uživatele.

### <a name="location-based-conditional-access"></a>Podmíněný přístup na základě umístění

[Zásady založené na umístěních](../conditional-access/concept-conditional-access-conditions.md#locations) založené na ROZSAHech IP adres je možné vyhovět, pokud může organizace vytvořit rozsah důvěryhodných IP adres, který definuje své partnerské organizace.

Zásady je také možné vyhovět na základě **geografických umístění**.

### <a name="risk-based-conditional-access"></a>Podmíněný přístup založený na riziku

[Zásady rizik přihlašování](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk) se vynutily, pokud uživatel typu Host B2B splní řízení grantu. Organizace může například vyžadovat Multi-Factor Authentication Azure AD pro střední nebo vysoké riziko přihlašování. Pokud se ale uživatel dřív nezaregistroval pro Multi-Factor Authentication služby Azure AD v tenantovi prostředků, uživatel se zablokuje. K tomu je potřeba zabránit uživatelům se zlými úmysly v registraci vlastních přihlašovacích údajů služby Azure AD Multi-Factor Authentication v případě, že budou ohrozit heslo legitimního uživatele.

[Zásady rizik uživatelů](../conditional-access/concept-conditional-access-conditions.md#user-risk) se v tenantovi prostředků ale nedají přeložit. Pokud například požadujete změnu hesla pro vysoce rizikové uživatele typu Host, budou blokovány z důvodu nemožnostho resetování hesel v adresáři prostředků.

### <a name="conditional-access-client-apps-condition"></a>Podmínka pro klientské aplikace podmíněného přístupu

[Podmínky klientských aplikací](../conditional-access/concept-conditional-access-conditions.md#client-apps) se chovají stejně pro uživatele typu Host B2B jako u jakéhokoli jiného typu uživatele. Uživatelům typu Host můžete například zabránit v používání starších ověřovacích protokolů.

### <a name="conditional-access-session-controls"></a>Ovládací prvky relace podmíněného přístupu

[Ovládací prvky relace](../conditional-access/concept-conditional-access-session.md) se chovají stejně pro uživatele typu Host B2B jako u jakéhokoli jiného typu uživatele.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích o spolupráci Azure AD B2B:

- [Co je spolupráce B2B ve službě Azure AD?](./what-is-b2b.md)
- [Ochrana identit a uživatelé B2B](../identity-protection/concept-identity-protection-b2b.md)
- [Externí identity – ceny](https://azure.microsoft.com/pricing/details/active-directory/)
- [Nejčastější dotazy](./faq.md)
