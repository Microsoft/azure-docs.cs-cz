---
title: Vyberte způsob správné ověřování pro vaše řešení hybridní identity Azure AD | Microsoft Docs
description: Tato příručka je určená pro pomoc CEOs, ředitelé informačních technologií, CISOs, hlavní Identity architektům, Enterprise architekty a zabezpečení a IT rozhodují zodpovědná za výběru metody ověřování pro jejich hybridní řešení identit Azure AD ve středně velkých organizace.
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: eb5fb008e602c2026e57d3436875ec485b350af8
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="choosing-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Výběr metody správné ověřování pro vaše řešení hybridní identity Azure Active Directory 

Tento článek je první v celé řady umožňující organizacím implementovat úplná hybridní řešení identit Azure AD. Úplná hybridní řešení identit Azure AD byl uvedené jako hybridní Identity digitální transformace Framework a zahrnuje obchodní výstupy a cíle organizace by měla soustředit na a ujistěte se implementují robustní a zabezpečené hybridní identita řešení. První výsledek obchodní rozhraní hláskuje požadavky pro organizace k zabezpečení procesu ověřování, když uživatelé přistupují k cloudových aplikací. Prvním cílem obchodní v zabezpečené obchodní výsledek ověřování je možnosti pro uživatele k přihlášení do cloudových aplikací pomocí jejich místní uživatelských jmen a hesel. Tento proces přihlášení a jak ověřovat uživatele umožňuje všechno, co v cloudu.

Výběr správné metody ověřování je nejdůležitější pro organizace, které chtějí přesouvat své aplikace do cloudu. Toto rozhodnutí by neměl přijme lehkým z následujících důvodů:

1. Je první rozhodnutí v organizaci chce přesunout do cloudu. 

2. Metoda ověřování je zásadní součástí sady přítomnosti organizace v cloudu, řízení přístupu ke všem data v cloudu a prostředků.

3. Základ pro všechny ostatní pokročilým zabezpečením a funkce uživatelského rozhraní je ve službě Azure AD.

4. Obtížné problémy při změně jednou implementovat metodu ověřování.

Ověřování s identitou jako nový rovině řízení zabezpečení IT je ochrana přístupu v organizaci nové World cloudu. Organizace by měl Ujistěte se, že roviny ovládací prvek identity umožňuje jejich silnější zabezpečení a udržovat své cloudové aplikace před uživateli.

### <a name="out-of-scope"></a>Mimo rozsah

Organizace, které nemají existující místní adresář nároků nejsou fokus tohoto článku. Tyto firmy obvykle vytvářet identity jenom v cloudu, který nevyžaduje hybridní řešení identit. Cloudové identity výhradně v cloudu a nejsou přidruženy k odpovídající místních identit.  

## <a name="choosing-the-right-authentication-method"></a>Výběr metody správné ověřování

Ověřování s Azure AD hybridní řešení identit jako nový rovině řízení, je základ pro přístup do cloudu. Výběr správné metody ověřování je velmi důležitý první rozhodnutí v nastavení hybridní řešení identit Azure AD. Implementace metody ověřování je nakonfigurovat pomocí služby Azure AD Connect, který také zřízení uživatelů v cloudu. 

Chcete-li zvolit metodu ověřování, je potřeba zvážit čas, stávající infrastruktury, složitost a náklady na implementaci vašeho výběru. Tyto faktory se liší pro každou organizaci a může časem změnit. 

Azure AD podporuje následující metody ověřování pro řešení hybridní identity:

### <a name="cloud-authentication"></a>Ověření cloudu
Pokud zvolíte tuto metodu ověřování, Azure AD zpracuje proces přihlášení pro uživatele. Kombinaci s bezproblémové jednotné přihlašování (SSO), můžete přihlášení do cloudových aplikací bez nutnosti znovu zadejte své přihlašovací údaje. Pomocí cloudového ověřování můžete zvolit ze dvou možností: 

**Heslo Hash synchronizace (PBS)** – nejjednodušší způsob, jak povolit ověřování pro místní adresář objekty ve službě Azure AD. Synchronizace hodnot Hash hesel umožňuje uživatelům používat stejné uživatelské jméno a heslo, používají místně bez nutnosti jakékoli další infrastrukturu nasadit. Některé prémiové funkce Azure AD, jako je například ochrany identit vyžaduje synchronizace hodnot Hash hesel, bez ohledu na to, který je vybraná metoda ověřování.

> [!NOTE] 
> Hesla se nikdy uložený jako nešifrovaný text nebo šifrován reverzibilního algoritmus ve službě Azure AD. Další informace o samotný proces synchronizace hodnot Hash hesel najdete v tématu [implementovat synchronizaci hodnoty hash hesla s Azure AD Connect sync](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization). 

**Předávací ověřování (ESP)** – poskytuje jednoduché heslo ověření pro ověřovací služby Azure AD pomocí softwaru agenta spuštěného na jeden nebo více místními servery k ověření uživatelů přímo s vaší místní Active Directory zajistit ověření hesla nedojde v cloudu. Společnosti, požadavek na zabezpečení okamžitě vynutit místní uživatelský účet stavy, zásad hesel a přihlašovací hodiny by použít tuto metodu ověřování. Další informace o procesu skutečné předávací ověřování najdete v tématu [přihlášení uživatele s předávací ověřování Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication).

### <a name="federated-authentication"></a>Federovaného ověřování
Pokud vyberete tuto metodu ověřování Azure AD rukou mimo proces ověřování k samostatného důvěryhodné ověření systému, například místní Active Directory Federation Services (AD FS) k ověření pro uživatele heslo. Ověřování systému můžete zadat další ověřovací požadavky, například ověřování pomocí čipových karet nebo vícefaktorového ověřování jiných výrobců. Další informace najdete v tématu [nasazení Active Directory Federation Services](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

V následující části vám pomohou při rozhodování, jakou metodu ověřování je pro vás, pomocí rozhodovací strom. Pomůže vám určit, zda chcete nasadit cloudu nebo federovaného ověřování pro vaše řešení hybridní identity Azure AD.

## <a name="azure-ad-authentication-decision-tree"></a>Azure AD authentication rozhodovací strom

![image1](media/azure-ad/azure-ad-authn-image1.png)

## <a name="detailed-considerations-on-authentication-methods"></a>Podrobné požadavky na metody ověřování

### <a name="cloud-authentication-password-hash-sync"></a>Cloudové ověřování: synchronizace hodnot Hash hesel 

* **Úsilí:** synchronizací hodnoty Hash hesla je nejméně náročný týkající se nasazení, údržbu a infrastruktury pro organizace, kteří potřebují jenom umožnit uživatelům přihlášení k Office 365, aplikace SaaS a jiné na základě AD prostředků Azure. Když je povolené, synchronizací hodnoty Hash hesla je součástí procesu synchronizace Azure AD Connect a spustí každé dvě minuty. 

* **Činnost koncového uživatele:** se doporučuje organizace nasazuje bezproblémové jednotné přihlašování (SSO) s synchronizaci hodnot Hash hesel vyloučení nepotřebných výzvy po jejich přihlášení vylepšit možnosti přihlašování pro uživatele.

* **Pokročilé scénáře:** Pokud organizace, je možné použít přehledy z identit s Azure AD Identity Protection sestav, jako je například sestavu uniklé přihlašovací údaje. Windows Hello pro firmy je jinou možnost, která má [specifické požadavky při použití synchronizace hodnot Hash hesel](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification). Organizace, které vyžadují služby Multi-Factor authentication se synchronizací hodnoty Hash hesla musí použití vícefaktorového ověřování Azure AD a nemůžete použít třetích stran nebo místní služby Multi-Factor authentication metody.

* **Kontinuita podnikových procesů:** synchronizací hodnoty Hash hesla je ze své podstaty vysoce dostupný jako cloudová služba, která je škálovatelná pro všechny datových centrech společnosti Microsoft. Doporučujeme, aby druhý server Azure AD Connect je nasazena v pracovním režimu v konfiguraci pohotovostní pro účely obnovení po havárii.

* **Aspekty:** synchronizace hodnot Hash hesel nevynucuje okamžitě změny ve stavu místní účet aktuálně. V takovém případě bude mít uživatel přístup do cloudových aplikací, dokud nedojde k synchronizaci stav účtu uživatele Azure AD. Pokud organizace, aby k překonání tohoto omezení, se doporučuje novou synchronizační cyklus se aktivuje po správci hromadné aktualizace místní stavů účet uživatele, například zakázání účtů. 

> [!NOTE] 
> Heslo vyprší a účet uzamčen stavy nejsou momentálně synchronizují do Azure AD s Azure AD Connect. 

Odkazovat na [implementace synchronizace hodnot Hash hesel](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) postup nasazení.

### <a name="cloud-authentication-pass-through-authentication"></a>Cloudové ověřování: předávací ověřování  

* **Úsilí:** pro předávací ověřování, budete potřebovat další (tři doporučeno) nebo lightweight agentů nainstalovaných na existující servery s přístupem k vaší místní Active Directory Domain Services, včetně přístupu k místní domény služby AD řadiče. Tyto agenty potřebují odchozí přístup k Internetu a mít tak přístup k vašim řadičům domény. Z tohoto důvodu není možné nasadit agenty v hraniční síti, protože vyžaduje neomezeným síťový přístup k řadičům domény. Veškerý přenos v síti je šifrovaný a omezené na požadavky na ověření. Další informace o tomto procesu naleznete v tématu [deep Dive informace o zabezpečení](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-security-deep-dive) na předávací ověřování.

* **Činnost koncového uživatele:** se doporučuje, aby nasazováním bezproblémové jednotné přihlašování s předávací ověřování k vyloučení nepotřebných výzvy po jejich přihlášení vylepšit možnosti přihlašování pro uživatele.

* **Pokročilé scénáře:** předávací ověřování vynucuje zásady místního účtu při přihlášení. Například je odepřen přístup, pokud je stav zakázán, účet místního uživatele uzamčen, [platnost hesla](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication ) nebo spadá mimo uživatele povolené doby přihlášení.  Organizace, které vyžadují služby Multi-Factor authentication s předávací ověřování musíte použít Azure Multi-Factor Authentication (MFA) a nemůžete použít metodu třetích stran nebo místní služby Multi-Factor authentication. Pokročilé funkce, jako například sestavu uniklé přihlašovací údaje Identity ochrany vyžaduje, aby synchronizací hodnoty Hash hesla je nasazené bez ohledu na to pokud si zvolíte předávací ověřování.

* **Kontinuita podnikových procesů:** se doporučuje nasazení dvou velmi předávací ověřování agentů, kromě prvního agenta na server Azure AD Connect k zajištění vysoké dostupnosti požadavků na ověření. Až budete mít tři agentů nasazených, jednoho agenta můžete přesto úspěšná, když jiné agenta probíhá údržba. Další výhodou nasazení synchronizace hodnot Hash hesel kromě předávací ověřování, je, že může fungovat jako metodu zálohování ověřování, když primární ověřování metoda již není k dispozici, například při místní servery nejsou k dispozici.

* **Aspekty:** Pokud jako metodu ověřování zálohování pro předávací ověřování použijete synchronizace hodnot Hash hesel a agenty nelze ověřit pověření uživatele, pak převzetí služeb při selhání synchronizace hodnot Hash hesel neprobíhá automaticky. Potřebovali byste přepnout metoda přihlašování ručně pomocí služby Azure AD Connect. Předávací ověřování podporuje pouze cloudové aplikace, které používají moderní ověřování a konkrétními protokoly Exchange Online jako ActiveSync, POP3 a IMAP4. Například [Microsoft Office 2013 a novější podporuje moderní ověřování, ale ne starší verze](https://blogs.office.com/en-us/2015/11/19/updated-office-365-modern-authentication-public-preview/) na další informace podpora aplikací Office. V tématu [nejčastější dotazy](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq) a další důležité informace na předávací ověřování, včetně alternativní ID podporovat.

Odkazovat na [implementace předávací ověřování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) postup nasazení.

### <a name="federated-authentication"></a>Federovaného ověřování

* **Úsilí:** pomocí federovaného ověřování systému závisí na externím systému k ověřování uživatelů. Některé společnosti se chcete znovu použít jejich investovali federované systému s jejich hybridní řešení identit Azure AD. Údržby a správy federovaných systému spadá mimo ovládací prvek služby Azure AD. Je v organizaci pomocí federovaného systému a ujistěte se, že ho je bezpečně nasadit a dokáže zpracovat zátěž ověřování. 

* **Činnost koncového uživatele:** na uživatelské zkušenosti federovaného ověřování je závislá na provádění funkcí, topologie a konfiguraci farmy federačních. Některé organizace vyžadují tuto flexibilitu potřebují přizpůsobit a konfigurovat přístup do farmy federačních podle jejich potřeb zabezpečení. Například je možné nakonfigurovat interně připojené uživatele a zařízení k přihlášení uživatele automaticky bez zobrazení výzvy k zadání pověření, protože budou mít již přihlášeného k jejich zařízení. Na druhé straně v případě potřeby některé rozšířené funkce zabezpečení provádět uživatele přihlásit proces obtížnější.

* **Pokročilé scénáře:** řešení ověřování federovaný je obvykle vyžaduje v případě zákazníků požadavku na ověření není nativně podporovány službou Azure AD, podrobné informace o [tady](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/), ale běžné požadavky:

    * Ověřování, které vyžadují čipové karty ani certifikáty
    * Pomocí místní MFA Server nebo poskytovatele služby Multi-Factor třetích stran.
    * Ověřování pomocí řešení třetí strany ověřování. Najdete v článku [seznam kompatibility federace Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility).
    * Uživatelé musí přihlásit pomocí svých sAMAccountName, například doména\uživatelské jméno, místo použití hlavní název uživatele (UPN), například user@domain.com

* **Kontinuita podnikových procesů:** federovaný systémy obvykle vyžadují zatížení vyrovnáváním pole serverů, také známé jako farma, nakonfigurované v k interní topologie sítě a hraniční sítě k zajištění vysoké dostupnosti pro žádosti o ověření. Synchronizace hodnot Hash hesel se dá nasadit společně s federovaný ověřování jako metodu ověřování zálohování při metodu primární ověřování již není k dispozici, například při místní servery nejsou k dispozici. Některé organizace velký podnik vyžadují federačních řešení pro podporu více vstupních bodů Internet nakonfigurované geografickou DNS pro požadavky na ověření s nízkou latencí.

* **Aspekty:** federovaný systémy obvykle vyžadují větších investice do místní infrastruktury. Většina organizací tuto možnost zvolte, pokud už mají místní federační investicemi a je silné obchodním požadavkem pro použití poskytovatele jedinou identitu. Federace se nachází ve srovnání s řešení ověřování cloudu složitější pro provoz a řešení potíží. ID uživatele pomocí směrovat domény, který nelze ověřit ve službě Azure AD se přihlásit k implementaci potřebují další konfigurace. Tento požadavek se označuje jako alternativního přihlašovacího ID podpory. V tématu [konfigurace alternativního přihlašovacího ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) pro omezení a požadavky. Pokud si zvolíte pro použití poskytovatele služby Multi-Factor authentication třetích stran s federací, ujistěte se, že poskytovatel podporuje WS-Trust zařízením povolit, aby připojení k Azure AD.

Odkazovat na [implementace federační služby](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) postup nasazení.

> [!NOTE] 
> Při nasazení služby Azure AD hybridní řešení identit, je nutné zajistit, že můžete implementovat jednu z podporovaných topologií služby Azure AD Connect. Další informace o podporované a nepodporované konfigurace v [topologie pro Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-topologies).

## <a name="architecture-diagrams"></a>Architektura diagramy

Následující diagram obrysy Základní architektura součásti požadované pro každou metodu ověřování, které můžete použít s Azure AD hybridní řešení identit. Poskytuje přehled k porovnání rozdíly mezi řešení.

Následující diagram popisuje jednoduchost řešení synchronizace hodnot Hash hesel:

![PBS](media/azure-ad/azure-ad-authn-image2.png)

Následující diagram popisuje požadavky agenta předávací ověřování:

![PTA](media/azure-ad/azure-ad-authn-image3.png)

Následující diagram popisuje součásti požadované pro federaci ve vaší hraniční a interní síti vaší organizace:

![ADFS](media/azure-ad/azure-ad-authn-image4.png)

## <a name="comparing-the-authentication-methods"></a>Porovnání metod ověřování

|Aspekt|Synchronizace hodnot Hash hesel + bezproblémové jednotného přihlašování|Předávací ověřování + bezproblémové jednotného přihlašování|Federace se službou AD FS|
|:-----|:-----|:-----|:-----|
|Kde ověřování dojít?|V cloudu|V cloudu, po ověření systému exchange zabezpečeného hesla s agentem místní ověřování|Lokálně|
|Jaké jsou požadavky na místní server rámec zřizování systému: Azure AD Connect?|Žádný|1 serveru pro každý další ověření agenta|2 nebo více serverů služby AD FS<br>2 nebo více serverů WAP v síti hraniční nebo hraniční sítě|
|Co jsou místní Internetové a síťové požadavky rámec zřizování systému?|Žádný|[Odchozí přístup k Internetu](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start) ze serverů systémem ověřování agentů|[Příchozí přístup k Internetu](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/overview/ad-fs-requirements) WAP serverům v hraniční<br>Příchozí síťový přístup na servery služby AD FS z WAP serverů v hraniční<br>Vyrovnávání zatížení sítě|
|Je k dispozici požadavku na certifikát SSL?|Ne|Ne|Ano|
|Je k dispozici řešení pro sledování stavu?|Nepožaduje se|Stav agenta poskytované [centra pro správu Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication)|[Azure AD Connect Health](https://docs.microsoft.com/en-us/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs)|
|Uživatelé získají jednotného přihlašování k prostředkům cloudu ze zařízení připojeného k doméně v síti společnosti?|Ano s [bezproblémové jednotného přihlašování](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)|Ano s [bezproblémové jednotného přihlašování](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)|Ano|
|Jaké typy přihlášení jsou podporovány?|UserPrincipalName + heslo<br>Integrované ověřování systému Windows pomocí [bezproblémové jednotného přihlašování](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)<br>[Alternativním přihlašovacím ID](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-get-started-custom)|UserPrincipalName + heslo<br>Integrované ověřování systému Windows pomocí [bezproblémové jednotného přihlašování](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)<br>[Alternativním přihlašovacím ID](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq)|UserPrincipalName + heslo<br>sAMAccountName + heslo<br>Integrované ověřování systému Windows<br>[Ověřování certifikátu a čipové karty](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br>[Alternativním přihlašovacím ID](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Je Windows Hello pro firmy, které jsou podporovány?|[Model klíče důvěryhodnosti](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[Model důvěryhodnosti certifikátu s Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Model klíče důvěryhodnosti](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[Model důvěryhodnosti certifikátu s Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Model klíče důvěryhodnosti](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[Model důvěryhodnosti certifikátu](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Jaké jsou možnosti služby Multi-Factor authentication?|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)<br>[Azure MFA serveru](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfaserver-deploy)<br>[MFA třetích stran](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)|
|Jaké stavy účet uživatele jsou podporovány?|Zakázané účty<br>(až 30 minut zpoždění)|Zakázané účty<br>Účet uzamčen<br>Platnost hesla vypršela.<br>Doby přihlášení|Zakázané účty<br>Účet uzamčen<br>Platnost hesla vypršela.<br>Doby přihlášení|
|Jaké jsou možnosti podmíněného přístupu?|[Azure AD podmíněného přístupu ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Azure AD podmíněného přístupu ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Azure AD podmíněného přístupu](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)<br>[Pravidla deklarace identity služby AD FS](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Blokuje starších protokolů, které jsou podporovány?|Ne|Ne|[Ano](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Můžete je přizpůsobit, loga, obrázku a popis na stránkách přihlášení?|[Ano s Azure AD Premium](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Ano s Azure AD Premium](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Ano](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|Jaké pokročilé scénáře jsou podporovány?|[Inteligentní heslo uzamčení](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-secure-passwords)<br>[Úniku přihlašovacích údajů sestavy](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-risk-events)|[Inteligentní heslo uzamčení](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|Ověřování systému více lokalit s nízkou latencí<br>[Uzamčení extranetu služby AD FS](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection)<br>[Integrace se systémy jiných výrobců identity](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

## <a name="recommendations-and-considerations-from-azure-ad"></a>Doporučení a důležité informace z Azure AD

Systém identit zajistí, že mají vaši uživatelé přístup do cloudu-obchodní aplikace můžete migrovat a zpřístupnit v cloudu. Ověřování řídí přístup k aplikacím, aby oprávnění uživatelé produktivitu a nesprávnými účastníky z vaší organizace citlivá data. Z tohoto důvodu zvažte následující doporučení v výběr metody správné ověřování pro vaši organizaci. 

Použít nebo povolení synchronizace hodnot Hash hesel, bez ohledu na to, jakou metodu ověřování zvolíte, z následujících důvodů:

1. **Vysoká dostupnost a zotavení po havárii:** předávací ověřování a federation závisí na místní infrastrukturu. Pro předávací ověřování zahrnuje nároky na místní hardware serveru a sítě, které vyžadují agenty předávací ověřování. Pro federaci je i větší nároky na místní, protože vyžaduje servery v hraniční síti k žádosti o ověření proxy serveru a interní federačních serverech. K Vyhýbejte se jediným bodů selhání, musí vaše organizace nasadit redundantní servery zajistit žádosti o ověření jsou obsluhovány vždycky, pokud se nezdaří všechny součásti, které. Předávací ověřování a federation jsou také závisí na řadičích domény reagovat na požadavky na ověřování, které můžete také nezdaří. Mnoho z těchto součástí potřebovat údržby zůstane v pořádku a výpadky budou s větší pravděpodobností údržby není naplánování a implementovaná správně. Výpadky můžete zabránit pomocí synchronizace hodnot Hash hesel, protože služba pro cloudové ověřování společnosti Microsoft Azure AD škáluje globálně a je vždy k dispozici.

2. **Místní výpadku základními:** důsledky místní výpadku způsobeného internetový útok nebo po havárii může být výrazně, od reputational brand škody až na ochrnuté pacienty organizaci nelze k řešení útoku. Mnoho organizací v posledním roce byly obětí malwarových útoků, včetně cílové ransomware, která způsobila, že jejich místní servery za zastavený. V pomáhá zákazníkům řešit tyto typy útoků, Microsoft si všimli dvě kategorie organizací:

   a. Organizace, které dříve povolena synchronizace hodnot Hash hesel se změnou jejich metodu ověřování pro použití synchronizace hodnot Hash hesel v několika hodin do režimu online. Pomocí přístupu k e-mailu prostřednictvím Office 365, může fungují k vyřešení problémů a mají přístup k jiné cloudové úlohy.

   b. Organizace, které nebylo dříve povolení synchronizace hodnot Hash hesel měl uchýlit k nedůvěryhodné externí příjemce e-mailových systémů komunikace a řešení problémů. V takových případech trvalo je týdnů, která chcete být znovu spuštěn a.

3. **Ochrana identity:** jedním z nejlepší způsobů, jak chránit uživatele v cloudu je Azure AD Identity Protection. Microsoft neustále kontroluje Internetu pro uživatele a heslo seznamy, nesprávnými účastníky prodeje a zpřístupnit v tmavý webu. Azure AD můžete tyto informace slouží k ověření, pokud žádné z uživatelských jmen a hesel ve vaší organizaci, znamená to ohrožení. Proto je důležité, aby synchronizace hodnot Hash hesel bez ohledu na to, jakou metodu ověřování budete používat, zda se federované nebo předávací ověřování. Uniklé přihlašovací údaje jsou prezentované jako sestavu a slouží k blokování nebo vynutit uživatele Chcete-li změnit své heslo při pokusu přihlásit pomocí uniklé heslo.

Nakonec souladu se [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html), společnost Microsoft nemá sadu nejvíce plné funkce Identity a správu přístupu. Obslužné rutiny Microsoft [žádosti o ověření 450 miliardy](https://www.microsoft.com/en-us/security/intelligence-report) každý měsíc pro poskytnutí přístupu k tisícům aplikací SaaS, jako je Office 365 z libovolného zařízení. 

## <a name="conclusion"></a>Závěr

V tento článek obsahuje přehled různých možností ověřování, organizace, můžete nakonfigurovat a nasadit pro podporu přístupu do cloudových aplikací. Ke splnění různých obchodních, zabezpečení a technických požadavků, organizace zvolit synchronizace hodnot Hash hesel a předávací ověřování, federace. S každou metodu ověřování vaší organizace vyberte, zda jsou vaše podnikové požadavky používala úsilí nezbytné k nasazení řešení a činnost koncového uživatele o procesu přihlášení. Musíte taky k vyhodnocení, zda vaše organizace potřebuje pokročilé scénáře a obchodní kontinuitu podnikových procesů jednotlivých metod ověřování. Nakonec bude třeba vyhodnotit aspektů každá metoda ověřování, pokud chcete zobrazit, pokud žádné bránit implementace vlastního výběru.

## <a name="next-steps"></a>Další postup

V dnešním světě hrozby jsou dispozici 24 hodin denně a pocházejí z everywhere. Implementace metody správné ověření pomůže zmírnění rizik zabezpečení a ochraně vaší identity. 

[Začínáme](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad) s Azure AD a nasaďte řešení správné ověřování pro vaši organizaci.

Pokud uvažujete o migraci z federovaného ověřování cloudu, další [o změně přihlašovací metoda](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method). Při plánování a implementaci migrace, můžete použít [tyto plány, které vám pomůže projektu](http://aka.ms/deploymentplans).
