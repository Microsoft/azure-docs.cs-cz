---
title: Vyberte metodu ověřování pravý pro vaši hybridní řešení identit Azure AD | Dokumentace Microsoftu
description: Tento průvodce pomůže CEOs, vedoucí, CISOs, Chief Identity architekty, podnikové architekty a zabezpečení a IT pracovníky s rozhodovací pravomocí odpovědnost za výběr metody ověřování pro jejich hybridní řešení identit Azure AD ve středně velkých až velkých organizacích.
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: b0f017e53d112e10a656b4a4effdaa9d902f238c
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53995197"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Vyberte metodu správné ověřování pro vaše řešení hybridní identity Azure Active Directory 

Tento článek začne série článků, která pomáhají organizacím implementovat kompletní řešení hybridní identity Azure Active Directory (Azure AD). Toto řešení bylo uvedeno jako [rozhraní digitální transformace pro hybridní Identity](https://aka.ms/aadframework). Zabývá se obchodních výsledků a cíle organizace můžou zaměřit na implementovat robustní a zabezpečené hybridní řešení identit. 

První obchodního výsledku rozhraní Framework obsahuje požadavky organizace na zabezpečení procesu ověřování, když uživatelé přistupují k cloudových aplikací. První obchodní cíle v zabezpečené obchodního výsledku ověřování je možnost uživatelů přihlásit ke cloudovým aplikacím pomocí jejich místních uživatelských jmen a hesel. Tento proces přihlášení k a jak se uživatelé ověřovat umožňují vše v cloudu.

Volba správné metody ověřování je nejdůležitější pro organizace, kteří chtějí přesuňte svoje aplikace do cloudu. Nevyřídí toto rozhodnutí lehce, z následujících důvodů:

1. Je to první rozhodnutí v organizaci, která chce přesunout do cloudu. 

2. Metoda ověřování je zásadní součástí sady řešení v organizaci přítomnost v cloudu. Určuje přístup ke všem data v cloudu a prostředků.

3. Je základem pro všechny ostatní pokročilé zabezpečení a funkce uživatelského prostředí ve službě Azure AD.

4. Metoda ověřování je obtížné změnit po implementaci.

Identita je nový roviny řízení zabezpečení IT. Ověřování je v organizaci přístup guard na novém světě cloudu. Organizace potřebují rovina řízení identit, která posiluje zabezpečení a sleduje jejich cloudových aplikací před případné útočníky.

### <a name="out-of-scope"></a>Mimo rozsah
Organizace, které nemají existující místní adresář nároky na místo nejsou hlavním cílem tohoto článku. Tyto firmy obvykle vytvoření identity jenom v cloudu, který nevyžaduje, aby řešení s hybridní identitou. Čistě cloudové identity výhradně v cloudu a nejsou přidruženy k odpovídající místních identit.

## <a name="authentication-methods"></a>Metody ověřování
Po hybridní řešení identit Azure AD vaší nové rovina řízení se ověřování jsou základem cloud přístup. Volba správné metody ověřování je velmi důležitý první rozhodnutí při nastavování hybridní řešení identit Azure AD. Implementujte metodu ověřování, který je nakonfigurovaný pomocí Azure AD Connect, která také zřizuje uživatele v cloudu.

Chcete-li zvolit metodu ověřování, je potřeba zvážit čas, stávající infrastruktury, složitost a náklady na implementaci podle vašeho výběru. Tyto faktory se liší pro každý organizace a může v průběhu času měnit. 

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

Azure AD podporuje následující metody ověřování pro hybridní řešení identit.

### <a name="cloud-authentication"></a>Ověření cloudu
Pokud zvolíte tuto metodu ověřování, Azure AD zpracuje proces přihlášení uživatelů. Pomocí bezproblémového jednotného přihlašování (SSO) s velkou provázaností, uživatelé můžou přihlásit ke cloudovým aplikacím, aniž byste museli znovu zadat své přihlašovací údaje. Pomocí cloudového ověřování můžete vybrat ze dvou možností: 

**Synchronizace hodnot hash hesel Azure AD**. Nejjednodušší způsob, jak povolit ověřování místních adresářových objektů ve službě Azure AD. Uživatelé můžou používat stejné uživatelské jméno a heslo, které používají místní bez nutnosti nasazovat žádnou další infrastrukturu. Některé funkce premium služby Azure AD, jako je Identity Protection, vyžadují synchronizaci hodnot hash hesel, bez ohledu na to, jakou metodu ověřování zvolit.

> [!NOTE] 
> Hesla se nikdy ve formátu prostého textu nebo zašifrovaný pomocí algoritmu reverzibilního ve službě Azure AD. Další informace o skutečný proces synchronizace hodnot hash hesel najdete v tématu [implementace synchronizace hodnot hash hesel pomocí synchronizace Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization). 

**Azure AD předávací ověřování**. Poskytuje jednoduché heslo ověření pro ověřování služby Azure AD s použitím agenta software, který běží na jeden nebo více místních serverů. Servery ověření uživatelů přímo s vaší místní služby Active Directory, což zajistí, že ověření hesla se nestane v cloudu. 

Firmy využívají k zabezpečení potřeba vynucovat okamžitě, místní uživatel účtu státy, zásady pro hesla, a přihlašovací hodiny mohou použít tuto metodu ověřování. Další informace o procesu skutečné předávací ověřování najdete v tématu [přihlášení uživatele pomocí předávacího ověřování Azure AD](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta).

### <a name="federated-authentication"></a>Federovaného ověřování
Pokud zvolíte tuto metodu ověřování, Azure AD rukou vypnuto proces ověření pro důvěryhodného oddělený ověřovací systém, jako je například v místním Active Directory Federation Services (AD FS), ověřit heslo uživatele.

Ověřování systému může poskytovat další pokročilé ověřování požadavků. Příklady ověřování pomocí čipové karty nebo jiného vícefaktorové ověřování. Další informace najdete v tématu [nasazení služby AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

Následující část vám pomůže rozhodnout která metoda ověřování je pro vás nejvhodnější, s použitím rozhodovací strom. To vám pomůže určit, jestli se má nasazení cloudu nebo federovaného ověřování pro vaše řešení hybridní identity Azure AD.

## <a name="decision-tree"></a>Rozhodovací strom

![Azure AD authentication rozhodovací strom](media/azure-ad/azure-ad-authn-image1.png)

Podrobnosti o rozhodnutí otázky:

1. Azure AD dokáže zpracovat přihlášení pro uživatele bez nutnosti spoléhat se na místní komponenty ověření hesla.
2. Azure AD můžete předat přihlašování uživatelů k ověřování pro důvěryhodného zprostředkovatele například společnosti Microsoft AD FS.
3. Pokud se mají použít zásady zabezpečení na úrovni uživatele služby Active Directory, jako je vypršení platnosti účtu, deaktivovaný účet, platnost hesla vypršela, účet uzamčen a hodin přihlašování u každého uživatele přihlásit, Azure AD vyžaduje místní součásti.
4. Přihlášení: funkce, nikoli nativně podporovány službou Azure AD:
   * Přihlášení pomocí čipové karty ani certifikáty.
   * Přihlášení pomocí místním MFA serverem.
   * Přihlášení pomocí řešení ověřování 3. stran.
   * Multi-Site místní řešení pro ověřování.
5. Azure AD Identity Protection vyžaduje synchronizace hodnot Hash hesel bez ohledu na to, jaké metody přihlašování rozhodnete, zadejte *uživatelé s uniklými přihlašovacími údaji* sestavy. Pokud selže primární metodu přihlašování a byl nakonfigurovaný před událostí selhání, můžou organizace převzetí služeb při selhání synchronizace hodnot Hash hesel.

>[!NOTE]
> Azure AD Identity Protection vyžadují [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) licence.

## <a name="detailed-considerations"></a>Podrobné pokyny

### <a name="cloud-authentication-password-hash-synchronization"></a>Cloudové ověřování: Synchronizace hodnot hash hesel

* **Úsilí**. Synchronizace hodnot hash hesel je nejméně náročný týkající se nasazení, údržby a infrastruktury.  Tato úroveň úsilí obvykle platí pro organizace, které potřebují pouze jejich uživatelům umožní přihlásit k Office 365, aplikacím SaaS a dalších založené na AD prostředků Azure. Pokud je zapnutá, synchronizaci hodnot hash hesel je součástí procesu synchronizace Azure AD Connect a spouští každé dvě minuty.

* **Činnost koncového uživatele**. Ke zlepšení prostředí přihlášení uživatelů, nasaďte bezproblémového jednotného přihlašování se synchronizace hodnot hash hesel. Bezproblémové jednotné přihlašování eliminují zbytečné výzvy při přihlášení uživatele.

* **Pokročilé scénáře**. Pokud se rozhodnete organizace, je možné přehledy na základě identit pomocí Azure AD Identity Protection sestavy s Azure AD Premium P2. Příkladem je sestava uniklými přihlašovacími údaji. Windows Hello pro firmy je další možnost, která má [specifické požadavky při použití synchronizace hodnot hash hesel](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification). 

    Organizace, které vyžadují vícefaktorové ověřování se synchronizace hodnot hash hesel, musíte použít vícefaktorové ověřování Azure AD. Tyto organizace nelze použít metody vícefaktorového ověřování třetí strany nebo místně.

* **Kontinuita podnikových procesů**. Synchronizace hodnot hash hesel pomocí cloudového ověřování je Cloudová služba, která se škáluje do všech datových center Microsoftu s vysokou dostupností. Ujistěte se, že synchronizace hodnot hash hesel nedostane mimo provoz delší dobu, nasaďte na druhý server Azure AD Connect v pracovním režimu v pohotovostním režimu konfigurace.

* **Důležité informace o**. Synchronizace hodnot hash hesel v současné době není okamžitě vynucovat změny v místní účet stavy. V takovém případě má uživatel přístup ke cloudovým aplikacím, dokud se stav účtu uživatele se synchronizují do služby Azure AD. Organizace chtít, aby k překonání tohoto omezení spuštěním nový synchronizační cyklus po správci hromadné aktualizace stavů účtu místní uživatel. Příkladem je zakázání účtů.

> [!NOTE]
> Heslo vyprší a stavy uzamčený účet nejsou aktuálně synchronizované do Azure AD se službou Azure AD Connect. 

Odkazovat na [implementace synchronizace hodnot hash hesel](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) pro jednotlivé kroky nasazení.

### <a name="cloud-authentication-pass-through-authentication"></a>Cloudové ověřování: Předávací ověřování  

* **Úsilí**. Předávací ověřování, budete potřebovat jeden nebo více (doporučujeme tři) lehký agenti nainstalovaní na existujících serverech. Tito agenti musí mít přístup k vaší místní Active Directory Domain Services, včetně místních řadiče domény AD. Technici potřebují odchozí přístup k Internetu a přístup k vašim řadičům domény. Z tohoto důvodu není možné nasadit agenty v hraniční síti. 

    Předávací ověřování vyžaduje přístup k síti bez omezení k řadičům domény. Veškerý přenos v síti je zašifrovaný a omezené na požadavky na ověření. Další informace o tomto procesu najdete v tématu [podrobné informace o zabezpečení](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-security-deep-dive) na předávací ověřování.

* **Činnost koncového uživatele**. Ke zlepšení prostředí přihlášení uživatelů, nasaďte bezproblémového jednotného přihlašování s předávací ověřování. Bezproblémové jednotné přihlašování eliminují zbytečné výzvy po přihlášení uživatele.

* **Pokročilé scénáře**. Předávací ověřování vynucuje zásady místního účtu při přihlášení. Například je odepřen přístup při uzamčení účtu místní uživatel stav je zakázán, nebo [vypršení platnosti hesla](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-faq#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) nebo spadá mimo dobu, kdy uživatel může přihlásit. 

    Organizace, které vyžadují vícefaktorové ověřování pomocí předávacího ověřování, musíte použít Azure Multi-Factor Authentication (MFA). Tyto organizace nemůže použít metodu vícefaktorové ověřování třetí strany nebo místně. Pokročilé funkce vyžadují, synchronizaci hodnot hash hesel je nasazený, jestli zvolíte předávací ověřování. Příkladem je sestava uniklými přihlašovacími údaji služby Identity Protection.

* **Kontinuita podnikových procesů**. Doporučujeme vám, že nasadíte dva agenti velmi předávací ověřování. Tyto funkce jsou kromě první agenta na server Azure AD Connect. Tato další nasazení zajišťuje vysokou dostupnost požadavků na ověření. Až budete mít tři agenty nasazení, jeden agent může stále selhat při jiného agenta je mimo provoz kvůli údržbě. 

    Existuje Další výhodou až po nasazení synchronizace hodnot hash hesel kromě předávací ověřování. Funguje jako záložní ověřování, když primární ověřování už není k dispozici.

* **Důležité informace o**. Synchronizace hodnot hash hesel můžete použít jako záložní ověřování pro předávací ověřování při agenty nelze ověřit pověření uživatele kvůli chybě významné místní. Převzetí služeb při selhání pro synchronizaci hodnot hash hesel nedojde automaticky a ručně přepínat metody přihlašování musí používat Azure AD Connect. 

    Další důležité informace na předávací ověřování, včetně alternativní ID podporovat, najdete v části [– nejčastější dotazy](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-faq).

Odkazovat na [implementace předávací ověřování](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) pro jednotlivé kroky nasazení.

### <a name="federated-authentication"></a>Federovaného ověřování

* **Úsilí**. Federované ověřování systému spoléhá na externím systémem důvěryhodný k ověřování uživatelů. Některé společnosti se chcete znovu použít své stávající investice do federovaných systému s jejich hybridní řešení identit Azure AD. Údržby a správy federovaných systému spadá mimo ovládací prvek služby Azure AD. Je to na organizaci pomocí federovaných systému zajistit bezpečné nasazení a dokáže zpracovat zatížení ověřování. 

* **Činnost koncového uživatele**. Uživatelské prostředí federovaného ověřování závisí na implementaci funkcí, topologie a konfiguraci farmy federačních. Některé organizace musí tuto flexibilitu při přizpůsobení a konfigurovat přístup do farmy federačních tak, aby odpovídaly své požadavky na zabezpečení. Například je možné nakonfigurovat interně připojených uživatelů a zařízení k přihlášení uživatelů automaticky, bez zobrazení výzvy k zadání přihlašovacích údajů. Tato konfigurace funguje, protože se už přihlásili k jejich zařízení. V případě potřeby některé rozšířené funkce zabezpečení ztížit proces přihlašování uživatelů.

* **Pokročilé scénáře**. Když zákazníci mají požadavku na ověřování, která nenabízí nativní podporu služby Azure AD, je obvykle potřeba řešení federovaného ověřování. Viz podrobné informace, které vám pomůžou [možnost přímo přihlášení](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/). Vezměte v úvahu následující běžné požadavky:

    * Ověřování, které vyžaduje čipové karty ani certifikáty.
    * Na místních serverech MFA nebo vícefaktorové zprostředkovatelé třetí strany.
    * Ověřování pomocí řešení ověřování třetích stran. Zobrazit [seznam kompatibilit pro federaci Azure AD](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-compatibility).
    * Přihlášení, která vyžaduje sAMAccountName, například DOMÉNA\uživatelské_jméno, místo hlavní název uživatele (UPN), například user@domain.com.

* **Kontinuita podnikových procesů**. Federované systémy obvykle vyžadují pole s vyrovnáváním zatížení serverů, známé jako farmy. Tato farma je nakonfigurovaný v interní síti a topologie hraniční sítě k zajištění vysoké dostupnosti pro žádosti o ověření.

    Synchronizace hodnot hash hesel spolu s federovaného ověřování jako záložní ověřování nasazení, když primární ověřování už není k dispozici. Příkladem je, když nejsou k dispozici na místních serverech. Některé velké organizace vyžadují federačních řešení, které podporuje více bodů příchozího přenosu dat sítě Internet nakonfigurovanou geo-DNS pro žádosti o ověření s nízkou latencí.

* **Důležité informace o**. Federované systémy obvykle vyžadují více významnou investici do místní infrastruktury. Většina organizací tuto možnost zvolte, pokud již má místní federační investice. A pokud se vyžaduje silnou obchodní používat poskytovatele jedinou identitou. Federace se nachází složitější k provozu a řešení potíží s ve srovnání s cloudovým řešením ověřování.

Pro nepoužívající domény, který nemůže být ověřen ve službě Azure AD budete potřebovat další konfiguraci k implementaci ID přihlášení uživatele. Tento požadavek se označuje jako alternativní přihlašovací ID podpory. Zobrazit [konfigurace alternativního přihlašovacího ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) omezení a požadavky. Pokud se rozhodnete používat poskytovatele služby Multi-Factor authentication třetích stran s federací, ujistěte se, že zprostředkovatel podporuje WS-Trust umožňující připojení zařízení k Azure AD.

Odkazovat na [nasazení federační servery](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers) pro jednotlivé kroky nasazení.

> [!NOTE] 
> Když nasadíte řešení hybridní identity Azure AD, musí implementovat jedno z podporované topologie služby Azure AD Connect. Další informace o podporované a nepodporované konfigurace na [topologie pro Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies).

## <a name="architecture-diagrams"></a>Diagramy architektury

Následující diagramy popisují základní architektura služby komponent potřebných pro každou metodu ověřování, která vám pomůže s vaší hybridní řešení identit Azure AD. Poskytuje přehled pro porovnání rozdílů mezi řešení.

* Jednoduchost řešení synchronizace hodnot hash hesel:

    ![Hybridní identita Azure AD se synchronizace hodnot hash hesel](media/azure-ad/azure-ad-authn-image2.png)

* Požadavky agenta předávací ověřování:

    ![Hybridní identita Azure AD pomocí předávacího ověřování](media/azure-ad/azure-ad-authn-image3.png)

* Komponenty potřebné pro federaci ve vaší hraniční a interní síti vaší organizace:

    ![Hybridní identita Azure AD s použitím federovaného ověřování](media/azure-ad/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Porovnání metod

|Posouzení|Synchronizace hodnot hash hesel a bezproblémového jednotného přihlašování|Předávací ověřování a bezproblémové jednotné přihlašování|Federace se službou AD FS|
|:-----|:-----|:-----|:-----|
|Pokud ověření proběhne?|V cloudu|V cloudu po serveru exchange zabezpečené heslo ověření pomocí místního ověřování agenta|Lokálně|
|Jaké jsou požadavky na místní server nad rámec systému zřizování: Azure AD Connect?|Žádný|Jeden server pro každý další ověřovací agent|Dva nebo víc serverů služby AD FS<br><br>Dva nebo víc serverů WAP v hraniční/DMZ sítě|
|Jaké jsou požadavky na místní Internet a sítí nad rámec zřizování systému?|Žádný|[Odchozí internetový přístup](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) ze serverů spuštěných agentů ověřování|[Příchozí přístup k Internetu](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-requirements) pro servery WAP v hraniční síti<br><br>Příchozí síťový přístup k serverům AD FS z servery WAP v hraniční síti<br><br>Vyrovnávání zatížení sítě|
|Existuje požadavek na certifikát SSL?|Ne|Ne|Ano|
|Je k dispozici řešení pro monitorování stavu?|Nepožaduje se|Stav agenta poskytované [centra pro správu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-pass-through-authentication)|[Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)|
|Uživatelé se jednotné přihlašování ke cloudovým prostředkům ze zařízení připojeného k doméně v podnikové síti?|Ano s [bezproblémového jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)|Ano s [bezproblémového jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)|Ano|
|Jaké typy přihlášení jsou podporovány?|UserPrincipalName + heslo<br><br>Integrované ověřování Windows s použitím [bezproblémového jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)<br><br>[Alternativním přihlašovacím ID](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)|UserPrincipalName + heslo<br><br>Integrované ověřování Windows s použitím [bezproblémového jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)<br><br>[Alternativním přihlašovacím ID](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-faq)|UserPrincipalName + heslo<br><br>sAMAccountName + heslo<br><br>Integrované ověřování systému Windows<br><br>[Ověřování certifikátu a čipové karty](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Alternativním přihlašovacím ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Je Windows Hello pro firmy, které jsou podporovány?|[Model klíče důvěryhodnosti](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Model důvěryhodnosti certifikátu s Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[Model klíče důvěryhodnosti](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Model důvěryhodnosti certifikátu s Intune](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)<br>*Vyžaduje úroveň funkčnosti domény Windows serveru 2016*|[Model klíče důvěryhodnosti](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Model důvěryhodnosti certifikátu](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Jaké jsou možnosti vícefaktorové ověřování?|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Vlastní ovládací prvky pomocí podmíněného přístupu *](https://docs.microsoft.com/azure/active-directory/conditional-access/controls#custom-controls-1)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Vlastní ovládací prvky pomocí podmíněného přístupu *](https://docs.microsoft.com/azure/active-directory/conditional-access/controls#custom-controls-1)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Azure MFA serveru](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)<br><br>[MFA třetích stran](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[Vlastní ovládací prvky pomocí podmíněného přístupu *](https://docs.microsoft.com/azure/active-directory/conditional-access/controls#custom-controls-1)|
|Jaké stavy účtu uživatele jsou podporovány?|Zakázané účty<br>(až do 30 minut, než)|Zakázané účty<br><br>Účet uzamčen<br><br>Vypršení platnosti účtu<br><br>Platnost hesla vypršela.<br><br>Přihlašovací hodiny|Zakázané účty<br><br>Účet uzamčen<br><br>Vypršení platnosti účtu<br><br>Platnost hesla vypršela.<br><br>Přihlašovací hodiny|
|Jaké jsou možnosti podmíněného přístupu?|[Podmíněný přístup Azure AD, s Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)|[Podmíněný přístup Azure AD, s Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)|[Podmíněný přístup Azure AD, s Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)<br><br>[Pravidla deklarací identity služby AD FS](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Je blokování starších verzí protokolů, které jsou podporovány?|[Ano](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[Ano](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)|[Ano](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Můžete přizpůsobit logo, obrázku a popisu na přihlašovací stránky?|[Ano, s Azure AD Premium](https://docs.microsoft.com/azure/active-directory/customize-branding)|[Ano, s Azure AD Premium](https://docs.microsoft.com/azure/active-directory/customize-branding)|[Ano](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|Jaké pokročilé scénáře jsou podporovány?|[Inteligentní uzamčení hesla](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)<br><br>[Úniku přihlašovacích údajů sestavy s Azure AD Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)|[Inteligentní uzamčení hesla](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|Nasazení ve více lokalitách s nízkou latencí ověřovacím systémem<br><br>[Uzamčení extranetu služby AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[Integrace se systémy identit třetích stran](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

> [!NOTE] 
> Vlastní ovládací prvky v podmíněný přístup Azure AD v současné době nepodporuje registraci zařízení.

## <a name="recommendations"></a>Doporučení
Vašeho systému identit zajišťuje vaši uživatelé přístup ke cloudovým aplikacím a -obchodní aplikace, migraci a zpřístupnit v cloudu. Pokud chcete zachovat produktivitu autorizovaných uživatelů a nesprávnými účastníky z vaší organizace citlivých dat, ověřování řídí přístup k aplikacím.

Použití nebo povolit synchronizaci hodnot hash hesel podle toho, která metoda ověřování vyberte z následujících důvodů:

1. **Vysoká dostupnost a zotavení po havárii**. Předávací ověřování a federace závisí na místní infrastrukturu. Předávací ověřování nároky na místní zahrnuje hardware serveru a sítě předávací ověřování vyžadovat agenty. Pro federaci je ještě větší nároky na místní. Vyžaduje servery v hraniční síti do žádosti o ověření proxy serveru a interní federačními servery. 

    Vyhýbejte se jediným bodů selhání, nasaďte redundantní servery. Potom žádosti o ověření budou vždy zpracovány pokud jakákoliv komponenta nepodaří. Předávací ověřování a federace také závisí na řadičích domény reagovat na žádosti o ověření, které může také selhat. Mnohé z těchto komponent musí údržby zůstat v pořádku. Výpadky jsou pravděpodobně při údržby se plánují a implementují správně. Vyhněte se výpadkům pomocí synchronizace hodnot hash hesel, protože globálně škáluje podle ověřování cloudové služby Microsoft Azure AD a jsou vždycky dostupné.

2. **Místní výpadek přežití**.  Důsledky výpadku v místním kvůli internetového útoku nebo po havárii může být značné, od reputational značky poškození ochrnuté pacienty organizaci schopen čelit útoku. Mnoho organizací byly nedávno obětí malwarových útoků, včetně cílové ransomwaru, způsobující jejich místní servery, přejděte. Microsoft pomůže zákazníkům řešit tyto typy útoků, zobrazí dva druhy organizací:

   * Organizace, které dříve zapnutá synchronizaci hodnot hash hesel změnit metodu ověřování používat synchronizaci hodnot hash hesel. Kdyby byly zpět do online režimu v řádu hodin. Pomocí přístupu k e-mailu prostřednictvím Office 365 to šlo vyřešit problémy a přístup k jiné úlohy založené na cloudu.

   * Organizace, které dříve nebyly povolit synchronizaci hodnot hash hesel se museli uchýlíte k nedůvěryhodné externího příjemce e-mailových systémů komunikace a řešení problémů. V takových případech, jakou trvalo je týdny nebo další funkce začít znovu.

3. **Ochrana identity**. Jednou z nejlepších způsobů, jak chránit uživatele v cloudu je Azure AD Identity Protection s Azure AD Premium P2. Microsoft neustále kontroluje Internet pro uživatele a heslo uvádí, že prodej a zpřístupnit v dark web. Tyto informace můžete použít Azure AD k ověření, pokud některý z uživatelských jmen a hesel ve vaší organizaci dojde k ohrožení. Proto je důležité pro povolení synchronizace hodnot hash hesel bez ohledu na to, jakou metodu ověřování, které používáte, zda, která je Federovaná nebo předávací ověřování. Uniklé přihlašovací údaje se zobrazují jako sestavy. Tyto informace slouží k blokování nebo přimějte uživatele ke změně hesla při pokusu o přihlášení pomocí uniklé hesla.

A konečně, souladu s [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html), Microsoft má nejvíce plně funkční sadu funkcí správy identit a přístupu. Společnost Microsoft zpracovává [450 miliard žádostí o ověření](https://www.microsoft.com/en-us/security/intelligence-report) každý měsíc pro zajištění přístupu k nepřeberným množstvím aplikací SaaS, jako je Office 365 z libovolného zařízení. 

## <a name="conclusion"></a>Závěr

Tento článek popisuje různé možnosti ověřování, které organizace můžete nakonfigurovat a nasadit pro podporu přístupu ke cloudovým aplikacím. Plnit různé obchodní, zabezpečení a technické požadavky organizace si vybrat synchronizaci hodnot hash hesel, předávacího ověřování a federace. 

Vezměte v úvahu každá metoda ověřování. Je úsilí nezbytné k nasazení řešení a spuštění procesu přihlašování, adres pro váš podnik jiné požadavky? Vyhodnoťte, jestli vaše organizace potřebuje pokročilé scénáře a funkce obchodní kontinuity podnikových procesů jednotlivých metod ověřování. Nakonec vyhodnoťte, jaké jsou požadavky každé metody ověřování. Některý z nich by vám bránily v implementaci podle vašeho výběru?

## <a name="next-steps"></a>Další postup

V dnešním světě hrozeb k dispozici 24 hodin denně a pocházejí z jakéhokoliv místa. Implementovat metodu správné ověření, a bude zmírnění rizik zabezpečení a ochraně svých identit.

[Začínáme](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad) s Azure AD a nasaďte řešení správné ověřování pro vaši organizaci.

Pokud uvažujete o migraci z federovanou se cloudovým ověřování, další informace o [Změna metody přihlašování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method). Chcete-li pomoci při plánování a implementaci migrace, použijte [těchto nasazení plánů projektů](https://aka.ms/deploymentplans).
