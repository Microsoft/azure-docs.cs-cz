---
title: Jaké jsou podmínky podmíněného přístupu Azure Active Directory? | Dokumenty Microsoft
description: Zjistěte, jak se používají podmínky podmíněného přístupu Azure Active Directory k aktivaci zásad.
services: active-directory
keywords: podmíněný přístup pro aplikace, podmíněný přístup s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu
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
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "36232305"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Jaké jsou podmínky podmíněného přístupu Azure Active Directory? 

S [podmíněného přístupu Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), jak ověřeného přístupu uživatele můžete řídit vašich cloudových aplikacích. V zásadách podmíněného přístupu definujte odpověď ("to") důvod Aktivace zásady ("Když taková situace nastane"). 

![Řízení](./media/active-directory-conditional-access-conditions/10.png)


V kontextu podmíněného přístupu:

- "**v takovém případě**" se nazývá **podmínky**. 
- "**Proveďte to**" se nazývá **ovládací prvky přístupu**.

Kombinací podmínek s ovládacími prvky přístup představuje zásad podmíněného přístupu.

![Řízení](./media/active-directory-conditional-access-conditions/61.png)


Podmínky, které jste nenakonfigurovali v zásadách podmíněného přístupu se nepoužívají. Některé podmínky jsou [povinné](active-directory-conditional-access-best-practices.md#whats-required-to-make-a-policy-work) použít zásady podmíněného přístupu do prostředí. 

Tento článek obsahuje přehled podmínek a jak se používají zásady podmíněného přístupu. 

## <a name="users-and-groups"></a>Uživatelé a skupiny

Stav uživatele a skupiny je povinný v zásadách podmíněného přístupu. V zásadách můžete buď vybrat **všichni uživatelé** nebo vyberte konkrétní uživatele a skupiny.

![Řízení](./media/active-directory-conditional-access-conditions/111.png)

Když vyberete:

- **Všichni uživatelé**, vaše zásady platí pro všechny uživatele v adresáři. To zahrnuje uživatele typu Host.

- **Vyberte uživatele a skupiny**, můžete nastavit následující možnosti:

    - **Všichni uživatelé typu Host** – Umožňuje cílit na zásady pro uživatele typu Host B2B. Tato podmínka odpovídá libovolný uživatelský účet s *userType* atribut nastaven na *hosta*. Toto nastavení můžete použít v případech, kdy je zásada použije jako účet je vytvořen v pozvání tok, který ve službě Azure AD.

    - **Role adresáře** – Umožňuje cílit zásady založené na přiřazení role uživatele. Tato podmínka podporuje role adresáře, jako *globálního správce* nebo *správce hesel*.

    - **Uživatelé a skupiny** -vám umožní cílit na konkrétní sady uživatelů. Například můžete vybrat skupinu, která obsahuje všichni členové oddělení lidských zdrojů, když máte aplikaci HR jako cloudovou aplikaci vybrali.

Skupiny, může být libovolný typ skupiny ve službě Azure AD, včetně dynamické nebo přiřazené zabezpečení a distribučních skupin

Konkrétní uživatelé nebo skupiny můžete také vyloučit ze zásad. Jeden běžným případem použití jsou účty služeb, pokud vaše zásada vynucuje ověřování službou Multi-Factor Authentication (MFA). 

Cílení na konkrétní skupiny uživatelů slouží k nasazení nových zásad. V nové zásady by měl cílíte pouze počáteční sadu uživatelů k ověření zásad chování. 



## <a name="cloud-apps"></a>Cloudové aplikace 

Cloudové aplikace je webové stránky nebo služby. To zahrnuje webové servery chráněné službou Proxy aplikací Azure. Podrobný popis podporovaných cloudových aplikací, najdete v části [přiřazení aplikací v cloudu](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments).    

Podmínka cloudové aplikace je povinný v zásadách podmíněného přístupu. V zásadách můžete buď vybrat **všechny cloudové aplikace** nebo vybrat konkrétní aplikace.

![Řízení](./media/active-directory-conditional-access-conditions/03.png)

Můžete vybrat:

- **Všechny cloudové aplikace** směrný plán zásad se použijí pro celou organizaci. Běžným případem použití pro tento výběr je zásada, která vyžaduje vícefaktorové ověřování při zjištění rizika přihlašování všech cloudových aplikací. Použít zásady **všechny cloudové aplikace** platí pro přístup do všech webových stránek a služeb. Toto nastavení není omezena pouze na cloudové aplikace, které se zobrazují na **vyberte cloudové aplikace** seznamu.

- Jednotlivých cloudových aplikací ke konkrétním službám cílové zásady. Třeba, můžete vyžadovat, aby uživatelé měli [vyhovující zařízení](active-directory-conditional-access-policy-connected-applications.md) pro přístup k Sharepointu Online. Tyto zásady platí také pro jiné služby při přístupu k obsahu na Sharepointu, třeba Microsoft Teams. 

Konkrétní aplikace můžete také vyloučit ze zásad; Tyto aplikace jsou však stále můžou zásady aplikované na služby, ke kterým mají přístup. 



## <a name="sign-in-risk"></a>Riziko přihlášení

Riziko přihlášení je indikátorem pravděpodobnost (vysoká, střední nebo nízká), že pokus o přihlášení nebyl prováděné legitimním vlastníkem uživatelského účtu. Azure AD vypočítá úroveň rizika přihlášení při přihlašování uživatele. Úroveň rizika počítané přihlášení můžete použít jako podmínka v zásadách podmíněného přístupu. 

![Podmínky](./media/active-directory-conditional-access-conditions/22.png)

Pokud chcete použít tuto podmínku, musíte mít [Azure Active Directory Identity Protection](active-directory-identityprotection.md) povolena.
 
Běžné případy použití pro tuto podmínku jsou zásady, které:

- Zablokuje uživatelům s rizikem velký počet uživatelů v potenciálně nezákonné uživatelům zabránit v přístupu k vašim cloudovým aplikacím. 
- Vyžadovat vícefaktorové ověřování pro uživatele se středním rizikem přihlášení. Vynucování služby Multi-Factor authentication, můžete poskytnout máte ještě větší jistotu, že přihlášení se provádí pomocí legitimním vlastníkem účtu.

Další informace najdete v tématu popisujícím [riziková přihlášení](active-directory-identityprotection.md#risky-sign-ins).  

## <a name="device-platforms"></a>Platformy zařízení

Platforma zařízení je charakterizovaná operačního systému, na kterém běží na vašem zařízení. Azure AD označuje platformu pomocí informací uvedených v zařízení, jako je například uživatelského agenta. Protože tyto informace neověřené, doporučuje se, že všechny platformy mají zásady použité k nim, buď blokuje přístup, vyžadující dodržování předpisů pomocí zásad Intune nebo vyžadující že zařízení být připojené k doméně. Výchozí hodnota je uplatňovat zásady na všech platformách zařízení. 


![Podmínky](./media/active-directory-conditional-access-conditions/24.png)

Úplný seznam platforem podporovaných zařízení najdete v tématu [podmínku platformy zařízení](active-directory-conditional-access-technical-reference.md#device-platform-condition).


Běžný případ použití pro tento stav je zásada, která omezuje přístup k vašim cloudovým aplikacím na [spravovaných zařízeních](active-directory-conditional-access-policy-connected-applications.md#managed-devices). Další scénáře, včetně stavu platformy zařízení, najdete v části [podmíněného přístupu na základě aplikace Azure Active Directory](active-directory-conditional-access-mam.md).



## <a name="device-state"></a>Stav zařízení

Podmínku stavu zařízení umožňuje připojená k hybridní službě Azure AD a zařízení označené jako vyhovující, které se mají vyloučit ze zásad podmíněného přístupu. To je užitečné, když zásady by se měly používat jenom na nespravovaném zařízení pro další relaci zabezpečení. Například pouze vynutit řízení relace Microsoft Cloud App Security po nespravovaných zařízení. 


![Podmínky](./media/active-directory-conditional-access-conditions/112.png)

Pokud chcete blokovat přístup pro nespravovaná zařízení, měli byste implementovat [podmíněného přístupu podle zařízení](active-directory-conditional-access-policy-connected-applications.md).


## <a name="locations"></a>Umístění

Díky umístění máte možnost definovat podmínky, které jsou založeny na odkud byl pokus o připojení. 
     
![Podmínky](./media/active-directory-conditional-access-conditions/25.png)

Běžné případy použití pro tuto podmínku jsou zásady, které:

- Vyžadovat vícefaktorové ověřování pro uživatele, kteří používají službu, když jsou mimo firemní síť.  

- Blokovat přístup pro uživatele, kteří používají službu z určitých zemích nebo oblastech. 

Další informace najdete v tématu [co je podmínka umístění podmíněného přístupu Azure Active Directory?](active-directory-conditional-access-locations.md)


## <a name="client-apps"></a>Klientské aplikace

Stav klienta aplikace umožňuje použít zásady pro různé typy aplikací, jako například:

- Weby a služby
- Mobilní aplikace a desktopové aplikace. 



Aplikace je klasifikován tak:

- Webové stránky nebo služby, pokud používá protokoly webové jednotné přihlašování, SAML, WS-Fed nebo OpenID Connect pro důvěrnému klientovi.

- A mobilní aplikace nebo aplikace klasické pracovní plochy, pokud pro nativní klient použije mobilní aplikaci, OpenID Connect.

Úplný seznam klientských aplikací, můžete použít ve své zásady podmíněného přístupu najdete v tématu [klientské aplikace podmínku](active-directory-conditional-access-technical-reference.md#client-apps-condition) v technické dokumentaci Azure Active Directory podmíněného přístupu.

Běžné případy použití pro tuto podmínku jsou zásady, které:

- Vyžadovat [vyhovující zařízení](active-directory-conditional-access-policy-connected-applications.md) pro mobilních a desktopových aplikací, které stahovat velké objemy dat do zařízení, zároveň vám umožní přístup z prohlížeče z libovolného zařízení.

- Blokovat přístup z webových aplikací, ale povolit přístup z mobilních a desktopových aplikací.

Kromě použití jednotné přihlašování k webu a protokoly pro moderní ověřování, můžete použít tuto podmínku e-mailové aplikace, které používají Exchange ActiveSync, stejně jako nativní e-mailové aplikace na většině smartphony. V současné době klientské aplikace pomocí protokolů pro starší verze musí být zabezpečené pomocí služby AD FS.

Tuto podmínku lze vybrat pouze v případě **Office 365 Exchange Online** je jediná Cloudová aplikace, které jste vybrali.

![Cloudové aplikace](./media/active-directory-conditional-access-conditions/32.png)

Výběr **Exchange ActiveSync** jako klientské aplikace. podmínka se podporuje jenom Pokud nemáte jiné podmínky v zásadě nakonfigurované. Však můžete zúžit rozsah tento stav se vztahují jenom na podporovaných platformách.

 
![Podporované platformy](./media/active-directory-conditional-access-conditions/33.png)

Použití této podmínce jenom na podporovaných platformách se o ekvivalent všechny platformy zařízení v [podmínku platformy zařízení](active-directory-conditional-access-conditions.md#device-platforms).

![Podporované platformy](./media/active-directory-conditional-access-conditions/34.png)


 Další informace naleznete v tématu:

- [Nastavení Sharepointu Online a Exchange Online pro podmíněný přístup Azure Active Directory](active-directory-conditional-access-no-modern-authentication.md)
 
- [Azure Active Directory podmíněného přístupu k aplikacím](active-directory-conditional-access-mam.md) 


### <a name="legacy-authentication"></a>Starší verze ověřování  

Podmíněný přístup se nyní vztahuje na starší klienti Office, které nepodporují moderní ověřování, stejně jako klienti, kteří používají protokoly e-mailu jako POP, IMAP, SMTP atd. Díky tomu můžete ke konfiguraci zásad, například **blokovat přístup z jiných klientů**.


![Starší verze ověřování](./media/active-directory-conditional-access-conditions/160.png)
 



#### <a name="known-issues"></a>Známé problémy

- Konfigurace zásad pro **ostatní klienty** blokuje určité klientů jako SPConnect v celé organizaci. To je způsobeno těchto starších klientů ověřování neočekávaným způsobem. Tento problém se nevztahují na hlavní aplikace Office, jako je starší klienti Office. 

- Může trvat až 24 hodin zásad se projeví. 


#### <a name="frequently-asked-questions"></a>Nejčastější dotazy

**To bude blokovat webových služeb systému Exchange (EWS)?**

To závisí na ověřovací protokol, který používá EWS. Pokud aplikace EWS používá moderní ověřování, bude se vztahuje klientskou aplikaci "mobilní aplikace a desktopoví klienti". Pokud aplikace EWS používá základní ověřování, bude se vztahuje "Ostatní klienty" klientské aplikace.


**Jaké ovládací prvky můžete použít pro ostatní klienty**

Všechny ovládací prvky lze nastavit pro "Ostatní klienty". Činnost koncového uživatele, ale bude blokovat přístup pro všechny případy. "Další klienti" nepodporují ovládacích prvků jako je vícefaktorové ověřování, vyhovující zařízení, připojení k doméně, atd. 
 
**Jaké podmínky můžete použít pro ostatní klienty?**

Žádné podmínky lze nastavit pro "Ostatní klienty".

**Exchange ActiveSync podporuje všechny ovládací prvky a podmínky?**

Ne. Tady je Přehled podpory protokolu Exchange ActiveSync (EAS):

- EAS podporuje pouze uživatele a cílí na skupiny. Nepodporuje hosta, role. Pokud je nakonfigurovaný stav hosta/role, všichni uživatelé získat zablokuje, protože nemůžeme určit, pokud mají zásady platit pro uživatele, nebo ne.

- EAS funguje jenom s výměnou jako cloudové aplikace. 

- Jakoukoli podmínku s výjimkou sama klientské aplikace EAS není podporována.

- U každého ovládacího prvku (vše kromě dodržování předpisů zařízením povede k bloku) lze nakonfigurovat EAS.

**Zásady se vztahují na všechny klientské aplikace. ve výchozím nastavení do budoucna?**

Ne. Není žádná změna v chování výchozí zásady. Zásady pokračovat v používání prohlížeče a mobilní aplikace nebo desktopu klientům ve výchozím nastavení.



## <a name="next-steps"></a>Další postup

- Pokud chcete vědět, jak nakonfigurovat zásady podmíněného přístupu najdete v tématu [vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](active-directory-conditional-access-app-based-mfa.md).

- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro vaše prostředí, najdete v článku [osvědčené postupy pro podmíněný přístup ve službě Azure Active Directory](active-directory-conditional-access-best-practices.md). 

