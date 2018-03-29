---
title: Co je nového? Poznámky k verzi pro Azure Active Directory | Microsoft Docs
description: Zjistěte, co je nového v Azure Active Directory (Azure AD), jako je například nejnovější poznámky k verzi, známé problémy, opravy chyb, zastaralé funkce a nadcházející změny.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9a947de3ad821d4cd65e579357e3c779e8623435
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="whats-new-in-azure-active-directory"></a>Co je nového ve službě Azure Active Directory?


> Nejnovější informace s co je nového ve službě Azure Active Directory (Azure AD) se přihlásíte k odběru [ ![RSS](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us) [kanálu](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us).



Azure AD obdrží vylepšení průběžně. Zůstane aktuální pomocí nejnovější, tento článek poskytuje informace o:

-   Nejnovější verze
-   Známé problémy
-   Opravy chyb
-   Zastaralé funkce
-   Plány pro změny

Tato stránka se aktualizuje každý měsíc, takže pokroku pravidelně.

## <a name="march-2018"></a>2018 března
 

### <a name="certificate-expire-notification"></a>Platnost certifikátu oznámení

**Typ:** pevné  
**Kategorie služby:** podnikové aplikace  
**Schopnosti produktu:** jednotného přihlašování
 
Azure AD odešle oznámení, když se certifikát pro galerie nebo jiných Galerie aplikace již brzy vyprší. 

Někteří uživatelé nedostal oznámení pro podnikové aplikace, které jsou nakonfigurované pro na základě SAML jednotné přihlašování. Tento problém byl vyřešen. Azure AD odešle oznámení pro certifikáty platnost vyprší za 7, 30 a 60 dnů. Můžete ere moci zobrazit tato událost v protokolech auditu. 

Další informace naleznete v tématu:

- [Správa certifikátů pro federované jednotné přihlašování v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Sestavy auditu aktivity na portálu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)

 
---
 

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Twitteru a Githubu poskytovatelů identit v Azure AD B2C

**Typ:** nová funkce  
**Kategorie služby:** B2C - správu identit uživatelů  
**Schopnosti produktu:** B2B/B2C
 
Nyní můžete přidat Twitter nebo Githubu jako poskytovatel identit v Azure AD B2C. Twitter přechází z verze public preview na všeobecné Ve verzi public preview je vydán Githubu.


Další informace najdete v tématu [co je spolupráce Azure AD B2B?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---


### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Omezit přístup z prohlížeče pomocí podmíněného přístupu na základě aplikací Azure AD Intune Managed Browser pro iOS a Android

**Typ:** nová funkce  
**Kategorie služby:** podmíněného přístupu  
**Schopnosti produktu:** Identity zabezpečení a ochrany
 

**Nyní ve verzi public preview!**

**Intune Managed Browser jednotné přihlašování:** zaměstnancům můžete použít jednotného přihlašování napříč nativních klientů (jako je Microsoft Outlook) a spravovaný prohlížeč Intune pro všechny Azure aplikace připojená k AD.

**Spravované prohlížeče podmíněného přístupu podpora Intune:** teď může vyžadovat zaměstnancům umožní používat Intune Managed browser pomocí zásady podmíněného přístupu na základě aplikace.

Další informace o to v našich [příspěvku na blogu](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Další informace naleznete v tématu:

- [Nastavení podmíněného přístupu na základě aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)

- [Nakonfigurujte zásady spravovaného prohlížeče](https://aka.ms/managedbrowser)  



---
 

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Proxy aplikace rutiny v modulu prostředí Powershell GA

**Typ:** nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnosti produktu:** řízení přístupu
 
Podpora pro rutiny služby Proxy aplikace je nyní v modulu prostředí Powershell GA! Upozorňujeme, že to vyžadují, abyste informováni o moduly Powershellu – Pokud jste se více než jeden rok za, některé rutiny může přestat pracovat. 


Další informace najdete v tématu [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Bezproblémové jednotného přihlašování pomocí neinteraktivní protokolu podporuje nativní klientů Office 365

**Typ:** nová funkce  
**Kategorie služby:** ověřování (přihlášení)  
**Schopnosti produktu:** ověření uživatele
 
Uživatele s využitím nativních klientů Office 365 (verze 16.0.8730.xxxx a vyšší) získat tichou prostředí přihlašování pomocí bezproblémové jednotné přihlašování. Tato podpora je poskytována přidání neinteraktivní protocol (WS-Trust) do služby Azure AD.

Další informace najdete v tématu [jak přihlášení na nativní klient s pracovní bezproblémové jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work).

 
---
 

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenanted-endpoints"></a>Uživatelé získají tichou přihlašování, s bezproblémové jednotné přihlašování, když aplikace odešle žádostí o přihlášení k Azure AD nevyužívá dělené tabulky koncové body

**Typ:** nová funkce  
**Kategorie služby:** ověřování (přihlášení)  
**Schopnosti produktu:** ověření uživatele
 
Uživatelé získají tichou přihlašování, s bezproblémové jednotné přihlašování, když aplikace (například `https://contoso.sharepoint.com`) odešle žádostí o přihlášení k Azure AD nevyužívá dělené tabulky koncové body – to znamená, `https://login.microsoftonline.com/contoso.com/<..>` nebo `https://login.microsoftonline.com/<tenant_ID>/<..>` – místo běžné koncového bodu Azure AD (`https://login.microsoftonline.com/common/<...>` ).

Další informace najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Třeba přidat pouze jednu adresu URL Azure AD, místo dříve, dvou adres URL do nastavení zóny intranetu uživatelů k zavedení bezproblémové jednotného přihlašování

**Typ:** nová funkce  
**Kategorie služby:** ověřování (přihlášení)  
**Schopnosti produktu:** ověření uživatele
 
K zavedení bezproblémové jednotného přihlašování pro vaše uživatele, je nutné přidat nastavení zón pouze jeden URL Azure AD k intranetu uživatelů pomocí zásad skupiny ve službě Active Directory: `https://autologon.microsoftazuread-sso.com`. Dřív byly zákazníci potřebné k přidání dvou adres URL.

Další informace najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nové federované aplikace k dispozici v galerii aplikací Azure AD

**Typ:** nová funkce  
**Kategorie služby:** podnikové aplikace  
**Schopnosti produktu:** 3. stran integrace
 
V březnu 2018 jsme doplnili podporu následujících 15 nových aplikací v naší Galerie aplikace s federací:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), pomocníka podle FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [amplitudě](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech digitální Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu cloudu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Můžete najít v dokumentaci pro všechny aplikace, zde: [https://aka.ms/appstutorial](https://aka.ms/appstutorial)


 
---
 

### <a name="pim-for-azure-resources-is-generally-available"></a>Je všeobecně dostupná pro prostředky Azure PIM

**Typ:** nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnosti produktu:** Privileged Identity Management
 
Pokud používáte Azure AD Privileged Identity Management pro adresář role, můžete teď můžete použít na PIM časově vázaných přístup a přiřazení možností pro role prostředků Azure, jako jsou odběry, skupiny prostředků, virtuální počítače a dalších prostředků, podporované pomocí Azure Resource Manager. Vynutit ověřování Multi-Factor Authentication při aktivaci role těsně za běhu a naplánovat aktivace v koordinaci s windows schválené změny. Kromě toho tato verze přináší vylepšení, včetně aktualizované uživatelského rozhraní, pracovní postupy schválení a možnost rozšíření role, jejichž platnost brzy vyprší a obnovení vypršela platnost role verzi public Preview není k dispozici.

Další informace najdete v tématu [PIM pro prostředky Azure (Preview)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Přidání volitelné deklarace do tokenů vaší aplikace (verze public preview)

**Typ:** nová funkce  
**Kategorie služby:** ověřování (přihlášení)  
**Schopnosti produktu:** ověření uživatele
 
Aplikace Azure AD můžete nyní žádost vlastní nebo volitelné deklarace identity ve Jwt nebo SAML tokeny.  Toto jsou deklarace identity o uživateli nebo klienta, která nejsou zahrnuta ve výchozím nastavení v tokenu, z důvodu omezení velikosti nebo použitelnosti.  Toto je momentálně ve verzi public preview pro aplikace Azure AD na koncové body verze 1.0 a v2.0.  Najdete v dokumentaci k informace na jaké deklarace identity je možné přidat a úprava manifest aplikace k vyžádání je.  

Další informace najdete v tématu [volitelné deklarace identity ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD podporuje PKCE pro bezpečnější toky OAuth

**Typ:** nová funkce  
**Kategorie služby:** ověřování (přihlášení)  
**Schopnosti produktu:** ověření uživatele
 
Dokumentace Azure AD se aktualizovaly v rámci Poznámka: podpora pro PKCE, která umožňuje bezpečnější komunikace během grant toku kódu autorizace OAuth 2.0.  U koncových bodů verze 1.0 a v2.0 jsou podporovány code_challenges S256 i ve formátu prostého textu. 

Další informace najdete v tématu autorizační kód požadavku[](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 

 
---
 

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-getworkers-api"></a>Podpora pro všechny uživatele hodnoty atributů k dispozici v rozhraní API Get_Workers Workday zřizování

**Typ:** nová funkce  
**Kategorie služby:** zřizování aplikace  
**Schopnosti produktu:** 3. stran integrace
 
Verzi public preview příchozí zřizování z Workday do služby Active Directory a Azure AD nyní podporuje možnost extrahovat a zřizování pro všechny hodnoty atributů k dispozici v rozhraní API Get_Workers Workday. Tento postup přidá podporuje pro stovky další standardní a vlastní atributy nad rámec těch, které jsou součástí počáteční verze během pracovního dne příchozí zřizování konektor.

Další informace najdete v tématu: [přizpůsobení v seznamu atributů uživatele Workday](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---



### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Změna členství ve skupině z dynamické statický a naopak

**Typ:** nová funkce  
**Kategorie služby:** skupiny správy  
**Schopnosti produktu:** spolupráce
 
Je možné změnit, jak se spravuje členství ve skupině. To je užitečné, pokud chcete zachovat stejný název skupiny a ID v systému, takže všechny existující odkazy na skupiny musí být stále platné; Vytvoření nové skupiny by vyžadovaly aktualizace tyto odkazy.
Aktualizovali jsme do centra pro správu Azure AD přidat podporu této funkce. Nyní zákazníci můžete převést existující skupiny z dynamické členství přiřazené členství a naopak. Stále k dispozici jsou taky existující rutiny prostředí PowerShell.

Další informace najdete v tématu [změna dynamické členství na statické a naopak](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal#changing-dynamic-membership-to-static-and-vice-versa)

 

 
---
 

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Vylepšené chování odhlašování pomocí bezproblémové jednotného přihlašování

**Typ:** změněné funkce  
**Kategorie služby:** ověřování (přihlášení)  
**Schopnosti produktu:** ověření uživatele
 
Dříve i v případě, že uživatelé odhlásili explicitně z k aplikaci zabezpečené službou Azure AD, že by být automaticky přihlášeni zpět pomocí bezproblémové jednotné přihlašování, pokud se pokoušeli o přístup k aplikaci Azure AD znovu v rámci své podnikové síti z jejich zařízení připojená k doméně. Díky této změně se odhlaste se podporuje.  To umožňuje uživatelům zvolit Azure stejný nebo jiný účet AD přihlásit zpátky, místo se automaticky přihlásíte pomocí bezproblémové jednotné přihlašování.

Další informace najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)

 
---
 

### <a name="application-proxy-connector-version-154020-released"></a>Verze konektoru Proxy aplikace 1.5.402.0 vydání

**Typ:** změněné funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnosti produktu:** Identity zabezpečení a ochrany
 
Tato verze konektoru je postupně se nasazuje pomocí listopadu. Tato nová verze konektoru obsahuje následující změny:

- Konektor teď Nastaví soubory cookie úrovni domény místo subdomény úroveň. To zajišťuje hladší prostředí jednotného přihlašování a zabraňuje redundantní ověřování výzvy.
- Podpora pro požadavky na blokové kódování
- Sledování stavu vylepšené konektoru 
- Několik oprav chyb a vylepšení stability

Další informace najdete v tématu [pochopit Azure AD Application Proxy konektory](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).

 
---
 

 



## <a name="february-2018"></a>2018 února
 

### <a name="improved-navigation-for-managing-users-and-groups"></a>Vylepšené navigace pro správu uživatelů a skupin

**Typ:** Plánovaná změna  
**Kategorie služby:** Directory Management  
**Schopnosti produktu:** adresáře
 

Bylo vylepšeno navigační prostředí pro správu uživatelů a skupin. Teď můžete Navigovat z přehledu directory přímo na seznam všech uživatelů, snadnější přístup k seznamu odstraněných uživatelů. Z přehledu directory můžete taky přejít přímo na seznam všech skupin, snadnější přístup k nastavení skupiny pro správu. A také na stránce Přehled adresář můžete hledat uživatele, skupiny, Podniková aplikace nebo registrace aplikací.
 

---


### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Dostupnost přihlášení a audit sestavy v Microsoft Azure provozována společností 21Vianet (Azure China 21Vianet)

**Typ:** nová funkce  
**Kategorie služby:** suverénní Cloudy  
**Schopnosti produktu:** monitorování a vytváření sestav
 

Nyní jsou k dispozici ve službě Microsoft Azure provozována společností 21Vianet (Azure China 21Vianet) instancí Azure AD aktivity protokolu sestavy. Zahrnují se tyto protokoly:

- **Protokoly aktivity přihlášení** -zahrnuje všechny přihlášení protokolům přidruženým k vašeho klienta.

- **Protokoly auditu heslo pro samoobslužné** -zahrnuje všechny protokoly auditu SSPR.

- **Protokoly auditu správy Directory** -zahrnuje všechny directory management související jako uživatel správy, Správa aplikací a ostatní protokoly auditu.

Tyto protokoly můžete získat přehled o tom, jak je to vaše prostředí. Poskytnutá data vám umožní:

- Zjistěte, jak jsou vaši uživatelé využité vaší aplikace a služby.

- Řešení problémů, které brání uživatelům získávání pracovní úkoly.

Další informace o tom, jak použít tyto sestavy najdete v tématu [generování sestav Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).
 

---


### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Pomocí role "Sestavy čtečky" (bez oprávnění správce role) můžete zobrazit sestavy aktivita služby Azure AD

**Typ:** nová funkce  
**Kategorie služby:** vytváření sestav  
**Schopnosti produktu:** monitorování a vytváření sestav
 

Jako součást zpětné vazby zákazníků povolit role bez oprávnění správce k přístupu k Azure AD aktivity zaznamená, nemůžeme povolili možnosti pro uživatele, kteří jsou v roli "Sestavy čtečky" tak, aby přístup k přihlášení a Audit aktivity v rámci portálu Azure, jakož i pomocí rozhraní Graph API. 

Další informace najdete pomocí těchto sestav najdete v tématu [generování sestav Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---
 


### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Deklarace EmployeeID jako atribut uživatele a identifikátor uživatele

**Typ:** nová funkce  
**Kategorie služby:** podnikové aplikace  
**Schopnosti produktu:** jednotného přihlašování
 

Můžete nakonfigurovat **EmployeeID** jako identifikátor uživatele a atribut uživatele pro uživatele člen a B2B hosté SAML aplikace založené na přihlášení z podniková aplikace uživatelského rozhraní.

Další informace najdete v tématu [přizpůsobení deklarace identity vystavené v tokenu SAML pro podnikové aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).
 

---


### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Zjednodušená správa aplikací pomocí zástupných znaků v Azure AD Application Proxy

**Typ:** nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnosti produktu:** ověření uživatele
 

Pro usnadnění nasazení aplikace a snížit vaše režijní náklady na správu, teď podporujeme možnost publikovat aplikace pomocí zástupných znaků. K publikování aplikací se zástupnými znaky, můžete postupovat podle publikování toku standardní aplikace, ale použít zástupný znak v interní a externí adresy URL.

Další informace najdete v tématu [aplikace zástupný znak v proxy aplikace služby Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-application-proxy-wildcard)

 

---
 
### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nové rutiny pro podporu konfigurace Proxy aplikace

**Typ:** nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnosti produktu:** platformy
 

Nejnovější verze modulu AzureAD PowerShell Preview obsahuje nové rutiny, která umožňuje zákazníkům ke konfiguraci aplikace Proxy aplikací pomocí prostředí PowerShell.

Jsou nové rutiny: 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup


 

---
 

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Nové rutiny pro podporu konfigurace skupin

**Typ:** nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnosti produktu:** platformy
 

Nejnovější verze modulu AzureAD PowerShell obsahuje rutiny pro správu skupin ve službě Azure AD. Tyto rutiny byly dřív dostupné v modulu AzureADPreview a jsou nyní přidaných do modulu AzureAD

Rutiny skupiny, které jsou nyní verze pro obecné dostupnosti jsou: 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup
 

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Je k dispozici nová verze služby Azure AD Connect

**Typ:** nová funkce  
**Kategorie služby:** AD Sync  
**Schopnosti produktu:** platformy
 

Azure AD Connect je upřednostňovaný nástroj pro synchronizaci dat mezi službou Azure AD a pro místní zdroje dat, včetně systému Windows Server Active Directory a LDAP.

**Důležité upozornění**
 
Toto sestavení představuje schématu a synchronizační pravidla změny. Azure AD Connect synchronizační služby se aktivuje úplný Import a úplnou synchronizaci kroků po upgradu. Informace o tom, jak toto chování změnit, najdete v článku [postup odložení úplnou synchronizaci po upgradu](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Tato verze obsahuje následující aktualizace a změny:

**Opravené problémy**

- Vyřešte časové okno na úlohy na pozadí pro filtrování Paritition stránku při přechodu na další stránku.
- Opravit chyby, která způsobila narušení přístupu během ConfigDB vlastní akci.
- Opravit chyby obnovení z časový limit připojení sql.
- Opravit chyby, které certifikáty se zástupnými znaky SAN nezdaří kontrola splněny.
- Opravit chyby, což způsobí, že miiserver.exe havárií během exportu konektoru AAD.
- Pevné chyby, které nesprávné heslo pokus o přihlášení řadiče domény při spuštění AAD connect průvodce a změňte konfiguraci

**Nové funkce a vylepšení**

- Pro GDPR jsme vyžadovaných k označení druhy zákaznická data, která jsou sdílet se společností Microsoft (telemetrie, stavu atd.), mají odkazy na podrobná online dokumentaci a poskytují způsob, jak můžete změnit předvolby.  Tato změnami přidává tyto funkce:
    - Sdílení dat a oznámení o ochraně osobních údajů na čistou instalaci stránky se smlouvou EULA.

    - Data oznámení na stránce upgradu sdílení a ochrana osobních údajů.

    - Nová úloha Další **nastavení ochrany osobních údajů** kde si uživatel může změnit jeho/jejich předvoleb.
 
- Aplikace telemetrie – správci můžete přepnout Tato třída dat zapnout nebo vypnout.

- Data na Azure AD Health – správci musí navštívit portál health k řízení jejich nastavení stavu. Jakmile změníte zásady služby agenty přečte a vynutit ho.

- Přidat akcí konfigurace zpětný zápis zařízení a indikátor průběhu inicializace stránky.

- Vylepšené obecné diagnostiky s sestavu ve formátu HTML a shromažďování dat úplné ZIP textu nebo sestavu ve formátu HTML.

- Vylepšení spolehlivosti automatického upgradu a přidat další telemetrické zajistit, že se dá určit stav serveru.

- Omezte oprávnění k dispozici k privilegovaným účtům na účet konektoru služby AD. Pro nové instalace Průvodce omezuje oprávnění, které privilegované účty po vytvoření účtu MSOL mít na účet MSOL. Změny se projeví Expresní instalace a vlastní instalace s automatické vytvoření účtu.

- Změnit instalační program není potřeba oprávnění správce systému na čisté instalace služby AADConnect.

- Nový nástroj pro řešení potíží se synchronizací pro konkrétní objekt. V současné době tento nástroj kontroluje následující:

    - UserPrincipalName Neshoda mezi objekt synchronizované uživatele a uživatelský účet v klientovi Azure AD.
  
    - Pokud objekt je filtrován z synchronizace kvůli filtrování domény
  
    - Pokud je objekt filtrován z synchronizace z důvodu filtrování organizační jednotky (OU)

- Nový nástroj pro synchronizaci aktuální hodnoty hash hesla uložená ve službě Active Directory místní pro konkrétní uživatelský účet. Nástroj nevyžaduje, aby změnu hesla. 
 

---
 

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Aplikace podpůrné zásady ochrany Intune aplikace pro použití s podmíněným přístupem založené na aplikaci Azure AD

**Typ:** změněné funkce  
**Kategorie služby:** podmíněného přístupu  
**Schopnosti produktu:** Identity zabezpečení a ochrany
 

Jsme přidali další aplikace, které podporují podmíněný přístup využívající aplikace. Teď můžete získat přístup k Office 365 a jiným aplikacím cloudu Azure AD připojené pomocí těchto schválené klientské aplikace.

Tyto aplikace budou přidány do konce dne 

- Microsoft PowerBI

- Microsoft Launcher

- Microsoft fakturace

Další informace naleznete v tématu:

- [Požadavek schválené klientské aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD, na základě aplikace podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)

 

---
 

### <a name="terms-of-use-update-to-mobile-experience"></a>Podmínky použití aktualizace mobilní prostředí 

**Typ:** změněné funkce  
**Kategorie služby:** podmínky použití  
**Schopnosti produktu:** zásad správného řízení
 

Jakmile se zobrazí podmínky použití, můžete nyní kliknout na **s potíže se zobrazením? Kliknutím sem**. Kliknutím na tento odkaz otevře podmínky použití nativně na vašem zařízení. Bez ohledu na velikost písma v dokumentu nebo velikost obrazovky zařízení můžete přiblížení a čtení dokumentu, podle potřeby. 
 

---
 
## <a name="january-2018"></a>2018 leden
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nové federované aplikace k dispozici v galerii aplikací Azure AD 

**Typ:** nová funkce  
**Kategorie služby:** podnikové aplikace  
**Schopnosti produktu:** 3. stran integrace
 

V lednu 2018 byly přidány následující nové aplikace s podporou federace v galerii aplikací:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [Software pro správu ochrany osobních údajů OneTrust](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk federovaný Directory](https://go.microsoft.com/fwlink/?linkid=864699) a [přesnost NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Úplný přehled všechny kurzy k dispozici, najdete v části [integraci aplikací SaaS s Azure Active Directory](https://aka.ms/appstutorial).
 

---
 


### <a name="sign-in-with-additional-risk-detected"></a>Zjistilo se přihlášení s dalšími riziky.

**Typ:** nová funkce  
**Kategorie služby:** Identity Protection  
**Schopnosti produktu:** Identity zabezpečení a ochrany
 

Přehledy, které máte pro událost zjištěnou riziko je vázaný na vaše předplatné Azure AD. S Azure AD Premium P2 edition získáte nejvíce podrobné informace o všech základní detekce.

S edice Azure AD Premium P1 detekce, které nejsou předmětem licence zobrazí jako riziko událost přihlášení s další riziko zjištěna.

Další informace najdete v tématu věnovaném [rizikovým událostem služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Skrýt aplikací Office 365 z panelů přístup koncového uživatele

**Typ:** nová funkce  
**Kategorie služby:** Moje aplikace  
**Schopnosti produktu:** jednotného přihlašování
 

Teď můžete lépe spravovat jak aplikace Office 365 zobrazovat ve panelů přístup uživatelů prostřednictvím nové nastavení uživatele. Tato možnost je užitečná pro snižuje množství aplikací v panelů přístupu uživatele, pokud chcete zobrazit pouze aplikace Office na portálu Office. Toto nastavení se nachází v **uživatelská nastavení** a je popsán **uživatelé vidí pouze aplikace Office 365 na portálu Office 365**.
 

Další informace najdete v tématu [skrýt aplikace z možnosti pro uživatele v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 


### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Bezproblémové přihlašování v aplikacích, které jsou povolené pro jednotné přihlašování heslo přímo z adresy URL aplikace 

**Typ:** nová funkce  
**Kategorie služby:** Moje aplikace  
**Schopnosti produktu:** jednotného přihlašování
 

Rozšíření prohlížeče Moje aplikace je nyní k dispozici prostřednictvím pohodlný nástroj, který poskytuje jednotné přihlašování v Moje aplikace na funkce jako zástupce v prohlížeči. Po instalaci uživatele se zobrazí ikona waffle v prohlížeči, který poskytuje je rychlý přístup k aplikacím. Uživatelé nyní mohou využít výhod:

- Umožňuje přímo se přihlásit k aplikacím na základě hesla-jednotného přihlašování z přihlašovací stránky aplikace
- Spuštění všech aplikací pomocí funkce rychlého vyhledávání
- Zástupce nedávno použité aplikací z rozšíření
- Rozšíření není k dispozici pro okraj a Chrome, Firefox.
 
Další informace najdete v tématu [Moje aplikace zabezpečené přihlašování rozšíření](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction#my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Správa služby Azure AD byl vyřazen prostředí na portálu Azure classic

**Typ:** zastaralé   
**Kategorie služby:** Azure AD  
**Schopnosti produktu:** adresáře
 

Od verze 8 leden 2018, Správa Azure AD byl vyřazen prostředí na portálu Azure classic. To došlo ve spojení s vyřazení z portálu Azure classic, sám sebe. Do budoucna, měli byste použít [centra pro správu Azure AD](https://aad.portal.azure.com) pro všechny vaše založené na portálu správy Azure AD.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Webový portál PhoneFactor byl vyřazen.

**Typ:** zastaralé  
**Kategorie služby:** Azure AD  
**Schopnosti produktu:** adresáře
 

Od verze 8 leden 2018 byl vyřazen PhoneFactor webový portál. Tento portál byl použit pro správu MFA serveru, ale tyto funkce byly přesunuty do portálu Azure na stránce portal.azure.com. 

Konfigurace vícefaktorového ověřování se nachází v: **Azure Active Directory \> MFA serveru**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Přestat používat sestav Azure AD


**Typ:** zastaralé  
**Kategorie služby:** vytváření sestav  
**Schopnosti produktu:** Identity – Správa životního cyklu  


S obecné dostupnosti novou konzolu pro správu služby Azure Active Directory a nová rozhraní API pro aktivity a zabezpečení sestavy, sestava rozhraní API je teď dostupná v části "/ sestavy" koncový bod byl vyřazen od konce 31. prosince 2017.


**Co je k dispozici?**

V rámci přechodu na novou konzolu pro správu byla provedena 2 nových rozhraní API k dispozici pro načtení protokoly aktivity Azure AD. Novou sadu rozhraní API zadejte bohatší filtrování a řazení funkce kromě bohatší auditu a přihlašovací aktivity. Dříve k dispozici prostřednictvím sestavy zabezpečení dat je nyní přístupná prostřednictvím Identity Protection rizikových událostech rozhraní API v aplikaci Microsoft Graph.

Další informace naleznete v tématu:

- [Začínáme s Azure Active Directory, vytváření sestav rozhraní API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Začínáme s Azure Active Directory Identity Protection a Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)


---


## <a name="december-2017"></a>2017 prosinec
 

### <a name="terms-of-use-in-the-access-panel"></a>Podmínky použití na přístupovém panelu

**Typ:** nová funkce  
**Kategorie služby:** podmínky použití  
**Schopnosti produktu:** zásad správného řízení nebo dodržování předpisů
 
Nyní můžete přejít na Panel přístupu a zobrazit podmínky použití, který jste dříve přijali.

Postupujte následovně:

1. Přejděte na [MyApps portál](https://myapps.microsoft.com)a přihlaste se.

2. V pravém horním rohu vyberte své jméno a pak vyberte **profil** ze seznamu. 

3. Na vaše **profil**, vyberte **přečtěte si podmínky použití**. 

4. Teď můžete zkontrolovat podmínky použití, který jste přijali. 

Další informace najdete v tématu [Azure AD podmínky použití funkce (preview)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="new-azure-ad-sign-in-experience"></a>Nové přihlašovací prostředí Azure AD

**Typ:** nová funkce  
**Kategorie služby:** Azure AD  
**Schopnosti produktu:** ověření uživatele
 
Azure AD a identity systémem účtů Microsoft uživatelská byla znovu navržena tak, aby mají konzistentní vzhled a chování. Kromě toho na přihlašovací stránku služby Azure AD shromažďuje uživatelské jméno nejprve následuje přihlašovací údaje na druhý obrazovky.

Další informace najdete v tématu [nové přihlašovací prostředí Azure AD je nyní ve verzi public preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Menšího přihlášení: V nové "zůstat přihlášeni" rozhraní pro přihlášení k Azure AD

**Typ:** nová funkce  
**Kategorie služby:** Azure AD  
**Schopnosti produktu:** ověření uživatele
 
**Zůstat přihlášeni** políčko na přihlašovací stránce služby Azure AD byl nahrazený nového řádku, který se zobrazí po úspěšně ověřit. 

Pokud je odpověď **Ano** na tuto výzvu služby vám dává trvalé obnovovací token. Toto chování je stejné jako když jste vybrali **zůstat přihlášeni** políčko ve starší verzi uživatelského rozhraní. U federovaných klientů se zobrazí tuto výzvu po úspěšné ověření u služby federované.

Další informace najdete v tématu [menšího přihlášení: je nové prostředí "zůstat přihlášeni" pro Azure AD ve verzi preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---
 

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Přidat konfiguraci tak, aby vyžadovala podmínky použití, které mají být rozbaleny před přijetím

**Typ:** nová funkce  
**Kategorie služby:** podmínky použití  
**Schopnosti produktu:** zásad správného řízení nebo dodržování předpisů
 
Možnost pro správce vyžaduje, aby jejich uživatelé rozbalte podmínky použití před vyjádření souhlasu s podmínkami.

Vyberte buď **na** nebo **vypnout** budou muset uživatelé rozbalte podmínky použití. **Na** nastavení vyžaduje, aby uživatelé zobrazit podmínky použití před přijetím je.

Další informace najdete v tématu [Azure AD podmínky použití funkce (preview)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="scoped-activation-for-eligible-role-assignments"></a>Vymezená aktivace pro přiřazení oprávněné rolí

**Typ:** nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnosti produktu:** Privileged Identity Management
 
Vymezená aktivace můžete použít k aktivaci přiřazení rolí oprávněné prostředků Azure s menší nezávislé než původní přiřazení výchozí hodnoty. Příkladem je-li, můžete jako vlastník předplatného přiřazena ve vašem klientovi. Při použití aktivace oboru můžete aktivovat roli vlastníka pro až pět prostředky obsažené v rámci předplatného (například skupiny prostředků a virtuálních počítačů). Obor aktivací může omezit možnost provádění nežádoucí změny k důležitým prostředkům Azure.

Další informace najdete v tématu [co je Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nové federované aplikace v galerii aplikací Azure AD

**Typ:** nová funkce  
**Kategorie služby:** podnikové aplikace  
**Schopnosti produktu:** 3. stran integrace
 
V prosinec 2017 byly přidány následující nové aplikace s podporou federace v galerii aplikací:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), správci prostředí Adobe [digitální výkladní skříň EFI](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [IMAGE FUNGUJE](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD integrace](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [jednotné přihlašování SAML pro bambus podle řešení GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [Jednotné přihlašování SAML pro Bitbucket podle řešení GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, integrace Zenegy Azure AD.

Úplný přehled všechny kurzy k dispozici, najdete v části [integraci aplikací SaaS s Azure Active Directory](https://aka.ms/appstutorial).

 
---
 

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Pracovní postupy schválení pro role adresář Azure AD

**Typ:** změněné funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnosti produktu:** Privileged Identity Management
 
Pracovní postup schválení pro role adresář Azure AD je všeobecně dostupná.

S pracovní postup schválení může správce privilegovaných rolí vyžadovat členům role způsobilých pro žádost o aktivaci role předtím, než můžete používat privilegované role. Několik uživatelů a skupin může být delegované schválení odpovědnosti. Členy role vhodné přijímat upozornění, když je dokončen schválení a jejich role je aktivní.

---
 

### <a name="pass-through-authentication-skype-for-business-support"></a>Předávací ověřování: Skype pro firmy podpory

**Typ:** změněné funkce  
**Kategorie služby:** ověřování (přihlášení)  
**Schopnosti produktu:** ověření uživatele


Předávací ověřování nyní podporuje uživatelská přihlášení ke Skypu pro firmy klientských aplikací, které podporují moderní ověřování, která zahrnuje online a hybridní topologie. 

Další informace najdete v tématu [Skype pro firmy topologie podporována s moderním ověřováním](https://technet.microsoft.com/library/mt803262.aspx).
 
---
 

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Aktualizace k Azure AD Privileged Identity Management for RBAC Azure (preview)

**Typ:** změněné funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnosti produktu:** Privileged Identity Management
 
S aktualizací ve verzi public preview služby Azure AD Privileged Identity Management (PIM) pro řízení řízení přístupu (RBAC) můžete provádět následující akce:

* Použití akorát správy.
* Vyžadovat schválení k aktivaci role prostředků.
* Naplánujte budoucí aktivace roli, která vyžaduje schválení pro obě Azure AD a Azure RBAC rolí.

 
Další informace najdete v tématu [Privileged Identity Management pro prostředky Azure (preview)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

 
---
 
## <a name="november-2017"></a>2017 listopadu
 
### <a name="access-control-service-retirement"></a>Vyřazení služby Řízení přístupu



**Typ:** Plánovaná změna  
**Kategorie služby:** služby Řízení přístupu  
**Schopnosti produktu:** služby Řízení přístupu 


 Pozdní 2018 vyřadí Azure Active Directory řízení přístupu (také označované jako služba Řízení přístupu). Další informace, které zahrnují prováděcí plán a pokyny vysoké úrovně migrace bude k dispozici v další trvá několik týdnů. Na této stránce můžete nechat komentáře jakékoli dotazy týkající se služby Řízení přístupu a seskupení odpoví.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Omezit přístup z prohlížeče na Intune Managed Browser 


**Typ:** Plánovaná změna  
**Kategorie služby:** podmíněného přístupu  
**Schopnosti produktu:** Identity zabezpečení a ochrana




K Office 365 a jiným aplikacím cloudu Azure AD připojení můžete omezit přístup z prohlížeče pomocí Intune Managed Browser jako schválené aplikace. 

Teď můžete nakonfigurovat následující podmínka pro podmíněný přístup využívající aplikace:

**Klientské aplikace:** prohlížeče

**Co je vliv změny?**

V současné době je blokován přístup při použití této podmínky. Pokud je k dispozici ve verzi preview, veškerý přístup bude vyžadovat použití aplikace spravovaného prohlížeče. 

Vyhledejte této schopnosti a další informace v poznámkách k nadcházející blogy a verzi. 

Další informace najdete v tématu [podmíněný přístup, ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nové schválené klientské aplikace pro podmíněný přístup využívající aplikaci Azure AD

 
**Typ:** Plánovaná změna  
**Kategorie služby:** podmíněného přístupu  
**Schopnosti produktu:** Identity zabezpečení a ochrana




Následující aplikace plánujeme přidat do seznamu [schválení klientských aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


Další informace naleznete v tématu:

- [Požadavek schválené klientské aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD, na základě aplikace podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>Podmínky použití podpora více jazyků



**Typ:** nová funkce    
**Kategorie služby:** podmínky použití  
**Schopnosti produktu:** zásad správného řízení nebo dodržování předpisů





Správci teď můžete vytvářet nové podmínky použití, které obsahují více dokumentů PDF. Můžete označit tyto dokumenty PDF s příslušný jazyk. Uživatelům se zobrazí PDF s odpovídající jazyk na základě jejich předvoleb. Pokud není nalezena žádná shoda, zobrazí se výchozí jazyk.


---
 

### <a name="real-time-password-writeback-client-status"></a>Stav klienta zpětného zápisu hesla v reálném čase



**Typ:** nová funkce  
**Kategorie služby:** samoobslužného resetování hesel  
**Schopnosti produktu:** ověření uživatele


 

Nyní můžete zkontrolovat stav vašeho klienta zpětný zápis hesla na místě. Tato možnost je dostupná v **integrace v místním** části [resetování hesla](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) stránky. 

Pokud jsou problémy s připojením k klientovi zpětného zápisu na místě, zobrazí chybovou zprávu, která nabízí:

- Informace o Proč se nemůžete připojit k vaší lokálního klienta zpětný zápis.
- Odkaz na dokumentaci, který vám pomůže při řešení problému. 


Další informace najdete v tématu [integrace v místním](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Azure AD, na základě aplikace podmíněného přístupu 



 
**Typ:** nová funkce  
**Kategorie služby:** Azure AD  
**Schopnosti produktu:** Identity zabezpečení a ochrana





Teď můžete omezit přístup k Office 365 a jiným aplikacím cloudu Azure AD připojené k [schválení klientských aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) podporující zásady ochrany aplikací služby Intune s použitím [podmíněného přístupu na základě aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam). Zásady ochrany aplikací Intune se používají ke konfiguraci a ochraně dat společnosti na tyto klientské aplikace.

Tím, že zkombinujete [na základě aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) s [na zařízení](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) zásady podmíněného přístupu, máte možnost k ochraně dat pro osobní a firemní zařízení.

Následující podmínky a ovládací prvky jsou nyní k dispozici pro použití s podmíněného přístupu na základě aplikace:

**Podmínka podporované platformy**

- iOS
- Android

**Stav aplikace klienta**

- Mobilní aplikace a desktopoví klienti

**Řízení přístupu**

- Vyžaduje se klientem schválená aplikace.


Další informace najdete v tématu [podmíněného přístupu na základě aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam).

 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Správa zařízení služby Azure AD na portálu Azure



**Typ:** nová funkce  
**Kategorie služby:** registrace zařízení a správy  
**Schopnosti produktu:** Identity zabezpečení a ochrana

 



Nyní můžete najít všechna vaše zařízení připojená k Azure AD a aktivity související s zařízení na jednom místě. Je nové prostředí pro správu, ke správě identit zařízení a nastavení na portálu Azure. V této verzi můžete:

- Zobrazte všechna zařízení, které jsou k dispozici pro podmíněný přístup v Azure AD.
- Zobrazit vlastnosti, které zahrnují vaše hybridní Azure zařízení připojená k AD.
- Najít klíče nástroje BitLocker pro zařízení připojená k AD Azure, spravovat vaše zařízení s Intune a další.
- Spravovat nastavení související s zařízení služby Azure AD.

Další informace najdete v tématu [spravovat zařízení pomocí portálu Azure](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).



 
---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Podpora systému macOS jako platforma pro podmíněný přístup k Azure AD 



**Typ:** nová funkce    
**Kategorie služby:** podmíněného přístupu  
**Schopnosti produktu:** Identity zabezpečení a ochrana 
 

Nyní můžete zahrnout (nebo vyloučit) systému macOS jako podmínka platformy zařízení zásady podmíněného přístupu Azure AD. Přidání systému macOS na platformách podporovaných zařízení můžete:

- **Registrovat a spravovat zařízení v systému macOS pomocí Intune.** Podobně jako u jiných platformách, jako je iOS a Android, aplikace portálu společnosti je k dispozici pro systému macOS provést jednotné registrace. Nová aplikace portálu společnosti pro systému macOS můžete použít k registraci zařízení v Intune a zaregistrovat ji pomocí služby Azure AD.
- **Zkontrolujte, zda systému macOS zařízení splňovat zásady dodržování předpisů vaší organizace definované v Intune.** V Intune na portálu Azure můžete nyní můžete nastavit zásady dodržování předpisů pro zařízení v systému macOS. 
- **Omezte přístup k aplikacím ve službě Azure AD pouze systému macOS kompatibilních zařízení.** Vytváření zásad podmíněného přístupu má systému macOS jako možnost platformy samostatné zařízení. Teď můžete vytvářet zásady podmíněného přístupu v systému macOS specifické pro cílové aplikace nastavit v Azure.

Další informace naleznete v tématu:

- [Vytvoření zásady dodržování předpisů pro zařízení s macOS pomocí Intune](https://aka.ms/macoscompliancepolicy)
- [Podmíněný přístup v Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)


 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Rozšíření serveru zásad sítě pro Azure Multi-Factor Authentication 


**Typ:** nová funkce    
**Kategorie služby:** vícefaktorového ověřování  
**Schopnosti produktu:** ověření uživatele




Cloudové služby Multi-Factor authentication možnosti k infrastruktuře ověřování přidá rozšíření Network Policy Server pro Azure Multi-Factor Authentication pomocí existujících serverů. S příponou Network Policy Server přidáte telefonní hovor, textová zpráva nebo ověření telefonu v aplikaci do vaší stávající tok ověřování. Nemusíte instalovat, konfigurovat a spravovat nové servery. 

Toto rozšíření byla vytvořena pro organizace, které chcete chránit připojení virtuální privátní sítě bez nasazení serveru Azure Multi-Factor Authentication. Network Policy Server, který rozšíření funguje jako adaptér mezi RADIUS a cloudové ověřování Azure Multi-Factor Authentication pro poskytování druhý faktor ověřování pro federované nebo synchronizovaných uživatelů.


Další informace najdete v tématu [integrovat stávající infrastruktury Network Policy Server Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Obnovit nebo trvale odebrat odstranění uživatelé


**Typ:** nová funkce    
**Kategorie služby:** Správa uživatelů  
**Schopnosti produktu:** adresáře 



V Centru správy služby Azure AD můžete provádět následující akce:

- Obnovte odstraněný uživatel. 
- Trvale odstraní uživatele.


**Chcete-li vyzkoušejte ji:**

1. V Centru pro správu Azure AD, vyberte [všichni uživatelé](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) v **spravovat** části. 

2. Z **zobrazit** seznamu, vyberte **uživatelé v poslední době odstraňovali**. 

3. Vyberte jeden nebo více odstraněných uživatelů a pak je buď obnovit nebo trvale odstranit.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nové schválené klientské aplikace pro podmíněný přístup využívající aplikaci Azure AD

 
**Typ:** změněné funkce  
**Kategorie služby:** podmíněného přístupu  
**Schopnosti produktu:** Identity zabezpečení a ochrana


Následující aplikace byly přidány do seznamu [schválení klientských aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- Microsoft Planner
- Azure Information Protection 


Další informace naleznete v tématu:

- [Požadavek schválené klientské aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD, na základě aplikace podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Použití "Nebo" mezi ovládacími prvky v zásadách podmíněného přístupu 


**Typ:** změněné funkce    
**Kategorie služby:** podmíněného přístupu  
**Schopnosti produktu:** Identity zabezpečení a ochrana

 
Teď můžete použít "nebo" (vyžadují jeden z vybraných ovládacích prvků) pro řízení podmíněného přístupu. Tato funkce slouží k vytvoření zásady s "nebo" mezi řízení přístupu. Například můžete tuto funkci pro vytvoření zásady, které vyžadují uživatele k přihlášení pomocí služby Multi-Factor authentication "nebo" na vyhovující zařízení.

Další informace najdete v tématu [ovládacích prvků v Azure AD podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).

 
---

### <a name="aggregation-of-real-time-risk-events"></a>Agregace v reálném čase rizikových událostí


**Typ:** změněné funkce    
**Kategorie služby:** ochranu Identity  
**Schopnosti produktu:** Identity zabezpečení a ochrana


V Azure AD Identity Protection jsou teď pro každý typ události riziko agregován všechny události v reálném čase rizik, které pocházely ze stejné IP adresy v daný den. Tato změna omezuje objem událostí riziko uvedených bez jakékoli změny v zabezpečení uživatelů.

Základní v reálném čase zjišťování funguje při každém přihlášení uživatele. Pokud máte zásadu přihlášení riziko zabezpečení nastavit tak, aby aplikace Multi-Factor authentication nebo blokování přístupu, je stále aktivuje během každé rizikové přihlášení.

 
---
 




## <a name="october-2017"></a>Říjen 2017


### <a name="deprecate-azure-ad-reports"></a>Přestat používat sestav Azure AD


**Typ:** Plánovaná změna  
**Kategorie služby:** vytváření sestav  
**Schopnosti produktu:** Identity – Správa životního cyklu  



Portál Azure nabízí:

- Novou konzolu pro správu Azure AD.
- Nová rozhraní API pro sestavy aktivity a zabezpečení.
 
Z důvodu tyto nové funkce byly sestavy rozhraní API v části endpoint/Reports vyřazeno na 10. prosince 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Detekce pole Automatické přihlášení


**Typ:** pevné   
**Kategorie služby:** Moje aplikace  
**Schopnosti produktu:** jednotného přihlašování  



Azure AD podporuje zjišťování pole automatické přihlašování pro aplikace, které vykreslení uživatelské jméno a heslo pole HTML. Tyto kroky jsou dokumentovány v článku [automaticky zaznamenání pole přihlášení pro aplikaci](https://docs.microsoft.com/azure/active-directory/application-config-sso-problem-configure-password-sso-non-gallery#how-to-manually-capture-sign-in-fields-for-an-application). Této funkci můžete najít tak, že přidáte *Non-Galerie* aplikace na **podnikové aplikace, které** stránku [portál Azure](http://aad.portal.azure.com). Kromě toho můžete nakonfigurovat **jednotné přihlašování** režimu do této nové aplikace **založené na heslech jednotné přihlašování**, zadejte adresu URL webu a potom uložte stránky.
 
Z důvodu problém se službou byl dočasně zakázán tuto funkci. Tento problém byl vyřešen a detekce pole Automatické přihlášení opět k dispozici.

---

### <a name="new-multi-factor-authentication-features"></a>Nové funkce služby Multi-Factor authentication


**Typ:** nová funkce  
**Kategorie služby:** vícefaktorového ověřování  
**Schopnosti produktu:** Identity zabezpečení a ochrana  



Vícefaktorové ověřování (MFA), je zásadní součástí ochrany vaší organizace. Chcete-li více adaptivní přihlašovací údaje a zkušenosti pohodlnější způsob jednotného, byly přidány následující funkce: 

- Výzvy Multi-Factor výsledky jsou integrované přímo do Azure AD přihlášení zprávu, která zahrnuje programový přístup k MFA výsledky.
- Konfigurace vícefaktorového ověřování se více úzce integruje do konfigurace Azure AD prostředí na portálu Azure.

Pomocí této verzi public preview správu MFA a vytváření sestav jsou integrovanou součástí prostředí základní konfigurace Azure AD. Teď můžete spravovat funkce portálu správy vícefaktorového ověřování v rámci prostředí Azure AD.

Další informace najdete v tématu [referenční informace pro vytváření sestav v portálu Azure MFA](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 


---

### <a name="terms-of-use"></a>Podmínky použití



**Typ:** nová funkce  
**Kategorie služby:** podmínky použití  
**Schopnosti produktu:** zásad správného řízení nebo dodržování předpisů  



Můžete použít Azure AD podmínky použití na informace, jako je důležité omezení pro požadavky právních nebo dodržování předpisů pro uživatele.

Podmínky použití služby Azure AD můžete použít v následujících scénářích:

- Obecné podmínky použití pro všechny uživatele ve vaší organizaci
- Konkrétní podmínky použití na základě atributů uživatele (například lékařů oproti sester) nebo domácí oproti mezinárodní zaměstnanci, provádí dynamických skupin
- Konkrétní podmínky použití pro přístup k velkým dopadem obchodních aplikací, jako je Salesforce

Další informace najdete v tématu [Azure AD podmínky použití](https://docs.microsoft.com/azure/active-directory/active-directory-tou).


---

### <a name="enhancements-to-privileged-identity-management"></a>Vylepšení Správa privilegovaných identit


**Typ:** nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnosti produktu:** Privileged Identity Management  


S Azure AD Privileged Identity Management můžete spravovat, řízení a monitorování v rámci vaší organizace na přístup k prostředkům Azure (preview):

- Předplatná
- Skupiny prostředků
- Virtuální počítače 

Všechny prostředky v rámci portálu Azure, které používají funkci Azure RBAC můžete využít výhod všech zabezpečení a možnosti správy životního cyklu, které má Azure AD Privileged Identity Management na nabídku.

Další informace najdete v tématu [Privileged Identity Management pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).


---

### <a name="access-reviews"></a>Kontroly přístupu


**Typ:** nová funkce  
**Kategorie služby:** přístup recenze  
**Schopnosti produktu:** zásad správného řízení nebo dodržování předpisů  



Organizace můžou efektivně spravovat přístup k podnikovým aplikacím a členství ve skupinách pomocí přístupu recenze (preview): 

- Můžete znovu certifikovat přístup uživatelů typu host pomocí kontrol jejich přístupu k aplikacím a jejich členství ve skupinách. Kontroloři můžete efektivně rozhodnout, zda povolit hosté dál přístup k přehledům poskytované recenze přístup podle.
- Pomocí kontrol přístupu je možné provést opětovnou certifikaci přístupu k aplikacím a členství ve skupinách i pro zaměstnance.

Kontroly přístupu lze shromažďovat v programech sledujících dodržování předpisů a přístup k citlivým aplikacím v rámci organizace.

Další informace najdete v tématu [zkontroluje přístup k Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).


---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Skrýt aplikace jiných výrobců z Moje aplikace a Spouštěč aplikace Office 365



**Typ:** nová funkce  
**Kategorie služby:** Moje aplikace  
**Schopnosti produktu:** jednotného přihlašování  



Nyní můžete lépe spravovat aplikace, které se zobrazí na vaši uživatelé portálech prostřednictvím novou **skrýt aplikace** vlastnost. Můžete skrýt, aplikacemi a pomáhá v případech, kde dlaždice aplikace zobrazí pro back endové služby nebo duplicitní dlaždice a Spouštěč oken aplikace zbytečné soubory uživatelů. Přepínač se **vlastnosti** část aplikace třetích stran a je popsán **viditelný pro uživatele?** Také můžete skrýt aplikaci programově pomocí prostředí PowerShell. 

Další informace najdete v tématu [skrýt aplikace jiných výrobců od koncových uživatelů ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Co je k dispozici?**

 V rámci přechodu na novou konzolu pro správu, dvou nových rozhraní API pro načítání aktivita služby Azure AD protokoly jsou k dispozici. Novou sadu rozhraní API poskytuje bohatší filtrování a řazení funkce kromě bohatší auditu a přihlašovací aktivity. Dříve k dispozici prostřednictvím teď sestavy zabezpečení dat je přístupný prostřednictvím API Identity ochrany riziko událostí v aplikaci Microsoft Graph.


## <a name="september-2017"></a>2017 září

### <a name="hotfix-for-identity-manager"></a>Oprava hotfix pro Identity Manager


**Typ:** změněné funkce  
**Kategorie služby:** Identity Manager  
**Schopnosti produktu:** Identity – Správa životního cyklu  



Balíček aktualizací oprav hotfix (sestavení 4.4.1642.0) je k dispozici od 25 září 2017 pro Identity Manager 2016 Service Pack 1. Tento balíček aktualizací:

- Řeší problémy a přidá vylepšení.
- Je kumulativní aktualizace, který nahrazuje všechny aktualizace Identity Manager 2016 Service Pack 1 až sestavení 4.4.1459.0 pro Identity Manager 2016. 
- Vyžaduje, abyste měli sestavení 4.4.1302.0 Identity Manager 2016. 

Další informace najdete v tématu [balíčku kumulativní opravy Hotfix (sestavení 4.4.1642.0) je k dispozici pro Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562). 

---
