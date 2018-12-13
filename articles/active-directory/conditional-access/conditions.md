---
title: Jaké jsou podmínky podmíněného přístupu Azure Active Directory? | Dokumenty Microsoft
description: Zjistěte, jak se používají podmínky podmíněného přístupu Azure Active Directory k aktivaci zásad.
services: active-directory
keywords: conditional access to apps, conditional access with Azure AD, secure access to company resources, conditional access policies
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: eb7d5dc2d38d814625e904d1270446f5f1671624
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321097"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Jaké jsou podmínky podmíněného přístupu Azure Active Directory? 

Můžete řídit přístup uživatelů k vašim cloudovým aplikacím pomocí [podmíněného přístupu Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal). V zásadách podmíněného přístupu, definujte odpověď ("proveďte to") důvod Aktivace zásady ("Když taková situace nastane"). 

![Důvod a odpovědi](./media/conditions/10.png)


V kontextu podmíněného přístupu **v takovém případě** se volá **podmínku**. **Proveďte to** je volána **řízení přístupu**. Kombinací podmínek a ovládacích prvků přístupu představuje zásad podmíněného přístupu.

![Zásady podmíněného přístupu](./media/conditions/61.png)


Podmínky, které jste nenakonfigurovali v zásadách podmíněného přístupu se nepoužívají. Některé podmínky jsou [povinné](best-practices.md) použít zásady podmíněného přístupu do prostředí. 

Tento článek představuje přehled podmínek a jak se používají zásady podmíněného přístupu. 

## <a name="users-and-groups"></a>Uživatelé a skupiny

Stav uživatele a skupiny je povinný v zásadách podmíněného přístupu. V zásadách můžete buď vybrat **všichni uživatelé** nebo vyberte konkrétní uživatele a skupiny.

![Uživatelé a skupiny](./media/conditions/111.png)

Když vyberete **všichni uživatelé**, vaše zásady platí pro všechny uživatele v adresáři, včetně uživatelů typu Host.

Pokud jste **výběr uživatelů a skupin**, můžete nastavit následující možnosti:

* **Všichni uživatelé typu Host** cílí zásady pro uživatele typu Host B2B. Tato podmínka odpovídá uživatelský účet, který má **userType** atribut nastaven na **hosta**. Toto nastavení použijte, když zásady potřeba použít jako účet je vytvořen v pozvání tok, který ve službě Azure AD.

* **Role adresáře** cílí zásady založené na přiřazení role uživatele. Tato podmínka podporuje role adresáře jako **globálního správce** nebo **správce hesel**.

* **Uživatelé a skupiny** cílí na konkrétní skupiny uživatelů. Například můžete vybrat skupinu, která obsahuje všichni členové oddělení lidských zdrojů, pokud je vybrána HR aplikace jako cloudové aplikace. Skupina může být libovolný typ skupiny ve službě Azure AD, včetně dynamické nebo přiřazené zabezpečení a distribučních skupin.

Konkrétní uživatelé nebo skupiny můžete také vyloučit ze zásad. Jeden běžným případem použití je účty služeb, pokud vaše zásada vynucuje vícefaktorové ověřování (MFA). 

Cílení na konkrétní skupiny uživatelů slouží k nasazení nových zásad. V nové zásady by měl cílíte pouze počáteční sadu uživatelů k ověření zásad chování. 



## <a name="cloud-apps"></a>Cloudové aplikace 

Cloudové aplikace je webu nebo služby. Weby, které jsou chráněné službou Azure AD Application Proxy jsou také cloudových aplikací. Podrobný popis podporovaných cloudových aplikací, najdete v části [přiřazení aplikací v cloudu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#cloud-apps-assignments). 

**Cloudové aplikace** podmínka je povinný v zásadách podmíněného přístupu. V zásadách můžete buď vybrat **všechny cloudové aplikace** nebo vybrat konkrétní aplikace.

![Zahrnout cloudových aplikací](./media/conditions/03.png)

Vyberte:

- **Všechny cloudové aplikace** do směrného plánu zásad platí pro celou organizaci. Tento výběr použijte pro zásady, které vyžadují vícefaktorové ověřování při zjištění rizika přihlašování všech cloudových aplikací. Použít zásady **všechny cloudové aplikace** platí pro přístup ke všem webům a službám. Toto nastavení se neomezuje na cloudové aplikace, které se zobrazují na **vyberte aplikace** seznamu. 

- Jednotlivých cloudových aplikací ke konkrétním službám cílové zásady. Třeba, můžete vyžadovat, aby uživatelé měli [vyhovující zařízení](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online) pro přístup k Sharepointu Online. Tyto zásady platí také pro jiné služby při přístupu k obsahu na Sharepointu. Příkladem je Microsoft Teams. 

Na základě zásady můžete vyloučit konkrétní aplikace. Tyto aplikace jsou však stále můžou zásady aplikované na služby, ke kterým mají přístup. 



## <a name="sign-in-risk"></a>Riziko přihlášení

Riziko přihlášení je indikátorem pravděpodobnost (vysoká, střední nebo nízká), že přihlášení nebylo provedené legitimním vlastníkem uživatelského účtu. Azure AD se vypočítá že úroveň rizika přihlášení, když uživatel společnosti přihlásit. Úroveň rizika počítané přihlášení můžete použít jako podmínka v zásadách podmíněného přístupu.

![Úrovně rizik přihlašování](./media/conditions/22.png)

Pokud chcete použít tuto podmínku, musíte mít [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable) povolena.
 
Běžné případy použití pro tuto podmínku jsou zásady, které mají následující ochrany: 

- Zablokuje uživatelům s rizikem velký počet uživatelů se změnami. Tato ochrana potenciálně nezákonné uživatelům brání v přístupu k vašim cloudovým aplikacím. 
- Vyžadovat vícefaktorové ověřování pro uživatele se středním rizikem přihlášení. Vynucení vícefaktorového ověřování, můžete zadat máte ještě větší jistotu, že přihlášení se provádí legitimním vlastníkem účtu.

Další informace najdete v tématu [blokování přístupu, když se zjistí ohrožení relace](app-sign-in-risk.md).  

## <a name="device-platforms"></a>Platformy zařízení

Platforma zařízení je charakterizovaná operačního systému, na kterém běží na vašem zařízení. Azure AD označuje platformu pomocí informací uvedených v zařízení, jako je například uživatelského agenta. Tyto informace neověřené. Doporučujeme, aby zásady použité k nim všechny platformy. Zásady by měla blokovat přístup, vyžadovat dodržování předpisů pomocí zásad Microsoft Intune nebo vyžadují že zařízení být připojené k doméně. Ve výchozím nastavení je použít zásady pro všechny platformy zařízení. 


![Konfigurace platforem zařízení](./media/conditions/24.png)

Seznam platforem podporovaných zařízení najdete v tématu [podmínku platformy zařízení](technical-reference.md#device-platform-condition).


Běžný případ použití pro tento stav je zásada, která omezuje přístup k vašim cloudovým aplikacím na [spravovaných zařízeních](require-managed-devices.md). Další scénáře, včetně stavu platformy zařízení, najdete v části [podmíněného přístupu na základě aplikace Azure Active Directory](app-based-conditional-access.md).



## <a name="device-state"></a>Stav zařízení

Podmínku stavu zařízení vyloučí hybridní zařízení označené jako vyhovující ze zásad podmíněného přístupu a zařízení připojená k Azure AD. 


![Konfigurace stavu zařízení](./media/conditions/112.png)

Tato podmínka je užitečné, když zásady by se měly používat jenom k nespravovanému zařízení pro další relaci zabezpečení. Například pouze vynutit řízení relace Microsoft Cloud App Security po nespravovaných zařízení. 

## <a name="locations"></a>Umístění

Pomocí umístění můžete definovat podmínky založené na kde došlo k pokusu připojení. 

![Konfigurovat místa](./media/conditions/25.png)

Běžné případy použití pro tuto podmínku jsou zásady ochrany, které následující:

- Vyžadovat vícefaktorové ověřování pro uživatele, kteří používají službu, když jsou mimo firemní síť.  

- Blokovat přístup pro uživatele, kteří používají službu z určitých zemích nebo oblastech. 

Další informace najdete v tématu [co je podmínka umístění podmíněného přístupu Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).


## <a name="client-apps"></a>Klientské aplikace

Ve výchozím nastavení zásady podmíněného přístupu platí pro následující aplikace:

- [Aplikace prohlížeče](technical-reference.md#supported-browsers) -prohlížečových aplikací zahrnují websites pomocí SAML, WS-Federation nebo OpenID Connect protokoly webové jednotné přihlašování. To platí i pro web nebo webovou službu, který byl zaregistrován jako důvěrné klienta OAuth. Například web Office 365 SharePoint. 

- [Mobilní a desktopové aplikace používající moderní ověřování](technical-reference.md#supported-mobile-applications-and-desktop-clients) – tyto aplikace zahrnují desktopových aplikacích Office a telefonní aplikace. 


Kromě toho je možné cílit na zásady pro konkrétní klientské aplikace, které nepoužívají moderní ověřování, například:

- Klientské aplikace pomocí protokolu Microsoft Exchange ActiveSync. Po blocích zásad, pomocí protokolu Exchange ActiveSync, ovlivnění uživatelé získat e-mail o karanténě jeden s informacemi, proč jsou zablokované. V případě potřeby e-mailu obsahuje pokyny pro registraci zařízení v Intune.

- Ostatní klienti. Tyto aplikace zahrnovat klienty, kteří používají základní ověřování pomocí protokolů e-mailu, jako jsou IMAP, MAPI, POP, SMTP a starší aplikace Office, které nepoužívají moderní ověřování. Další informace najdete v tématu [jak moderní ověřování funguje pro klientské aplikace Office 2013 a Office 2016](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016).

![Klientské aplikace](./media/conditions/41.png)

Běžné případy použití pro tuto podmínku jsou zásady s následujícími požadavky:

- **[Vyžadovat, aby spravovaná zařízení](require-managed-devices.md)**  pro mobilních a desktopových aplikací, které stahování dat do zařízení. Ve stejnou dobu povolit přístup z prohlížeče z libovolného zařízení. Tento scénář zabrání ukládání a synchronizaci dokumentů na nespravovaném zařízení. Pomocí této metody můžete snížit pravděpodobnost ke ztrátě dat. Pokud dojde ke ztrátě nebo odcizení zařízení.

- **[Vyžadovat, aby spravovaná zařízení](require-managed-devices.md)**  aplikace pomocí aplikace ActiveSync přístup k Exchangi Online.

- **[Blok starší verze ověřování](block-legacy-authentication.md)**  do služby Azure AD (dalších klientů)

- Blokovat přístup z webových aplikací a povolit přístup z mobilních a desktopových aplikací.



### <a name="exchange-activesync-clients"></a>Klienti Exchange ActiveSync

Můžete vybrat jenom **klientů protokolu Exchange ActiveSync** pokud:


- Microsoft Office 365 Exchange Online je jediná Cloudová aplikace, kterou jste vybrali.

    ![Cloudové aplikace](./media/conditions/32.png)

- Není nutné další podmínky nakonfigurované v zásadách. Však můžete zúžit obor platí pouze pro tento stav [podporované platformy](technical-reference.md#device-platform-condition).
 
    ![Použít zásady jenom na podporovaných platformách](./media/conditions/33.png)


Když je zablokovaný přístup, protože [spravované zařízení](require-managed-devices.md) je povinné, ovlivnění uživatelé získat jednu e-mailu, který je k používání Intune provede. 

Pokud bude schválená aplikace je potřeba získat ovlivněných uživatelů: pokyny k instalaci a používání mobilního klienta aplikace Outlook.

V ostatních případech například pokud vícefaktorové ověřování se vyžaduje, ovlivnění uživatelé se zablokováno, protože klienti, kteří používají základní ověřování nepodporují MFA.

Můžete cílit pouze tohoto nastavení můžete uživatelům a skupinám. Nepodporuje hosté nebo role. Pokud je nakonfigurovaná podmínku hosta nebo role, všichni uživatelé jsou blokovány, protože nemůže určit podmíněného přístupu, pokud mají zásady platit pro uživatele, nebo ne.


 Další informace naleznete v tématu:

- [Nastavení Sharepointu Online a Exchange Online pro podmíněný přístup Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
 
- [Azure Active Directory podmíněného přístupu k aplikacím](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). 



## <a name="next-steps"></a>Další postup

- Chcete-li zjistit, jak nakonfigurovat zásady podmíněného přístupu, přečtěte si téma [rychlý start: Vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](app-based-mfa.md).

- Pokud chcete nakonfigurovat zásady podmíněného přístupu pro vaše prostředí, najdete v článku [osvědčené postupy pro podmíněný přístup ve službě Azure Active Directory](best-practices.md). 

