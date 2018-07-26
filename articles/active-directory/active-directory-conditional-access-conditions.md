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
ms.openlocfilehash: 59ae7e83bc2b76cb679b0baf1f5739f28ec7046c
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248276"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Jaké jsou podmínky podmíněného přístupu Azure Active Directory? 

Přístup můžete řídit jak Autorizovaní uživatelé vašich cloudových aplikací pomocí [podmíněného přístupu Azure Active Directory (Azure AD)](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal). V zásadách podmíněného přístupu definujte odpověď důvod pro spouštění vašich zásad. Příklad odpovědi je **proveďte to**. Z důvodu příklad je **v takovém případě**.

![Důvod a odpovědi](./media/active-directory-conditional-access-conditions/10.png)


V kontextu podmíněného přístupu **v takovém případě** se volá **podmínku**. **Proveďte to** je volána **řízení přístupu**. Kombinací podmínek a ovládacích prvků přístupu představuje zásad podmíněného přístupu.

![Zásady podmíněného přístupu](./media/active-directory-conditional-access-conditions/61.png)


Podmínky, které jste nenakonfigurovali v zásadách podmíněného přístupu se nepoužívají. Některé podmínky jsou [povinné](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-best-practices) použít zásady podmíněného přístupu do prostředí. 

Tento článek představuje přehled podmínek a jak se používají zásady podmíněného přístupu. 

## <a name="users-and-groups"></a>Uživatelé a skupiny

Stav uživatele a skupiny je povinný v zásadách podmíněného přístupu. V zásadách můžete buď vybrat **všichni uživatelé** nebo vyberte konkrétní uživatele a skupiny.

![Uživatelé a skupiny](./media/active-directory-conditional-access-conditions/111.png)

Když vyberete **všichni uživatelé**, vaše zásady platí pro všechny uživatele v adresáři, včetně uživatelů typu Host.

Pokud jste **výběr uživatelů a skupin**, můžete nastavit následující možnosti:

* **Všichni uživatelé typu Host** cílí zásady pro uživatele typu Host B2B. Tato podmínka odpovídá uživatelský účet, který má **userType** atribut nastaven na **hosta**. Toto nastavení můžete použít, když zásady potřeba použít jako účet je vytvořen v pozvání tok, který ve službě Azure AD.

* **Role adresáře** cílí zásady založené na přiřazení role uživatele. Tato podmínka podporuje role adresáře jako **globálního správce** nebo **správce hesel**.

* **Uživatelé a skupiny** cílí na konkrétní skupiny uživatelů. Například můžete vybrat skupinu, která obsahuje všichni členové oddělení lidských zdrojů, pokud je vybrána HR aplikace jako cloudové aplikace. Skupina může být libovolný typ skupiny ve službě Azure AD, včetně dynamické nebo přiřazené zabezpečení a distribučních skupin.

Konkrétní uživatelé nebo skupiny můžete také vyloučit ze zásad. Jeden běžným případem použití je účty služeb, pokud vaše zásada vynucuje vícefaktorové ověřování (MFA). 

Cílení na konkrétní skupiny uživatelů slouží k nasazení nových zásad. V nové zásady by měl cílíte pouze počáteční sadu uživatelů k ověření zásad chování. 



## <a name="cloud-apps"></a>Cloudové aplikace 

Cloudové aplikace je webu nebo služby. Weby, které jsou chráněné službou Azure AD Application Proxy jsou také cloudových aplikací. Podrobný popis podporovaných cloudových aplikací, najdete v části [přiřazení aplikací v cloudu](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-technical-reference#cloud-apps-assignments). 

**Cloudové aplikace** podmínka je povinný v zásadách podmíněného přístupu. V zásadách můžete buď vybrat **všechny cloudové aplikace** nebo vybrat konkrétní aplikace.

![Zahrnout cloudových aplikací](./media/active-directory-conditional-access-conditions/03.png)

- Vyberte **všechny cloudové aplikace** do směrného plánu zásad platí pro celou organizaci. Tento výběr použijte pro zásady, které vyžadují vícefaktorové ověřování při zjištění rizika přihlašování všech cloudových aplikací. Použít zásady **všechny cloudové aplikace** platí pro přístup ke všem webům a službám. Toto nastavení se neomezuje na cloudové aplikace, které se zobrazují na **vyberte aplikace** seznamu. 

- Vyberte jednotlivé cloudové aplikace ke konkrétním službám cílové zásady. Třeba, můžete vyžadovat, aby uživatelé měli [vyhovující zařízení](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online) pro přístup k Sharepointu Online. Tyto zásady platí také pro jiné služby při přístupu k obsahu na Sharepointu. Příkladem je Microsoft Teams. 

Na základě zásady můžete vyloučit konkrétní aplikace. Tyto aplikace jsou však stále můžou zásady aplikované na služby, ke kterým mají přístup. 



## <a name="sign-in-risk"></a>Riziko přihlášení

Riziko přihlášení je indikátorem vysoká, střední nebo nízká pravděpodobnost, že pokus o přihlášení nebyl provedené legitimním vlastníkem uživatelského účtu. Azure AD se vypočítá že úroveň rizika přihlášení, když uživatel společnosti přihlásit. Úroveň rizika počítané přihlášení může být podmínka v zásadách podmíněného přístupu. 

![Úrovně rizik přihlašování](./media/active-directory-conditional-access-conditions/22.png)

Pokud chcete použít tuto podmínku, musíte mít [Azure Active Directory Identity Protection](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-identityprotection-enable) povolena.
 
Běžné případy použití pro tuto podmínku jsou zásady, které mají následující ochrany: 

- Zablokuje uživatelům s rizikem velký počet uživatelů se změnami. Tato ochrana potenciálně nezákonné uživatelům brání v přístupu k vašim cloudovým aplikacím. 
- Vyžadovat vícefaktorové ověřování pro uživatele se středním rizikem přihlášení. Vynucení vícefaktorového ověřování, můžete zadat máte ještě větší jistotu, že přihlášení se provádí legitimním vlastníkem účtu.

Další informace najdete v tématu popisujícím [riziková přihlášení](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-security-risky-sign-ins).  

## <a name="device-platforms"></a>Platformy zařízení

Platforma zařízení je charakterizovaná operačního systému, na kterém běží na vašem zařízení. Azure AD označuje platformu pomocí informací uvedených v zařízení, jako je například uživatelského agenta. Tyto informace neověřené. Doporučujeme, aby zásady použité k nim všechny platformy. Zásady by měla blokovat přístup, vyžadovat dodržování předpisů pomocí zásad Microsoft Intune nebo vyžadují že zařízení být připojené k doméně. Ve výchozím nastavení je použít zásady pro všechny platformy zařízení. 


![Konfigurace platforem zařízení](./media/active-directory-conditional-access-conditions/24.png)

Seznam platforem podporovaných zařízení najdete v tématu [podmínku platformy zařízení](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-technical-reference#device-platform-condition).


Běžný případ použití pro tento stav je zásada, která omezuje přístup k vašim cloudovým aplikacím na [spravovaných zařízeních](active-directory-conditional-access-policy-connected-applications.md#managed-devices). Další scénáře, včetně stavu platformy zařízení, najdete v části [podmíněného přístupu na základě aplikace Azure Active Directory](active-directory-conditional-access-mam.md).



## <a name="device-state"></a>Stav zařízení

Podmínku stavu zařízení vyloučí hybridní zařízení označené jako vyhovující ze zásad podmíněného přístupu a zařízení připojená k Azure AD. Tato podmínka je užitečné, když zásady by se měly používat jenom k nespravovanému zařízení pro další relaci zabezpečení. Například pouze vynutit řízení relace Microsoft Cloud App Security po nespravovaných zařízení. 


![Konfigurace stavu zařízení](./media/active-directory-conditional-access-conditions/112.png)

Pokud chcete blokovat přístup pro nespravovaná zařízení, implementovat [podmíněného přístupu podle zařízení](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online).


## <a name="locations"></a>Umístění

Pomocí umístění můžete definovat podmínky založené na kde došlo k pokusu připojení. 

![Konfigurovat místa](./media/active-directory-conditional-access-conditions/25.png)

Běžné případy použití pro tuto podmínku jsou zásady ochrany, které následující:

- Vyžadovat vícefaktorové ověřování pro uživatele, kteří používají službu, když jsou mimo firemní síť.  

- Blokovat přístup pro uživatele, kteří používají službu z určitých zemích nebo oblastech. 

Další informace najdete v tématu [co je podmínka umístění podmíněného přístupu Azure Active Directory?](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-locations).


## <a name="client-apps"></a>Klientské aplikace

S použitím podmínky aplikace klienta, můžete použít zásadu na různé typy aplikací. Příklady jsou weby, služby, mobilní aplikace a desktopové aplikace. 



Aplikace je sestavení klasifikováno následujícím způsobem:

- Webu nebo služby, pokud používá protokoly webové jednotné přihlašování, SAML, WS-Fed nebo OpenID Connect pro důvěrnému klientovi.

- Mobilní aplikace nebo aplikace klasické pracovní plochy, pokud pro nativní klient použije mobilní aplikaci, OpenID Connect.

Seznam klientských aplikací, můžete použít ve své zásady podmíněného přístupu najdete v tématu [klientské aplikace podmínku](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-technical-reference#client-apps-condition) v technické dokumentaci Azure Active Directory podmíněného přístupu.

Běžné případy použití pro tuto podmínku jsou zásady ochrany, které následující: 

- Vyžadovat [vyhovující zařízení](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online) pro mobilních a desktopových aplikací, které stahovat velké objemy dat do zařízení. Ve stejnou dobu povolit přístup z prohlížeče z libovolného zařízení.

- Blokovat přístup z webových aplikací a povolit přístup z mobilních a desktopových aplikací.

Můžete použít tuto podmínku, která má webové jednotné přihlašování a moderních ověřovacích protokolů. Můžete také použít ho k e-mailové aplikace, které používají Microsoft Exchange ActiveSync. Příklady jsou nativní e-mailové aplikace na většině smartphony. V současné době klientské aplikace, které používají starší verzi protokoly musí být zabezpečené pomocí Azure AD Federation Services.

Stav klienta aplikace můžete zvolit pouze v případě aplikace Microsoft Office 365 Exchange Online je jediná Cloudová aplikace, kterou jste vybrali.

![Cloudové aplikace](./media/active-directory-conditional-access-conditions/32.png)

Výběr **Exchange ActiveSync** jako klient aplikace podmínka je podporována pouze v případě, že nemáte k dispozici další podmínky nakonfigurované v zásadách. Však můžete zúžit rozsah tuto podmínku pro použití jenom na podporovaných platformách.

 
![Použít zásady jenom na podporovaných platformách](./media/active-directory-conditional-access-conditions/33.png)

Použití k tomuto stavu jenom na podporovaných platformách se rovná ve všech platformách zařízení [podmínku platformy zařízení](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online).

![Konfigurace platforem zařízení](./media/active-directory-conditional-access-conditions/34.png)


 Další informace najdete v těchto článcích:

- [Nastavení Sharepointu Online a Exchange Online pro podmíněný přístup Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
 
- [Azure Active Directory podmíněného přístupu k aplikacím](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam). 


### <a name="legacy-authentication"></a>Starší verze ověřování  

Podmíněný přístup se nyní vztahuje na starších klientů Microsoft Office, které nepodporují moderní ověřování. Platí také pro klienty, kteří používají protokolů e-mailu, jako jsou SMTP, POP a IMAP. Pomocí starší verze ověřování můžete nakonfigurovat zásady, jako je **blokovat přístup z jiných klientů**.


![Konfigurace klientských aplikací](./media/active-directory-conditional-access-conditions/160.png)  


#### <a name="known-issues"></a>Známé problémy

- Konfigurace zásad pro **ostatní klienty** blokuje určité klientů jako SPConnect v celé organizaci. Tento blok se stane, protože starší klienti ověřování neočekávaným způsobem. Problém se nevztahuje na hlavní aplikací Office, jako je starší klienti Office. 

- Může trvat až 24 hodin pro zásady tak, aby projeví. 


#### <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka:** bude blokovat ověřování webové služby systému Exchange?

To závisí na ověřovací protokol, který používá webové služby systému Exchange. Pokud aplikace webové služby systému Exchange používá moderní ověřování, se bude vztahovat **mobilní aplikace a desktopoví klienti** klientskou aplikaci. Základní ověřování se bude vztahovat **ostatní klienty** klientskou aplikaci.


**Otázka:** jaké ovládací prvky je možné použít pro **ostatní klienty**?

Všechny ovládací prvky lze nakonfigurovat pro **ostatní klienty**. Ale činnost koncového uživatele bude blokovaný přístup pro všechny případy. **Jiní klienti** nepodporují ovládací prvky, jako je vícefaktorové ověřování, vyhovující zařízení a připojení k doméně. 
 
**Otázka:** jakých podmínek je možné použít pro **ostatní klienty**?

Jakoukoli podmínku lze nakonfigurovat pro **ostatní klienty**.

**Otázka:** nepodporuje Exchange ActiveSync podporují všechny ovládací prvky a podmínky?

Ne. Následující seznam shrnuje podporu protokolu Exchange ActiveSync: 

- Exchange ActiveSync podporuje pouze uživatele a cílí na skupiny. Nepodporuje hosté nebo role. Pokud je nakonfigurovaná podmínku hosta nebo role, zablokuje se všichni uživatelé. Exchange ActiveSync blokuje všechny uživatele, protože nemůže určit, pokud mají zásady platit pro uživatele, nebo ne.

- Exchange ActiveSync funguje pouze s Microsoft Exchange Online jako cloudové aplikace. 

- Exchange ActiveSync nepodporuje jakoukoli podmínku s výjimkou klienta aplikace. 

- Exchange ActiveSync se dá nakonfigurovat u každého ovládacího prvku. Všechny ovládací prvky s výjimkou dodržování předpisů zařízením vést k bloku.

**Otázka:** zásady se vztahují na všechny klientské aplikace. ve výchozím nastavení do budoucna?

Ne. Není žádná změna v chování výchozí zásady. Zásady pokračovat v používání prohlížeče a mobilní aplikace a desktopoví klienti ve výchozím nastavení.



## <a name="next-steps"></a>Další postup

- Chcete-li zjistit, jak nakonfigurovat zásady podmíněného přístupu, přečtěte si téma [rychlý start: vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-app-based-mfa).

- Pokud chcete nakonfigurovat zásady podmíněného přístupu pro vaše prostředí, najdete v článku [osvědčené postupy pro podmíněný přístup ve službě Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-best-practices). 

