---
title: Vyberte způsob správné ověřování pro vaše řešení hybridní identity Azure AD | Microsoft Docs
description: Tento průvodce pomáhá CEOs, ředitelé informačních technologií, CISOs, hlavní Identity architektům, Enterprise architekty a zabezpečení a IT rozhodují zodpovědná za výběru metody ověřování pro jejich hybridní řešení identit Azure AD ve středně velkých organizacích.
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 01b76ea902ec92f8ab32bc00cff27b1b890ce9ff
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113098"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Vyberte způsob správné ověřování pro vaše řešení hybridní identity Azure Active Directory 

Tento článek začne řadu články, které pomůžou organizacím implementaci kompletního řešení hybridní identity Azure Active Directory (Azure AD). Toto řešení se uvedené jako [hybridní Identity digitální transformace Framework](https://aka.ms/aadframework). Pokrývá výstupy obchodní a cíle organizace můžou zaměřit na implementovat robustní a zabezpečené hybridní řešení identit. 

První výsledek obchodní rozhraní hláskuje požadavky pro organizace k zabezpečení procesu ověřování, když uživatelé přistupují k cloudových aplikací. Prvním cílem obchodní v zabezpečené obchodní výsledek ověřování je možnost uživatelů přihlásit do cloudových aplikací pomocí jejich místní uživatelských jmen a hesel. Tento proces přihlášení k a jak se uživatelé ověřují, umožní všechno, co v cloudu.

Výběr správné metody ověřování je nejdůležitější pro organizace, které chtějí přesouvat své aplikace do cloudu. Nemáte rozmyslet toto rozhodnutí, z následujících důvodů:

1. Je první rozhodnutí pro organizaci, která chce přesunout do cloudu. 

2. Metoda ověřování je zásadní součástí sady přítomnosti organizace v cloudu. Jimi řídí přístup ke všem data v cloudu a prostředků.

3. Základ pro všechny ostatní pokročilým zabezpečením a funkce uživatelského rozhraní je ve službě Azure AD.

4. Metoda ověřování je obtížné změnit po implementaci.

Identita je nové rovině řízení zabezpečení IT. Proto ověřování je v organizaci přístup ochrana nové World cloudu. Organizace potřebují rovině řízení identity, která zvyšuje účinnost jejich zabezpečení a zajišťuje jejich cloudové aplikace před uživateli.

### <a name="out-of-scope"></a>Mimo rozsah
Organizace, které nemají existující místní adresář nároků nejsou fokus tohoto článku. Tyto firmy obvykle vytvářet identity jenom v cloudu, který nevyžaduje hybridní řešení identit. Cloudové identity výhradně v cloudu a nejsou přidruženy k odpovídající místních identit.

## <a name="authentication-methods"></a>Metody ověřování
Vaše nové rovině řízení po hybridní řešení identit Azure AD ověřování je základ pro přístup do cloudu. Výběr správné metody ověřování je velmi důležitý první rozhodnutí v nastavení hybridní řešení identit Azure AD. Implementujte metodu ověřování, která je nakonfigurována pomocí Azure AD Connect, který také zřizuje uživatele v cloudu.

Chcete-li zvolit metodu ověřování, je potřeba zvážit čas, stávající infrastruktury, složitost a náklady na implementaci vašeho výběru. Tyto faktory se liší pro každou organizaci a může časem změnit. 

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

Azure AD podporuje následující metody ověřování pro hybridních řešení identit.

### <a name="cloud-authentication"></a>Ověření cloudu
Pokud zvolíte tuto metodu ověřování, Azure AD zpracuje proces přihlášení uživatelů. Kombinaci s bezproblémové jednotné přihlašování (SSO), můžete přihlášení do cloudových aplikací bez nutnosti znovu zadejte své přihlašovací údaje. S ověřováním cloudu můžete zvolit ze dvou možností: 

**Synchronizaci hodnoty hash hesla služby Azure AD**. Nejjednodušší způsob, jak povolit ověřování pro místní adresář objekty ve službě Azure AD. Uživatelé mohou používat stejné uživatelské jméno a heslo, používají místní bez nutnosti jakékoli další infrastrukturu nasadit. Některé prémiové funkce Azure AD Identity Protection, jako je vyžaduje synchronizaci hodnoty hash hesla pro bez ohledu na to, jakou metodu ověřování zvolit.

> [!NOTE] 
> Hesla se nikdy uložený jako nešifrovaný text nebo šifrován reverzibilního algoritmus ve službě Azure AD. Další informace o procesu skutečné synchronizaci hodnoty hash hesla najdete v tématu [implementovat synchronizaci hodnoty hash hesla s Azure AD Connect sync](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization). 

**Azure AD předávací ověřování**. Poskytuje ověření jednoduché heslo pro ověřování služby Azure AD pomocí agenta softwaru, který běží na jeden nebo více místních serverů. Servery ověření uživatelů přímo s vaší místní služby Active Directory, které zajišťuje, že ověření hesla nedojde v cloudu. 

Společnosti, požadavek na zabezpečení okamžitě vynutit místního uživatele účtu stavy, zásad hesel a přihlašovací hodiny může použít tuto metodu ověřování. Další informace o procesu skutečné předávací ověřování najdete v tématu [přihlášení uživatele s Azure AD předávací ověřování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication).

### <a name="federated-authentication"></a>Federovaného ověřování
Pokud zvolíte tuto metodu ověřování, rukou Azure AD při procesu ověřování do samostatného důvěryhodné ověření systému, například místní Active Directory Federation Services (AD FS), ověřit heslo uživatele.

Ověřování systému můžete zadat další pokročilého ověřování požadavky. Příklady jsou ověřování pomocí čipových karet nebo jiných výrobců vícefaktorového ověřování. Další informace najdete v tématu [nasazení Active Directory Federation Services](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

Následující část vám pomůže zjistit, která metoda ověřování je pro vás nejvhodnější pomocí rozhodovací strom. Pomáhá určit, zda chcete provést nasazení cloudu nebo federovaného ověřování pro vaše řešení hybridní identity Azure AD.

## <a name="decision-tree"></a>Rozhodovací strom

![Azure AD authentication rozhodovací strom](media/azure-ad/azure-ad-authn-image1.png)

## <a name="detailed-considerations"></a>Podrobné požadavky

### <a name="cloud-authentication-password-hash-synchronization"></a>Cloudové ověřování: synchronizaci hodnoty hash hesla

* **Úsilí**. Synchronizaci hodnoty hash hesla je nejméně náročný týkající se nasazení, údržbu a infrastruktury.  Tato úroveň úsilí obvykle platí pro organizace, které stačí svým uživatelům přihlášení k Office 365, aplikace SaaS a jiné na základě AD prostředků Azure. Pokud je zapnutá, synchronizaci hodnoty hash hesla je součástí procesu synchronizace Azure AD Connect a spustí každé dvě minuty.

* **Činnost koncového uživatele**. K vylepšení možností přihlašování uživatelů, nasaďte bezproblémové přihlášení SSO se synchronizaci hodnoty hash hesla. Bezproblémové SSO eliminuje nepotřebné výzvy, když jsou uživatelé přihlášení.

* **Pokročilé scénáře**. Pokud organizace, je možné použít přehledy z identit s Azure AD Identity Protection sestavy. Příkladem je jako sestavu uniklé přihlašovací údaje. Windows Hello pro firmy je jinou možnost, která má [specifické požadavky při použití synchronizaci hodnoty hash hesla](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification). 

    Organizace, které vyžadují vícefaktorové ověřování s synchronizaci hodnoty hash hesla musí použití vícefaktorového ověřování Azure AD. Tyto organizace nemůže používat třetích stran nebo místní metody vícefaktorového ověřování.

* **Kontinuita podnikových procesů**. Pomocí cloudového ověřování synchronizaci hodnoty hash hesla je vysoce dostupný jako cloudová služba, která je škálovatelná pro všechny datových centrech společnosti Microsoft. Pokud chcete mít jistotu, že synchronizaci hodnoty hash hesla nepřekračuje mimo provoz delší dobu, nasaďte druhý server Azure AD Connect v pracovním režimu v konfiguraci úsporného režimu.

* **Aspekty**. Synchronizaci hodnoty hash hesla v současné době nepodporuje okamžitě vynutit změny ve stavu místní účet. V takovém případě má uživatel přístup k cloudových aplikací, dokud nedojde k synchronizaci stav účtu uživatele Azure AD. Organizace chtít, aby k překonání tohoto omezení spuštěním nové synchronizační cyklus po správci hromadné aktualizace stavů účet místního uživatele. Příklad zakazuje účty.

> [!NOTE]
> Platnost hesla a účtu zamčený stavy nejsou momentálně synchronizují do Azure AD s Azure AD Connect. 

Odkazovat na [implementace synchronizaci hodnoty hash hesla](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization) postup nasazení.

### <a name="cloud-authentication-pass-through-authentication"></a>Cloudové ověřování: předávací ověřování  

* **Úsilí**. Pro předávací ověřování, budete potřebovat jeden nebo více (doporučujeme tři) lightweight agentů nainstalovaných na existující servery. Tyto agenty musí mít přístup k vaší místní Active Directory Domain Services, včetně místní AD řadiče domény. Potřebují odchozí přístup k Internetu a přístup k vašim řadičům domény. Z tohoto důvodu není možné nasadit agenty v hraniční síti. 

    Předávací ověřování vyžaduje neomezeným síťový přístup k řadičům domény. Veškerý přenos v síti je šifrovaný a omezené na požadavky na ověření. Další informace o tomto procesu naleznete v tématu [deep Dive informace o zabezpečení](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-security-deep-dive) na předávací ověřování.

* **Činnost koncového uživatele**. K vylepšení možností přihlašování uživatelů, nasaďte bezproblémové jednotného přihlašování pomocí předávacího ověřování. Bezproblémové SSO eliminuje nepotřebné výzvy, po přihlášení uživatelé.

* **Pokročilé scénáře**. Předávací ověřování vynucuje zásady místního účtu při přihlášení. Například byl odepřen přístup, pokud je stav zakázán, účet místního uživatele uzamčen, nebo [platnost hesla](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) nebo spadá mimo dobu, kdy je povoleno přihlášení uživatele. 

    Organizace, které vyžadují vícefaktorové ověřování s předávací ověřování, musíte použít Azure Multi-Factor Authentication (MFA). Tyto organizace nelze použít metodu vícefaktorového ověřování třetích stran nebo místní. Pokročilé funkce vyžadují, aby synchronizaci hodnoty hash hesla nasazen, zda zvolíte předávací ověřování. Příkladem je sestava uniklé přihlašovací údaje Identity ochrany.

* **Kontinuita podnikových procesů**. Doporučujeme, abyste nasadili dva agenti velmi předávací ověřování. Tyto funkce jsou kromě prvního agenta na server Azure AD Connect. Tato další nasazení zajistí vysokou dostupnost požadavků na ověření. Až budete mít tři agentů nasazených, jednoho agenta můžete přesto úspěšná, když jiné agenta probíhá údržba. 

    Není k dispozici další výhody nasazení synchronizaci hodnoty hash hesla kromě předávací ověřování. Jedná jako metodu ověřování zálohování při metodu primární ověřování již není k dispozici.

* **Aspekty**. Můžete použít jako metodu ověřování zálohování pro předávací ověřování synchronizaci hodnoty hash hesla a agenty nelze ověřit pověření uživatele. Potom převzetí služeb při selhání pro synchronizaci hodnoty hash hesla nedojde automaticky. Přepínač metoda přihlašování ručně pomocí Azure AD Connect. 

    Předávací ověřování podporuje pouze cloudové aplikace, které používají moderní ověřování a konkrétními protokoly Exchange Online. Některé protokoly jsou ActiveSync POP3 a IMAP4. Například si Microsoft Office 2013 a novější podporu moderní ověřování, ale starší verze. Další informace o podpoře aplikace Office, najdete v části [moderní ověřování Office 365 aktualizovat](https://blogs.office.com/en-us/2015/11/19/updated-office-365-modern-authentication-public-preview/). Další důležité informace o předávací ověřování, včetně alternativní ID podporovat, najdete v části [nejčastější dotazy](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq).

Odkazovat na [implementace předávací ověřování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) postup nasazení.

### <a name="federated-authentication"></a>Federovaného ověřování

* **Úsilí**. Federovaného ověřování systému spoléhá na externím systému důvěryhodný k ověřování uživatelů. Některé společnosti se chcete znovu použít jejich investovali federované systému s jejich hybridní řešení identit Azure AD. Údržby a správy federovaných systému spadá mimo ovládací prvek služby Azure AD. Je to na organizaci pomocí federovaného systému a ujistěte se, že ho je bezpečně nasadit a dokáže zpracovat zátěž ověřování. 

* **Činnost koncového uživatele**. Činnost koncového uživatele federovaného ověřování závisí na implementaci funkcí, topologie a konfiguraci farmy federačních. Některé organizace potřebují tuto flexibilitu potřebují přizpůsobit a konfigurovat přístup do farmy federačních podle jejich potřeb zabezpečení. Například je možné nakonfigurovat interně připojené uživatele a zařízení k přihlášení uživatele automaticky, aniž by byli vyzváni k zadání pověření. Toto nastavení lze použít, protože se už přihlásili k jejich zařízení. V případě potřeby některé rozšířené funkce zabezpečení ztížit proces přihlášení uživatelů.

* **Pokročilé scénáře**. Při požadavku na ověřování, která nenabízí nativní podporu služby Azure AD mají zákazníci je obvykle potřeba řešení federovaného ověřování. Zobrazit podrobné informace vám pomohou [zvolit právo přihlášení](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/). Vezměte v úvahu následující běžné požadavky:

    * Ověřování, která vyžaduje čipové karty ani certifikáty.
    * Na místní servery MFA nebo vícefaktorové poskytovatelů třetích stran.
    * Ověřování pomocí řešení třetí strany ověřování. Najdete v článku [seznam kompatibility federace Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility).
    * Přihlášení, která vyžaduje sAMAccountName, například DOMÉNA\jméno_uživatele, místo hlavní název uživatele (UPN), například user@domain.com.

* **Kontinuita podnikových procesů**. Federované systémy obvykle vyžadují Vyrovnávání zatížení sítě řadu serverů, označuje jako farmy. Tato farma je nakonfigurován interní síti a topologie hraniční sítě k zajištění vysoké dostupnosti pro žádosti o ověření.

    Když metoda primární ověřování již není k dispozici, nasaďte synchronizaci hodnoty hash hesla společně s federovaného ověřování jako metodu ověřování zálohování. Příkladem je při místní servery nejsou k dispozici. Některé organizace velký podnik vyžadují federačních řešení pro podporu více vstupních bodů Internet nakonfigurované geografickou DNS pro požadavky na ověření nízkou latencí.

* **Aspekty**. Federované systémy obvykle vyžadují větších investice do místní infrastruktury. Většina organizací tuto možnost zvolte, pokud už mají místní federační investice. A pokud je silné obchodním požadavkem pro použití poskytovatele single-identity. Federace se nachází ve srovnání s řešení ověřování cloudu složitější pro provoz a řešení potíží.

Pro nepoužívající domény, který nelze ověřit ve službě Azure AD budete potřebovat další konfigurace implementovat ID přihlášení uživatele. Tento požadavek se označuje jako alternativního přihlašovacího ID podpory. V tématu [konfigurace alternativního přihlašovacího ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) pro omezení a požadavky. Pokud chcete použít poskytovatele služby Multi-Factor authentication třetích stran s federací, ověřte, že poskytovatel podporuje WS-Trust zařízením povolit, aby připojení k Azure AD.

Odkazovat na [nasazení federační servery](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) postup nasazení.

> [!NOTE] 
> Při nasazení služby Azure AD hybridní řešení identit, je nutné implementovat jednu z podporovaných topologií služby Azure AD Connect. Další informace o podporované a nepodporované konfigurace v [topologie pro Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-topologies).

## <a name="architecture-diagrams"></a>Architektura diagramy

Následující diagramy popisují základní architektura součásti požadované pro každou metodu ověřování, které můžete použít s Azure AD hybridní řešení identit. Poskytují přehled porovnání rozdíly mezi řešení.

* Jednoduchost řešení synchronizace hodnoty hash hesla:

    ![Hybridní identit Azure AD s synchronizaci hodnoty hash hesla](media/azure-ad/azure-ad-authn-image2.png)

* Požadavky agenta předávací ověřování:

    ![Hybridní identit Azure AD pomocí předávacího ověřování](media/azure-ad/azure-ad-authn-image3.png)

* Komponent potřebných pro federaci ve vaší hraniční a interní síti vaší organizace:

    ![Hybridní identit Azure AD s federovaného ověřování](media/azure-ad/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Porovnání metod

|Aspekt|Synchronizaci hodnoty hash hesla + bezproblémové jednotného přihlašování|Předávací ověřování + bezproblémové jednotného přihlašování|Federace se službou AD FS|
|:-----|:-----|:-----|:-----|
|Kde ověřování dojít?|V cloudu|V cloudu po ověření systému exchange zabezpečeného hesla s agentem místní ověřování|Lokálně|
|Jaké jsou požadavky na místní server rámec zřizování systému: Azure AD Connect?|Žádný|Jeden server pro každou další ověření agenta|Dva nebo více serverů služby AD FS<br><br>Dva nebo více serverů v síti hraniční/DMZ WAP|
|Jaké jsou požadavky na místní Internet a sítě rámec zřizování systému?|Žádný|[Odchozí přístup k Internetu](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start) ze serverů systémem ověřování agentů|[Příchozí přístup k Internetu](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/overview/ad-fs-requirements) WAP serverům v hraniční síti<br><br>Příchozí síťový přístup ke serverů služby AD FS z WAP serverů v hraniční síti<br><br>Vyrovnávání zatížení sítě|
|Je k dispozici požadavku na certifikát SSL?|Ne|Ne|Ano|
|Je k dispozici řešení pro sledování stavu?|Nepožaduje se|Stav agenta poskytované [centra pro správu Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication)|[Azure AD Connect Health](https://docs.microsoft.com/en-us/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs)|
|Uživatelé získají jednotného přihlašování k prostředkům cloudu ze zařízení připojeného k doméně v síti společnosti?|Ano s [bezproblémové jednotného přihlašování](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)|Ano s [bezproblémové jednotného přihlašování](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)|Ano|
|Jaké typy přihlášení jsou podporovány?|UserPrincipalName + heslo<br><br>Integrované ověřování systému Windows pomocí [bezproblémové jednotného přihlašování](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)<br><br>[Alternativním přihlašovacím ID](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-get-started-custom)|UserPrincipalName + heslo<br><br>Integrované ověřování systému Windows pomocí [bezproblémové jednotného přihlašování](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)<br><br>[Alternativním přihlašovacím ID](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq)|UserPrincipalName + heslo<br><br>sAMAccountName + heslo<br><br>Integrované ověřování systému Windows<br><br>[Ověřování certifikátu a čipové karty](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Alternativním přihlašovacím ID](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Je Windows Hello pro firmy, které jsou podporovány?|[Model klíče důvěryhodnosti](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Model důvěryhodnosti certifikátu s Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Model klíče důvěryhodnosti](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Model důvěryhodnosti certifikátu s Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Model klíče důvěryhodnosti](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Model důvěryhodnosti certifikátu](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Jaké jsou možnosti vícefaktorového ověřování?|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)<br><br>[Azure MFA serveru](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfaserver-deploy)<br><br>[MFA třetích stran](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)|
|Jaké stavy účet uživatele jsou podporovány?|Zakázané účty<br>(až 30 minut zpoždění)|Zakázané účty<br><br>Účet uzamčen<br><br>Platnost hesla vypršela.<br><br>Přihlašovací hodiny|Zakázané účty<br><br>Účet uzamčen<br><br>Platnost hesla vypršela.<br><br>Přihlašovací hodiny|
|Jaké jsou možnosti podmíněného přístupu?|[Podmíněný přístup pro Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Podmíněný přístup pro Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Podmíněný přístup pro Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)<br><br>[Pravidla deklarace identity služby AD FS](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Blokuje starších protokolů, které jsou podporovány?|[Ano](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[Ano](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[Ano](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Můžete upravit loga, obrázku a popis na stránkách přihlášení?|[Ano, s Azure AD Premium](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Ano, s Azure AD Premium](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Ano](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|Jaké pokročilé scénáře jsou podporovány?|[Inteligentní heslo uzamčení](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-secure-passwords)<br><br>[Úniku přihlašovacích údajů sestavy](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-risk-events)|[Inteligentní heslo uzamčení](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|Nasazení ve více lokalitách s nízkou latencí ověřování systému<br><br>[Uzamčení extranetu služby AD FS](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection)<br><br>[Integrace se systémy jiných výrobců identit](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

## <a name="recommendations"></a>Doporučení
Systém identit zajišťuje přístup uživatelů k cloudových aplikací a -obchodní aplikace můžete migrovat a zpřístupnit v cloudu. Aby oprávněným uživatelům produktivitu a nesprávnými účastníky z vaší organizace citlivá data ověřování řídí přístup k aplikacím.

Použít nebo povolit synchronizaci hodnoty hash hesla pro kteroukoli metodu ověřování, můžete zvolit z následujících důvodů:

1. **Vysoká dostupnost a zotavení po havárii**. Předávací ověřování a federation závisí na místní infrastrukturu. Pro předávací ověřování nároky na místní zahrnuje hardware serveru a sítě předávací ověřování vyžadovat agenty. Pro federaci je i větší nároky na místní. To vyžaduje servery v hraniční síti k žádosti o ověření proxy serveru a interní federačních serverech. 

    Chcete-li Vyhýbejte se jediným bodů selhání, nasaďte redundantní servery. Potom žádosti o ověření budou vždy obsloužení Pokud všechny součásti, které se nezdaří. Předávací ověřování a federaci také závisí na řadičích domény reagovat na požadavky na ověřování, které může selhat také. Mnoho z těchto součástí potřebovat údržby zůstane v pořádku. Když údržby není naplánovat a implementovat správně výpadků budou s větší pravděpodobností. Brání výpadkům pomocí synchronizaci hodnoty hash hesla, protože služba Microsoft Azure AD cloud ověřování škáluje globálně a je vždy k dispozici.

2. **Místní výpadku základními**.  Důsledky místní výpadku způsobeného internetový útok nebo po havárii může být výrazně, od reputational brand škody až na ochrnuté pacienty organizaci nelze k řešení útoku. Mnoho organizací byly nedávno obětí malwarových útoků, včetně cílové ransomware, která způsobila, že jejich místní servery přejdete. Když Microsoft pomáhá zákazníkům řešit tyto typy útoků, vidí dvě kategorie organizací:

   * Organizace, které dřív zapnutý synchronizaci hodnoty hash hesla změnit jejich metodu ověřování pro použití synchronizaci hodnoty hash hesla. Byly během několika hodin do režimu online. Pomocí přístupu k e-mailu prostřednictvím Office 365, šlo vyřešte problémy a přístup k jiné cloudové úlohy.

   * Organizace, které nebylo dříve povolit synchronizaci hodnoty hash hesla měly uchýlit k nedůvěryhodné externí příjemce e-mailových systémů komunikace a řešení problémů. V takových případech trvalo je týdnů, která chcete být znovu spuštěn a.

3. **Ochrana identity**. Jedním z nejlepší způsobů, jak chránit uživatele v cloudu je Azure AD Identity Protection. Microsoft neustále kontroluje Internetu pro uživatele a heslo uvádí, že nesprávnými účastníky prodeje a zpřístupnit v tmavý webu. Azure AD můžete tyto informace slouží k ověření, pokud žádné z uživatelských jmen a hesel ve vaší organizaci, znamená to ohrožení. Proto je důležité pro povolení synchronizace hesel hash bez ohledu na to, jakou metodu ověřování budete používat, zda je federovaný, nebo předávací ověřování. Uniklé přihlašovací údaje jsou uvedené jako sestavu. Tyto informace slouží k blokování nebo přimějte uživatele ke změně hesla při pokusu přihlásit pomocí uniklé hesla.

Nakonec souladu se [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html), společnost Microsoft nemá sadu vybavená úplné funkce správy identit a přístupu. Obslužné rutiny Microsoft [žádosti o ověření 450 miliardy](https://www.microsoft.com/en-us/security/intelligence-report) každý měsíc pro poskytnutí přístupu k tisícům aplikací SaaS, jako je Office 365 z libovolného zařízení. 

## <a name="conclusion"></a>Závěr

Tento článek popisuje různé možnosti ověřování, které organizace můžete nakonfigurovat a nasadit pro podporu přístupu do cloudových aplikací. Ke splnění různé firmy, zabezpečení a technické požadavky, organizace zvolit synchronizaci hodnoty hash hesla, předávací ověřování a federace. 

Zvažte každou metodu ověřování. Podporuje úsilí nezbytné k nasazení řešení a možnosti pro uživatele procesu přihlašování, cílit na vaše obchodní požadavky? Vyhodnoťte, jestli vaše organizace potřebuje pokročilé scénáře a obchodní kontinuitu podnikových procesů jednotlivých metod ověřování. Nakonec vyhodnoťte důležité informace o jednotlivých metod ověřování. Některý z nich zabránit vám v implementaci zvoleného?

## <a name="next-steps"></a>Další postup

V dnešním světě hrozby jsou dispozici 24 hodin denně a pocházejí z everywhere. Implementovat správné metody ověřování, a bude zmírnění rizik zabezpečení a ochraně vaší identity.

[Začínáme](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad) s Azure AD a nasaďte řešení správné ověřování pro vaši organizaci.

Pokud uvažujete o migraci z federovaných cloudové ověřování, další informace o [změna metoda přihlašování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method). Při plánování a implementaci migrace, použijte [tyto plány nasazení projektu](http://aka.ms/deploymentplans).
