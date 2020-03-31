---
title: Ověřování pro řešení hybridních identit Azure AD
titleSuffix: Active Directory
description: Tato příručka pomáhá generálním ředitelům, ředitelům itos, CICO, hlavním architektům identit, podnikovým architektům a tvůrcům s rozhodovací pravomocí v oblasti zabezpečení a IT, kteří jsou zodpovědní za výběr metody ověřování pro jejich řešení hybridní identity Azure AD ve středních až velkých organizacích.
keywords: ''
author: martincoetzer
ms.author: martinco
ms.date: 10/30/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: e263ecde532a8aaed420932bf355910da201723e
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365841"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Výběr správné metody ověřování pro řešení hybridníidentity služby Azure Active Directory

Výběr správné metody ověřování je prvním problémem pro organizace, které chtějí přesunout své aplikace do cloudu. Neber toto rozhodnutí na lehkou váhu, a to z následujících důvodů:

1. Je to první rozhodnutí pro organizaci, která se chce přesunout do cloudu.

2. Metoda ověřování je důležitou součástí přítomnosti organizace v cloudu. Řídí přístup ke všem cloudovým datům a prostředkům.

3. Je základem všech dalších pokročilých funkcí zabezpečení a uživatelského prostředí ve službě Azure AD.

Identita je nová řídicí rovina zabezpečení IT, takže ověřování je ochrana přístupu organizace do nového cloudového světa. Organizace potřebují rovinu řízení identity, která posiluje jejich zabezpečení a udržuje jejich cloudové aplikace v bezpečí před narušiteli.

> [!NOTE]
> Změna metody ověřování vyžaduje plánování, testování a potenciálně prostoje. [Postupné zavádění](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout) je skvělý způsob testování a postupné migraci z federace do cloudového ověřování.

### <a name="out-of-scope"></a>Mimo rozsah
Organizace, které nemají existující místní adresářové stopy nejsou v centru tohoto článku. Tyto firmy obvykle vytvářejí identity pouze v cloudu, což nevyžaduje řešení hybridní identity. Identity pouze pro cloud existují výhradně v cloudu a nejsou přidruženy k odpovídajícím místním identitám.

## <a name="authentication-methods"></a>Metody ověřování
Když je řešení hybridní identity Azure AD vaší novou rovinou ovládacího prvku, ověřování je základem přístupu ke cloudu. Výběr správné metody ověřování je zásadní první rozhodnutí při nastavování řešení hybridní identity Azure AD. Implementujte metodu ověřování, která je nakonfigurovaná pomocí Služby Azure AD Connect, která také zřizuje uživatele v cloudu.

Chcete-li zvolit metodu ověřování, je třeba zvážit čas, existující infrastrukturu, složitost a náklady na implementaci vaší volby. Tyto faktory se liší pro každou organizaci a mohou se v průběhu času měnit.

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

Azure AD podporuje následující metody ověřování pro řešení hybridní identity.

### <a name="cloud-authentication"></a>Cloudové ověřování
Když zvolíte tuto metodu ověřování, Azure AD zpracovává proces přihlášení uživatelů. Ve spojení s bezproblémovým jednotného přihlašování (SSO) se uživatelé můžou přihlásit ke cloudovým aplikacím, aniž by museli znovu zadávat svá přihlašovací údaje. Pomocí cloudového ověřování si můžete vybrat ze dvou možností:

**Synchronizace hash hesla Azure AD**. Nejjednodušší způsob, jak povolit ověřování pro místní objekty adresáře ve službě Azure AD. Uživatelé mohou používat stejné uživatelské jméno a heslo, které používají místně, aniž by museli nasazovat další infrastrukturu. Některé prémiové funkce Azure AD, jako je Ochrana identity a [Služby domény Azure AD](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md), vyžadují synchronizaci hodnot hash hesel bez ohledu na to, kterou metodu ověřování zvolíte.

> [!NOTE]
> Hesla se nikdy neukládají ve prostém textu nebo šifrují pomocí reverzibilní algoritmuve službě Azure AD. Další informace o skutečném procesu synchronizace hodnot hash hesel naleznete v [tématu Implementace synchronizace hash hesel se synchronizací Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

**Předávací ověřování Azure AD**. Poskytuje jednoduché ověření hesla pro ověřovací služby Azure AD pomocí softwarového agenta, který běží na jednom nebo více místních serverech. Servery ověřují uživatele přímo pomocí místní služby Active Directory, což zajišťuje, že k ověření hesla nedojde v cloudu.

Společnosti s požadavkem na zabezpečení k okamžitému vynucení místních stavů uživatelských účtů, zásad hesel a přihlašovacích hodin mohou používat tuto metodu ověřování. Další informace o skutečném procesu předávacího ověřování najdete [v tématu Přihlášení uživatele pomocí předávacího ověřování Azure AD](../../active-directory/hybrid/how-to-connect-pta.md).

### <a name="federated-authentication"></a>Federované ověřování
Když zvolíte tuto metodu ověřování, Azure AD předává proces ověřování samostatnému důvěryhodnému ověřovacímu systému, jako je například místní služba AD FS (AD FS), aby ověřila heslo uživatele.

Ověřovací systém může poskytnout další rozšířené požadavky na ověřování. Příkladem je ověřování pomocí čipových karet nebo vícefaktorové ověřování třetích stran. Další informace naleznete [v tématu Nasazení služby AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

Následující část vám pomůže rozhodnout, která metoda ověřování je pro vás vhodná pomocí rozhodovacího stromu. Pomáhá vám určit, jestli se má nasazovat cloudové nebo federované ověřování pro vaše řešení hybridní identity Azure AD.

## <a name="decision-tree"></a>Rozhodovací strom

![Rozhodovací strom ověřování Azure AD](./media/choose-ad-authn/azure-ad-authn-image1.png)

Podrobnosti k otázkám rozhodování:

1. Azure AD může zpracovávat přihlášení pro uživatele bez spoléhání se na místní součásti k ověření hesel.
2. Azure AD můžete předat přihlášení uživatele k důvěryhodnému poskytovateli ověřování, jako je microsoft AD FS.
3. Pokud potřebujete použít zásady zabezpečení služby Active Directory na úrovni uživatele, jako je vypršela platnost účtu, zakázaný účet, vypršela platnost hesla, účet uzamčena a přihlašovací hodiny na každém přihlášení uživatele, Azure AD vyžaduje některé místní součásti.
4. Funkce přihlášení, které nativně nepodporuje Azure AD:
   * Přihlaste se pomocí čipových karet nebo certifikátů.
   * Přihlaste se pomocí místního serveru MFA.
   * Přihlaste se pomocí ověřovacího řešení třetích stran.
   * Řešení místního ověřování na více pracovišti.
5. Azure AD Identity Protection vyžaduje synchronizaci hash hesel bez ohledu na to, kterou metodu přihlášení zvolíte, aby *uživatelům poskytli neuniklou* sestavu přihlašovacích údajů. Organizace mohou přejimítat převzetím služeb při selhání do synchronizace hash hesla, pokud jejich primární metoda přihlášení selže a byla nakonfigurována před událostí selhání.

> [!NOTE]
> Azure AD Identity Protection vyžaduje licence [Azure AD Premium P2.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="detailed-considerations"></a>Podrobné úvahy

### <a name="cloud-authentication-password-hash-synchronization"></a>Cloudové ověřování: Synchronizace hodnot hash hesel

* **Úsilí**. Synchronizace hash hesel vyžaduje nejmenší úsilí týkající se nasazení, údržby a infrastruktury.  Tato úroveň úsilí se obvykle vztahuje na organizace, které potřebují jenom své uživatele k přihlášení k Office 365, saas aplikací maašla a další prostředky založené na Azure AD. Při zapnutí je synchronizace hodnot hash hesel součástí procesu synchronizace azure aslužby a běží každé dvě minuty.

* **Uživatelské prostředí**. Chcete-li zlepšit možnosti přihlášení uživatelů, nasaďte bezproblémové jednotné přihlašování pomocí synchronizace hodnot hash hesel. Bezproblémové jednotné přihlašovat eliminuje zbytečné výzvy při přihlášení uživatelů.

* **Pokročilé scénáře**. Pokud se tak organizace rozhodnou, je možné použít přehledy z identit se sestavami Azure AD Identity Protection se sestavami Azure AD Premium P2. Příkladem je zpráva o leaked credentials. Windows Hello pro firmy má [specifické požadavky při použití synchronizace hodnot hash hesla](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification). [Azure AD Domain Services](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) vyžaduje synchronizaci hodnot hash hesla pro zřízení uživatelů s jejich podnikovými přihlašovacími údaji ve spravované doméně.

    Organizace, které vyžadují vícefaktorové ověřování se synchronizací hodnot hash hesel, musí používat vlastní ovládací prvky azure ad vícefaktorového ověřování nebo [podmíněného přístupu](../../active-directory/conditional-access/controls.md#custom-controls-preview). Tyto organizace nemohou používat metody vícefaktorového ověřování třetích stran nebo místních výrobců, které jsou závislé na federaci.

> [!NOTE]
> Podmíněný přístup Azure AD vyžaduje licence [Azure AD Premium P1.](https://azure.microsoft.com/pricing/details/active-directory/)

* **Kontinuita provozu**. Použití synchronizace hodnot hash hesla s cloudovým ověřováním je vysoce dostupné jako cloudová služba, která se škáluje do všech datových center Microsoftu. Chcete-li zajistit, aby synchronizace hash hesla nešla dolů po delší dobu, nasaďte druhý server Azure AD Connect v pracovním režimu v pohotovostní konfiguraci.

* **Důležité informace**. V současné době synchronizace hodnot hash hesla okamžitě nevynucuje změny ve stavech místního účtu. V takovém případě má uživatel přístup ke cloudovým aplikacím, dokud se stav uživatelského účtu nesynchronizuje se službou Azure AD. Organizace mohou chtít překonat toto omezení spuštěním nového cyklu synchronizace poté, co správci provést hromadné aktualizace místních stavů uživatelských účtů. Příkladem je zakázání účtů.

> [!NOTE]
> Platnost hesla vypršela a stavy uzamčení účtu nejsou aktuálně synchronizovány s Azure AD s Azure AD Connect. Když změníte heslo uživatele a *nastavíte, že uživatel musí změnit heslo při příštím příznaku přihlášení,* nebude se hash hesla synchronizovat do služby Azure AD s Azure AD Connect, dokud uživatel nezmění své heslo.

Postup nasazení naleznete v [části Implementace synchronizace hash hesel.](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)

### <a name="cloud-authentication-pass-through-authentication"></a>Cloudové ověřování: Předávací ověřování  

* **Úsilí**. Pro předávací ověřování potřebujete jeden nebo více (doporučujeme tři) zjednodušené agenty nainstalované na stávajících serverech. Tito agenti musí mít přístup k místním službám Active Directory Domain Services, včetně místních řadičů domény služby AD. Potřebují odchozí přístup k Internetu a přístup k řadičům domény. Z tohoto důvodu není podporována nasazení agentů v hraniční síti.

    Předávací ověřování vyžaduje neomezený síťový přístup k řadičům domény. Veškerý síťový provoz je šifrován a omezen na požadavky na ověření. Další informace o tomto procesu naleznete v [podrobném přehledu zabezpečení](../../active-directory/hybrid/how-to-connect-pta-security-deep-dive.md) při předávacím ověřování.

* **Uživatelské prostředí**. Chcete-li zlepšit možnosti přihlášení uživatelů, nasaďte bezproblémové jednotné přihlašování s předávacím ověřováním. Bezproblémové jednotné přihlašování eliminuje zbytečné výzvy po přihlášení uživatelů.

* **Pokročilé scénáře**. Předávací ověřování vynucuje zásady místního účtu v době přihlášení. Přístup je například odepřen, když je stav účtu místního uživatele zakázán, uzamčen nebo [vyprší platnost hesla](../../active-directory/hybrid/how-to-connect-pta-faq.md#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) nebo pokus o přihlášení spadá mimo hodiny, kdy se uživatel může přihlásit.

    Organizace, které vyžadují vícefaktorové ověřování s předávacím ověřováním, musí používat vlastní ovládací prvky Azure Multi-Factor Authentication (MFA) nebo [Podmíněného přístupu](../../active-directory/conditional-access/controls.md#custom-controls-preview). Tyto organizace nelze použít třetí strany nebo místní vícefaktorové ověřování metoda, která závisí na federaci. Pokročilé funkce vyžadují, aby byla nasazena synchronizace hodnot hash hesel bez ohledu na to, zda zvolíte předávací ověřování. Příkladem je děravá zpráva pověření identity ochrany.

* **Kontinuita provozu**. Doporučujeme nasadit dva další předávací ověřování agenty. Tyto doplňky jsou navíc k prvnímu agentovi na serveru Azure AD Connect. Toto další nasazení zajišťuje vysokou dostupnost požadavků na ověření. Když máte nasazeny tři agenty, jeden agent může stále selhat, když jiný agent je z důvodu údržby.

    Kromě předávacího ověřování je k dispozici další výhoda synchronizace hash hesel. Funguje jako metoda ověřování zálohování, pokud primární metoda ověřování již není k dispozici.

* **Důležité informace**. Synchronizace hodnot hash hesla můžete použít jako metodu ověřování zálohování pro předávací ověřování, když agenti nemohou ověřit pověření uživatele z důvodu významné místní selhání. Převzetí služeb při selhání na synchronizaci hash hesla se neuskuteční automaticky a je nutné použít Azure AD Connect přepnout metodu přihlášení ručně.

    Další informace o předávacím ověřování, včetně podpory alternativního ID, naleznete [v nejčastějších dotazech](../../active-directory/hybrid/how-to-connect-pta-faq.md).

Postup nasazení naleznete v [implementaci předávacího ověřování.](../../active-directory/hybrid/how-to-connect-pta.md)

### <a name="federated-authentication"></a>Federované ověřování

* **Úsilí**. Federovaný ověřovací systém spoléhá k ověřování uživatelů na externím důvěryhodném systému. Některé společnosti chtějí znovu použít své stávající federované systémové investice s jejich řešení hybridní identity Azure AD. Údržba a správa federovaného systému spadá mimo kontrolu Azure AD. Je na organizaci pomocí federovaného systému, aby se ujistil, že je nasazen bezpečně a zvládne zatížení ověřování.

* **Uživatelské prostředí**. Uživatelské prostředí federovaného ověřování závisí na implementaci funkcí, topologie a konfiguraci federační farmy. Některé organizace potřebují tuto flexibilitu k přizpůsobení a konfiguraci přístupu k federační farmě tak, aby vyhovoval jejich požadavkům na zabezpečení. Například je možné nakonfigurovat interně připojené uživatele a zařízení tak, aby automaticky přihlašovali uživatele, aniž by se jim vyzýla pověření. Tato konfigurace funguje, protože se již přihlásili ke svým zařízením. V případě potřeby některé pokročilé funkce zabezpečení ztěžují proces přihlášení uživatelů.

* **Pokročilé scénáře**. Federované řešení ověřování se obvykle vyžaduje, když zákazníci mají požadavek na ověřování, který Azure AD nepodporuje nativně. Podívejte se na podrobné informace, které vám pomohou [vybrat správnou možnost přihlášení](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/). Zvažte následující běžné požadavky:

  * Ověřování, které vyžaduje čipové karty nebo certifikáty.
  * Místní servery MFA nebo zprostředkovatelé vícefaktorových služeb třetích stran, kteří vyžadují poskytovatele federované identity.
  * Ověřování pomocí řešení ověřování třetích stran. Podívejte se na [seznam kompatibility federací Azure AD](../../active-directory/hybrid/how-to-connect-fed-compatibility.md).
  * Přihlaste se, který vyžaduje název sAMAccountName, například DOMAIN\username, namísto user@domain.comhlavního uživatelského jména (UPN), například .

* **Kontinuita provozu**. Federované systémy obvykle vyžadují pole serverů s vyrovnáváním zatížení, označované jako farma. Tato farma je konfigurována v topologii interní sítě a hraniční sítě, aby byla zajištěna vysoká dostupnost pro požadavky na ověření.

    Nasazení synchronizace hash hesel spolu s federovaným ověřováním jako metodou ověřování zálohování, pokud již není k dispozici primární metoda ověřování. Příkladem je, když místní servery nejsou k dispozici. Některé velké podnikové organizace vyžadují federační řešení pro podporu více internetových příchozích bodů pro přenos dat nakonfigurovaných pomocí geografického DNS pro požadavky na ověření s nízkou latencí.

* **Důležité informace**. Federované systémy obvykle vyžadují významnější investice do místní infrastruktury. Většina organizací tuto možnost zvolí, pokud již mají místní federační investici. A pokud je to silný obchodní požadavek používat poskytovatele jedné identity. Federace je složitější pro provoz a řešení potíží ve srovnání s řešeními cloudového ověřování.

Pro nesměrovatelnou doménu, kterou nelze ověřit ve službě Azure AD, potřebujete další konfiguraci k implementaci přihlášení id uživatele. Tento požadavek se označuje jako podpora alternativního přihlášení id. Omezení a požadavky [najdete v tématu Konfigurace Alternativního ID přihlášení.](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) Pokud se rozhodnete použít poskytovatele vícefaktorového ověřování třetí strany s federací, ujistěte se, že poskytovatel podporuje WS-Trust, aby zařízení mohla připojit Azure AD.

Postup nasazení naleznete v [potížích nasazení v odkazech Nasazení federačních serverů.](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers)

> [!NOTE]
> Když nasadíte řešení hybridní identity Azure AD, musíte implementovat jednu z podporovaných topologie Azure AD Connect. Další informace o podporovaných a nepodporovaných konfiguracích najdete v [topologii pro Azure AD Connect](../../active-directory/hybrid/plan-connect-topologies.md).

## <a name="architecture-diagrams"></a>Diagramy architektury

Následující diagramy popisují součásti architektury vysoké úrovně požadované pro každou metodu ověřování, kterou můžete použít s řešením hybridní identity Azure AD. Poskytují přehled, který vám pomůže porovnat rozdíly mezi řešeními.

* Jednoduchost řešení synchronizace hodnot hash hesel:

    ![Hybridní identita Azure AD se synchronizací hash hesel](./media/choose-ad-authn/azure-ad-authn-image2.png)

* Požadavky agenta předávacího ověřování pomocí dvou agentů pro redundanci:

    ![Hybridní identita Azure AD s předávacím ověřováním](./media/choose-ad-authn/azure-ad-authn-image3.png)

* Součásti potřebné pro federaci v obvodové a interní síti vaší organizace:

    ![Hybridní identita Azure AD s federovaným ověřováním](./media/choose-ad-authn/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Porovnání metod

|Aspekty|Synchronizace hash hesla + bezproblémové jednotné přihlašování|Předávací ověřování + bezproblémové jednotné přihlašení|Federace se službou AD FS|
|:-----|:-----|:-----|:-----|
|Kde provádíte ověření?|V cloudu|V cloudu po zabezpečené výměně ověření hesla s místním agentem ověřování|Lokálně|
|Jaké jsou požadavky na místní server nad rámec zřizovacího systému: Azure AD Connect?|Žádný|Jeden server pro každého dalšího agenta ověřování|Dva nebo více serverů služby AD FS<br><br>Dva nebo více WAP serverů v obvodové/DMZ síti|
|Jaké jsou požadavky na místní Internet a sítě nad rámec zřizovacího systému?|Žádný|[Odchozí přístup k Internetu](../../active-directory/hybrid/how-to-connect-pta-quick-start.md) ze serverů s ověřovacími agenty|[Příchozí přístup k internetu](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-requirements) k serverům WAP v obvodu<br><br>Příchozí síťový přístup k serverům AD FS ze serverů WAP v obvodu<br><br>Vyrovnávání zatížení sítě|
|Existuje požadavek na certifikát TLS/SSL?|Ne|Ne|Ano|
|Existuje řešení pro monitorování stavu?|Není požadováno|Stav agenta poskytovaný [Centrem pro správu služby Azure Active Directory](../../active-directory/hybrid/tshoot-connect-pass-through-authentication.md)|[Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md)|
|Získají uživatelé jednotné přihlašování ke cloudovým prostředkům ze zařízení spojených s doménou v rámci podnikové sítě?|Ano s [bezproblémovým jednotném přihlašovatcem](../../active-directory/hybrid/how-to-connect-sso.md)|Ano s [bezproblémovým jednotném přihlašovatcem](../../active-directory/hybrid/how-to-connect-sso.md)|Ano|
|Jaké typy přihlášení jsou podporovány?|UserPrincipalName + heslo<br><br>Integrované ověřování systému Windows pomocí [bezproblémového jednotného přihlašujícího systému](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[Alternativní přihlašovací ID](../../active-directory/hybrid/how-to-connect-install-custom.md)|UserPrincipalName + heslo<br><br>Integrované ověřování systému Windows pomocí [bezproblémového jednotného přihlašujícího systému](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[Alternativní přihlašovací ID](../../active-directory/hybrid/how-to-connect-pta-faq.md)|UserPrincipalName + heslo<br><br>sAMAccountName + heslo<br><br>Integrované ověřování systému Windows<br><br>[Ověřování certifikátů a čipových karet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Alternativní přihlašovací ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Je Windows Hello pro firmy podporován?|[Klíčový model důvěryhodnosti](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)|[Klíčový model důvěryhodnosti](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>*Vyžaduje úroveň funkčnosti domény Windows Server 2016*|[Klíčový model důvěryhodnosti](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Model důvěryhodnosti certifikátu](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Jaké jsou vícefaktorové možnosti ověřování?|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Vlastní ovládací prvky s podmíněným přístupem*](../../active-directory/conditional-access/controls.md)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Vlastní ovládací prvky s podmíněným přístupem*](../../active-directory/conditional-access/controls.md)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Server Azure MFA](../../active-directory/authentication/howto-mfaserver-deploy.md)<br><br>[Vícefaktorové financování třetí strany](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[Vlastní ovládací prvky s podmíněným přístupem*](../../active-directory/conditional-access/controls.md)|
|Jaké stavy uživatelských účtů jsou podporovány?|Zakázané účty<br>(až 30minutové zpoždění)|Zakázané účty<br><br>Účet uzamčen<br><br>Platnost účtu vypršela.<br><br>Platnost hesla vypršela.<br><br>Hodiny přihlášení|Zakázané účty<br><br>Účet uzamčen<br><br>Platnost účtu vypršela.<br><br>Platnost hesla vypršela.<br><br>Hodiny přihlášení|
|Jaké jsou možnosti podmíněného přístupu?|[Podmíněný přístup Azure AD s Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Podmíněný přístup Azure AD s Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Podmíněný přístup Azure AD s Azure AD Premium](../../active-directory/conditional-access/overview.md)<br><br>[Pravidla deklarace ad FS](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Je blokování starších protokolů podporováno?|[Ano](../../active-directory/conditional-access/overview.md)|[Ano](../../active-directory/conditional-access/overview.md)|[Ano](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Můžete přizpůsobit logo, obrázek a popis na přihlašovacích stránkách?|[Ano, s Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Ano, s Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Ano](../../active-directory/hybrid/how-to-connect-fed-management.md)|
|Jaké pokročilé scénáře jsou podporovány?|[Inteligentní uzamčení hesla](../../active-directory/authentication/howto-password-smart-lockout.md)<br><br>[Děravé sestavy přihlašovacích údajů s Azure AD Premium P2](../../active-directory/reports-monitoring/concept-risk-events.md)|[Inteligentní uzamčení hesla](../../active-directory/authentication/howto-password-smart-lockout.md)|Ověřovací systém s nízkou latencí ve více lokalitě<br><br>[Uzamčení extranetu ve sfách AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[Integrace se systémy identit třetích stran](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)|

> [!NOTE]
> Vlastní ovládací prvky v podmíněném přístupu Azure AD aktuálně nepodporují registraci zařízení.

## <a name="recommendations"></a>Doporučení
Váš systém identit zajišťuje uživatelům přístup ke cloudovým aplikacím a obchodním aplikacím, které migrujete a zpřístupníte v cloudu. Ověřování řídí přístup k aplikacím, aby oprávnění uživatelé nesměli a nezpřístupňují citlivá a špatná uživatelé z citlivých dat vaší organizace.

Použijte nebo povolte synchronizaci hodnot hash hesel pro kteroukoli metodu ověřování, kterou zvolíte, a to z následujících důvodů:

1. **Vysoká dostupnost a zotavení po havárii**. Předávací ověřování a federace spoléhají na místní infrastrukturu. Pro předávací ověřování zahrnuje místní nároky na serverový hardware a sítě, které vyžadují agenti předávacího ověřování. Pro federaci je místní stopa ještě větší. Vyžaduje servery v hraniční síti pro požadavky na ověřování proxy serveru a interní federační servery.

    Chcete-li se vyhnout selhání jednotlivých bodů, nasaďte redundantní servery. Požadavky na ověření pak budou vždy obsluhovány, pokud dojde k selhání některé součásti. Předávací ověřování i federace také spoléhají na řadiče domény, které reagují na požadavky na ověření, což může také selhat. Mnoho z těchto součástí potřebují údržbu, aby zůstali zdraví. Výpadky jsou pravděpodobnější, když údržba není správně naplánována a implementována. Vyhněte se výpadkům pomocí synchronizace hodnot hash hesla, protože cloudová ověřovací služba Microsoft Azure AD se globálně škáluje a je vždy dostupná.

2. **Přežití v místním prostředí**.  Důsledky výpadku v místním prostředí způsobeného kybernetickým útokem nebo katastrofou mohou být značné, od poškození značky pověsti až po paralyzovnou organizaci, která se s útokem nedokáže vypořádat. V poslední době se mnoho organizací staly oběťmi malwarových útoků, včetně cíleného ransomwaru, který způsobil, že jejich místní servery spadly. Když společnost Microsoft pomáhá zákazníkům vypořádat se s těmito druhy útoků, vidí dvě kategorie organizací:

   * Organizace, které dříve zapnuly synchronizaci hodnot hash hesel, změnily metodu ověřování tak, aby používaly synchronizaci hodnot hash hesel. Byli zase online během několika hodin. Díky přístupu k e-mailu přes Office 365 pracovali na řešení problémů a přístupu k dalším cloudovým úlohám.

   * Organizace, které dříve nepovolily synchronizaci hodnot hash hesel, se musely uchýlit k nedůvěryhodným externím spotřebitelským e-mailovým systémům, aby bylo možné řešit problémy. V těchto případech jim trvalo týdny, než obnovili místní infrastrukturu identit, než se uživatelé mohli znovu přihlásit ke cloudovým aplikacím.

3. **Ochrana identity**. Jedním z nejlepších způsobů ochrany uživatelů v cloudu je Azure AD Identity Protection s Azure AD Premium P2. Společnost Microsoft neustále prohledává seznamy uživatelů a hesel, které špatní herci prodávají a zpřístupňují na tmavém webu. Azure AD můžete použít tyto informace k ověření, pokud jsou ohrožena některá uživatelská jména a hesla ve vaší organizaci. Proto je důležité povolit synchronizaci hodnot hash hesel bez ohledu na to, kterou metodu ověřování používáte, ať už je federovaná nebo předávací ověřování. Děravé přihlašovací údaje jsou prezentovány jako sestava. Tyto informace slouží k zablokování nebo vynucení uživatelů změnit svá hesla při pokusu o přihlášení pomocí uniklých hesel.

## <a name="conclusion"></a>Závěr

Tento článek popisuje různé možnosti ověřování, které organizace mohou konfigurovat a nasazovat pro podporu přístupu ke cloudovým aplikacím. Aby organizace splňovaly různé obchodní, bezpečnostní a technické požadavky, mohou si vybrat mezi synchronizací hodnot hash hesel, předávacím ověřováním a federací.

Zvažte každou metodu ověřování. Splňuje úsilí o nasazení řešení a uživatelské prostředí procesu přihlášení vaše obchodní požadavky? Vyhodnoťte, zda vaše organizace potřebuje pokročilé scénáře a funkce kontinuity provozu jednotlivých metod ověřování. Nakonec vyhodnoťte aspekty každé metody ověřování. Brání vám některý z nich v implementaci vaší volby?

## <a name="next-steps"></a>Další kroky

V dnešním světě jsou hrozby přítomny 24 hodin denně a přicházejí odevšad. Implementujte správnou metodu ověřování a zmírní tevaše bezpečnostní rizika a ochrání vaše identity.

[Začínáme](../../active-directory/fundamentals/get-started-azure-ad.md) s Azure AD a nasaďte správné ověřovací řešení pro vaši organizaci.

Pokud uvažujete o migraci z federovaného na cloudové ověřování, přečtěte si další informace o [změně metody přihlášení](../../active-directory/hybrid/plan-connect-user-signin.md). Chcete-li migraci naplánovat a implementovat, použijte [tyto plány nasazení projektu](https://aka.ms/deploymentplans) nebo zvažte použití nové funkce [postupného zavádění](../../active-directory/hybrid/how-to-connect-staged-rollout.md) k migraci federovaných uživatelů k použití cloudového ověřování v fázovaného přístupu.
