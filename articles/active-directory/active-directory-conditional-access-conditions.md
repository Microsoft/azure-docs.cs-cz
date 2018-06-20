---
title: Jaké jsou podmínky v Azure Active Directory podmíněného přístupu? | Dokumenty Microsoft
description: Zjistěte, jak používá podmínky podmíněného přístupu Azure Active Directory k aktivaci zásadu.
services: active-directory
keywords: podmíněný přístup k aplikacím, podmíněného přístupu s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 42792170593dbd94d0eae9b408c70f326891508a
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232305"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Jaké jsou podmínky v Azure Active Directory podmíněného přístupu? 

S [podmíněného přístupu Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), jak oprávněným uživatelům přístup můžete řídit cloudových aplikací. V zásadách podmíněného přístupu které definujete odpovědi ("to") důvod aktivován vaší zásady ("v takovém případě"). 

![Řízení](./media/active-directory-conditional-access-conditions/10.png)


V kontextu podmíněného přístupu:

- "**v takovém případě**" se nazývá **podmínky**. 
- "**Udělejte to**" se nazývá **přístup k ovládacím prvkům**.

Kombinace vaše podmínky s ovládacími prvky přístup představuje zásady podmíněného přístupu.

![Řízení](./media/active-directory-conditional-access-conditions/61.png)


Podmínek, které jste nenakonfigurovali v zásadách podmíněného přístupu se nepoužijí. Některé podmínky jsou [povinné](active-directory-conditional-access-best-practices.md#whats-required-to-make-a-policy-work) použít zásady podmíněného přístupu do prostředí. 

Tento článek poskytuje přehled podmínky a jak se používají v zásadách podmíněného přístupu. 

## <a name="users-and-groups"></a>Uživatelé a skupiny

Podmínka uživatelů a skupin je povinné v zásadách podmíněného přístupu. V zásadách můžete buď vyberte **všichni uživatelé** nebo vyberte konkrétní uživatele a skupiny.

![Řízení](./media/active-directory-conditional-access-conditions/111.png)

Když vyberete:

- **Všichni uživatelé**, vaše zásady platí pro všechny uživatele v adresáři. To zahrnuje uživatele typu Host.

- **Vyberte uživatele a skupiny**, můžete nastavit následující možnosti:

    - **Všechny uživatele typu Host** -umožňuje cílových zásad pro uživatele typu Host B2B. Tato podmínka odpovídá libovolného uživatelského účtu s *userType* atribut nastaven na *hosta*. Toto nastavení můžete použít v případech, kdy je zásada má být použita, jakmile je účet vytvořený v tok pozvání ve službě Azure AD.

    - **Role Directory** -Umožňuje cílit zásady podle přiřazení role uživatele. Tato podmínka podporuje role directory například *globálního správce* nebo *heslo správce*.

    - **Uživatelé a skupiny** -umožňuje cíl konkrétní skupiny uživatelů. Například můžete vybrat skupinu, která obsahuje všichni členové oddělení lidských zdrojů, když máte aplikaci HR vybrán jako cloudové aplikace.

Skupiny, může být jakýkoli typ skupiny ve službě Azure AD, včetně dynamické nebo přiřazené zabezpečení a distribučních skupin

Můžete také vyloučit konkrétní uživatele nebo skupiny ze zásady. Jeden běžný případ použití jsou účty služby, pokud vaše zásady vynucují funkci vícefaktorového ověřování (MFA). 

Cílení na konkrétní skupiny uživatelů je užitečná pro nasazení nových zásad. V nové zásady by měl cíl pouze počáteční sadu uživatelů k ověření zásad chování. 



## <a name="cloud-apps"></a>Cloudové aplikace 

Cloudové aplikace se webové stránky nebo služby. To zahrnuje webů zabezpečených službou Proxy aplikace Azure. Podrobný popis podporované cloudové aplikace, najdete v části [cloudové aplikace přiřazení](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments).    

Podmínka cloudové aplikace je povinné v zásadách podmíněného přístupu. V zásadách můžete buď vyberte **všech cloudových aplikací** nebo vyberte konkrétní aplikace.

![Řízení](./media/active-directory-conditional-access-conditions/03.png)

Můžete vybrat:

- **Všechny cloudových aplikací** směrný plán zásad má být použita k celé organizaci. Běžně používá pro tento výběr je zásada, která vyžaduje vícefaktorové ověřování při přihlašování riziko je zjištěna všech cloudových aplikací. Zásady použité **všech cloudových aplikací** platí pro přístup do všech webových stránek a služeb. Toto nastavení není omezeno na cloudové aplikace, které se zobrazují na **vyberte cloudových aplikací** seznamu.

- Jednotlivých cloudových aplikací k určitým službám cíl zásad. Například může vyžadovat uživatelé měli [zařízení kompatibilní s](active-directory-conditional-access-policy-connected-applications.md) pro přístup k SharePoint Online. Když přistupují k obsahu služby SharePoint, například Microsoft Teams je tato zásada také použitá k jiným službám. 

Můžete také vyloučit konkrétní aplikace z zásady; Tyto aplikace se však stále vztahují zásady uplatněné na službách, které chtějí získat přístup. 



## <a name="sign-in-risk"></a>Riziko přihlášení

Riziko přihlášení je indikátorem pro pravděpodobnost (vysoká, střední nebo nízká), že nebyla provedena pokus o přihlášení, legitimní vlastníkem uživatelského účtu. Azure AD vypočítá úroveň rizika přihlášení během přihlášení uživatele. Úroveň rizika počítané přihlášení můžete použít jako podmínku v zásadách podmíněného přístupu. 

![Podmínky](./media/active-directory-conditional-access-conditions/22.png)

Pokud chcete použít tuto podmínku, musíte mít [Azure Active Directory Identity Protection](active-directory-identityprotection.md) povolena.
 
Běžné případy použití pro tuto podmínku jsou zásady, které:

- Blokujte přístup pro uživatele s vysokou přihlašovací riziko potenciálně nezákonné uživatelům zabránit v přístupu k vaší cloudové aplikace. 
- Požadovat použití vícefaktorového ověřování pro uživatele s střední riziko přihlášení. Vynucování služby Multi-Factor authentication, můžete poskytnout další jistotu, že přihlášení se provádí legitimní vlastníka účtu.

Další informace najdete v tématu popisujícím [riziková přihlášení](active-directory-identityprotection.md#risky-sign-ins).  

## <a name="device-platforms"></a>Platformy zařízení

Platforma je charakterizovaná operačního systému, který běží na vašem zařízení. Azure AD identifikuje platformou pomocí informací uvedených zařízení, jako je například uživatelský agent. Protože tyto informace neověřené, doporučujeme, aby měly všechny platformy zásada použitá, a to buď blokování přístupu, požadováno dodržování zásad Intune nebo nutnosti že zařízení být připojené k doméně. Výchozí hodnota je uplatňovat zásady na všech platformách zařízení. 


![Podmínky](./media/active-directory-conditional-access-conditions/24.png)

Úplný seznam platforem podporovaných zařízení najdete v tématu [podmínku platformy zařízení](active-directory-conditional-access-technical-reference.md#device-platform-condition).


Společné pro tento stav je zásadu, která omezuje přístup na vašich cloudových aplikací pro případ použití [zařízení spravovaná](active-directory-conditional-access-policy-connected-applications.md#managed-devices). Další scénáře včetně podmínku platformy zařízení, najdete v části [podmíněného přístupu na základě aplikace Azure Active Directory](active-directory-conditional-access-mam.md).



## <a name="device-state"></a>Stav zařízení

Podmínka stavu zařízení umožňuje připojený hybridní Azure AD a zařízení označená jako kompatibilní, které se mají vyloučit ze zásad podmíněného přístupu. To je užitečné, když zásadu použít pouze na nespravovaném zařízení k zajištění zabezpečení další relace. Například pouze vynuťte ovládacího prvku relace Microsoft Cloud App Security když je zařízení nespravovaná. 


![Podmínky](./media/active-directory-conditional-access-conditions/112.png)

Pokud chcete zablokovat přístup pro nespravovaná zařízení, měli byste implementovat [podmíněného přístupu na základě zařízení](active-directory-conditional-access-policy-connected-applications.md).


## <a name="locations"></a>Umístění

Umístění máte možnost definovat podmínky, které jsou založeny na kde bylo inicializováno pokus o připojení. 
     
![Podmínky](./media/active-directory-conditional-access-conditions/25.png)

Běžné případy použití pro tuto podmínku jsou zásady, které:

- Požadovat použití vícefaktorového ověřování pro uživatele, kteří používají službu, když jsou mimo podnikovou síť.  

- Blokujte přístup pro uživatele, kteří používají službu z konkrétní zemích nebo oblastech. 

Další informace najdete v tématu [co je podmínka umístění v Azure Active Directory podmíněného přístupu?](active-directory-conditional-access-locations.md)


## <a name="client-apps"></a>Klientské aplikace

Podmínka aplikace klienta můžete použít zásady pro různé typy aplikací, jako například:

- Weby a služby
- Mobilní aplikace a aplikací klasické pracovní plochy. 



Aplikace se označuje jako:

- Webové stránky nebo služby, pokud používá webové protokoly jednotné přihlašování, SAML, WS-Fed nebo OpenID Connect pro důvěrné klienta.

- A mobilní aplikace nebo plochy aplikace, pokud mobilní aplikace, OpenID Connect používá pro nativní klienta.

Úplný seznam klientských aplikací v požadované zásady podmíněného přístupu můžete použít, najdete v části [klienta aplikace podmínky](active-directory-conditional-access-technical-reference.md#client-apps-condition) v technické dokumentaci Azure Active Directory podmíněného přístupu.

Běžné případy použití pro tuto podmínku jsou zásady, které:

- Vyžadovat [zařízení kompatibilní s](active-directory-conditional-access-policy-connected-applications.md) mobilních a desktopových aplikací, které stáhnout velké objemy dat a zařízení současně přístup z prohlížeče z libovolného zařízení.

- Blokovat přístup z webových aplikací, ale povolení přístupu z mobilních a desktopových aplikací.

Kromě používat protokoly moderní ověřování a jednotné přihlašování k webu, můžete použít tuto podmínku pro e-mailové aplikace, které používají protokol Exchange ActiveSync, jako je nativní e-mailové aplikace na většině smartphony. V současné době klientské aplikace pomocí starší verze protokolů je potřeba zabezpečit pomocí služby AD FS.

Tuto podmínku lze vybrat pouze pokud **Office 365 Exchange Online** je pouze cloudové aplikace, které jste vybrali.

![Cloudové aplikace](./media/active-directory-conditional-access-conditions/32.png)

Výběr **Exchange ActiveSync** jako klientských aplikací podmínka je podporována pouze pokud nemáte další podmínky v zásada nakonfigurovaná. Můžete však zúžit obor Tento stav se vztahují jenom na podporovaných platformách.

 
![Podporované platformy](./media/active-directory-conditional-access-conditions/33.png)

Použití tuto podmínku jenom na podporovaných platformách se o ekvivalent ve všech platformách zařízení [podmínku platformy zařízení](active-directory-conditional-access-conditions.md#device-platforms).

![Podporované platformy](./media/active-directory-conditional-access-conditions/34.png)


 Další informace naleznete v tématu:

- [Nastavení služby SharePoint Online a Exchange Online pro Azure Active Directory podmíněného přístupu](active-directory-conditional-access-no-modern-authentication.md)
 
- [Azure Active Directory, na základě aplikace podmíněného přístupu](active-directory-conditional-access-mam.md) 


### <a name="legacy-authentication"></a>Starší verze ověřování  

Podmíněný přístup se teď platí pro starší klienty Office, které nepodporují moderní ověřování, jakož i klienty, kteří používají protokoly e-mailu POP, IMAP, SMTP, atd. To umožňuje konfigurovat zásady jako **blokovat přístup z jiných klientů**.


![Starší verze ověřování](./media/active-directory-conditional-access-conditions/160.png)
 



#### <a name="known-issues"></a>Známé problémy

- Konfigurace zásad pro **ostatní klienty** blokuje z určité klientů jako SPConnect v celé organizaci. Toto je z důvodu těchto starších klientů ověřování neočekávaným způsobem. Tento problém se nevztahuje na hlavní aplikace Office jako starší klienty Office. 

- Může trvat až 24 hodin pro zásady tak, aby vstoupila v platnost. 


#### <a name="frequently-asked-questions"></a>Nejčastější dotazy

**To bude blokovat webových služeb systému Exchange (EWS)?**

To závisí na ověřovací protokol, který používá EWS. Pokud aplikace EWS používá moderní ověřování, bude o něm zmínka pomocí klientské aplikace "mobilní aplikace a klienti vzdálené plochy". Pokud aplikace EWS používá základní ověřování, bude možné předmětem "Ostatní klienty" klientské aplikace.


**Jaké ovládací prvky můžete použít pro ostatní klienty**

Libovolný ovládací prvek mohou být konfigurovány pro "Ostatní klienty". Činnost koncového uživatele, ale bude blokovat přístup pro všechny případy. "Ostatní klienty" nepodporují ovládací prvky jako MFA, kompatibilní zařízení, připojení k doméně, atd. 
 
**Jaké podmínky lze použít pro ostatní klienty?**

Veškeré podmínky lze nakonfigurovat pro "Ostatní klienty".

**Podporuje protokolu Exchange ActiveSync všech podmínek a ovládací prvky?**

Ne. Zde je souhrn podporu protokolu Exchange ActiveSync (EAS):

- EAS podporuje pouze uživatele a skupiny cílení. Nepodporuje hosta, role. Pokud je nakonfigurovaná podmínku hosta nebo role, všichni uživatelé získat zablokuje, vzhledem k tomu, že jsme nemůže zjistit, pokud mají zásady platit pro uživatele, nebo ne.

- EAS pracuje pouze s Exchange jako cloudové aplikace. 

- EAS nepodporuje žádné podmínky s výjimkou klientské aplikace.

- EAS se dá nakonfigurovat s libovolný ovládací prvek (všechny kromě dodržování předpisů zařízení povede k bloku).

**Se zásady vztahují na všechny klientské aplikace ve výchozím nastavení do budoucna?**

Ne. Neexistuje žádná změna v chování výchozí zásady. Zásady pokračuje v používání prohlížeče a mobilní aplikace nebo plochy klientům ve výchozím nastavení.



## <a name="next-steps"></a>Další postup

- Pokud chcete vědět, jak konfigurovat zásadu podmíněného přístupu, najdete v článku [vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](active-directory-conditional-access-app-based-mfa.md).

- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro prostředí, najdete v článku [osvědčené postupy pro podmíněný přístup v Azure Active Directory](active-directory-conditional-access-best-practices.md). 

