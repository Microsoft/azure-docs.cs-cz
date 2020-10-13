---
title: Ověřování pro řešení hybridních identit Azure AD
titleSuffix: Active Directory
description: Tato příručka pomáhá CEOs, ředitelé informačních technologií, CISO, hlavní architekty identity, podnikovým architektům a organizacím pro rozhodování IT, kteří zodpovídají za výběr metody ověřování pro řešení hybridních identit v Azure AD ve středně velkých organizacích.
keywords: ''
author: martincoetzer
ms.author: martinco
ms.date: 10/30/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: d5de8da548c2e141eb921aa4f95e82f7199ae1f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90602366"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Vyberte správnou metodu ověřování pro Azure Active Directory řešení hybridní identity.

Výběr správné metody ověřování je první záležitost, kterou organizace chtějí přesunout své aplikace do cloudu. Toto rozhodnutí neprovádějte lehce, z následujících důvodů:

1. Je to první rozhodnutí pro organizaci, která se chce přesunout do cloudu.

2. Metoda ověřování je kritická součást přítomnosti organizace v cloudu. Řídí přístup ke všem cloudovým datům a prostředkům.

3. Je základem všech dalších funkcí rozšířeného zabezpečení a uživatelského prostředí v Azure AD.

Identita je novou rovinou ovládacího prvku zabezpečení IT, takže ověřování je ochranná technologie pro přístup organizace k novému cloudovém světě. Organizace potřebují rovinu řízení identit, která posílí jejich zabezpečení a udržuje jejich cloudové aplikace v bezpečí před narušiteli.

> [!NOTE]
> Změna metody ověřování vyžaduje plánování, testování a případné výpadky. Postupné [zavedení](./how-to-connect-staged-rollout.md) je skvělým způsobem, jak testovat migraci uživatelů z federace na cloudové ověřování.

### <a name="out-of-scope"></a>Mimo rozsah
Organizacím, které nemají stávající místní adresář, se zaměřuje na tento článek. Tyto firmy obvykle vytvářejí identity pouze v cloudu, což nevyžaduje řešení hybridní identity. Pouze cloudové identity existují výhradně v cloudu a nejsou přidružené k odpovídajícím místním identitám.

## <a name="authentication-methods"></a>Metody ověřování
Když je řešení hybridní identity Azure AD vaší novou rovinou ovládacích prvků, ověřování je základem cloudového přístupu. Volba správné metody ověřování je zásadním prvním rozhodnutím při nastavování řešení hybridní identity Azure AD. Implementujte metodu ověřování, která je nakonfigurovaná pomocí Azure AD Connect, která taky zřídí uživatele v cloudu.

Pokud chcete zvolit metodu ověřování, musíte vzít v úvahu čas, stávající infrastrukturu, složitost a náklady na implementaci podle vašeho výběru. Tyto faktory jsou pro každou organizaci odlišné a můžou se v průběhu času měnit.

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

Azure AD podporuje následující metody ověřování pro řešení hybridních identit.

### <a name="cloud-authentication"></a>Cloudové ověřování
Když zvolíte tuto metodu ověřování, Azure AD bude zpracovávat přihlašovací procesy uživatelů. V kombinaci s bezproblémové jednotné přihlašování (SSO) se uživatelé můžou přihlašovat ke cloudovým aplikacím bez nutnosti opětovného zadání přihlašovacích údajů. Pomocí cloudového ověřování si můžete vybrat ze dvou možností:

**Synchronizace hodnot hash hesel Azure AD**. Nejjednodušší způsob, jak povolit ověřování pro místní adresářové objekty ve službě Azure AD. Uživatelé můžou používat stejné uživatelské jméno a heslo, které používají místně, aniž by museli nasazovat žádnou další infrastrukturu. Některé prémiové funkce Azure AD, jako jsou Identity Protection a [Azure AD Domain Services](../../active-directory-domain-services/tutorial-create-instance.md), vyžadují synchronizaci hodnot hash hesel bez ohledu na to, kterou metodu ověřování zvolíte.

> [!NOTE]
> Hesla se nikdy neukládají jako nešifrovaný text nebo se šifrují pomocí vratného algoritmu ve službě Azure AD. Další informace o skutečném procesu synchronizace hodnot hash hesel najdete v tématu [implementace synchronizace hodnot hash hesel pomocí Azure AD Connect synchronizace](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

**Předávací ověřování Azure AD**. Poskytuje jednoduché ověřování hesla pro služby ověřování Azure AD pomocí softwarového agenta, který běží na jednom nebo několika místních serverech. Servery ověřují uživatele přímo pomocí místní služby Active Directory, která zajišťuje, aby ověřování hesla neprobíhalo v cloudu.

Společnosti s požadavkem na zabezpečení, který hned vynutil stav místních uživatelských účtů, zásady hesel a hodiny přihlášení, můžou tuto metodu ověřování použít. Další informace o vlastním procesu předávacího ověřování najdete v tématu [přihlášení uživatele pomocí předávacího ověřování Azure AD](../../active-directory/hybrid/how-to-connect-pta.md).

### <a name="federated-authentication"></a>Federovaného ověřování
Když zvolíte tuto metodu ověřování, Azure AD předá proces ověřování do samostatného důvěryhodného ověřovacího systému, jako je třeba místní Active Directory Federation Services (AD FS) (AD FS), aby se ověřilo heslo uživatele.

Ověřovací systém může poskytovat další požadavky na pokročilé ověřování. Příklady jsou ověřování založené na čipové kartě nebo vícefaktorové ověřování třetí strany. Další informace najdete v tématu [nasazení Active Directory Federation Services (AD FS)](/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

Následující část vám pomůže rozhodnout, která metoda ověřování je pro vás nejvhodnější pomocí rozhodovacího stromu. Pomůže vám určit, jestli se má nasadit Cloud nebo federované ověřování pro vaše řešení hybridní identity Azure AD.

## <a name="decision-tree"></a>Rozhodovací strom

![Rozhodovací strom ověřování Azure AD](./media/choose-ad-authn/azure-ad-authn-image1.png)

Podrobnosti o rozhodovacích otázkách:

1. Azure AD dokáže zpracovávat přihlášení uživatelů, aniž by museli při ověřování hesel spoléhat na místní součásti.
2. Služba Azure AD může uživatele přihlašovat k důvěryhodnému zprostředkovateli ověřování, jako je AD FS Microsoftu.
3. Pokud potřebujete použít zásady zabezpečení služby Active Directory na úrovni uživatele, jako je například vypršení platnosti účtu, zakázaný účet, heslo vypršelo, účet uzamčený a přihlašovací hodiny pro každé přihlášení uživatele, služba Azure AD vyžaduje některé místní součásti.
4. Služba Azure AD nepodporuje funkce přihlašování nativně:
   * Přihlaste se pomocí čipových karet nebo certifikátů.
   * Přihlaste se pomocí místního MFA serveru.
   * Přihlaste se pomocí řešení ověřování třetí strany.
   * Řešení místního ověřování pro více webů.
5. Azure AD Identity Protection vyžaduje synchronizaci hodnot hash hesel bez ohledu na to, kterou metodu přihlašování zvolíte, a poskytněte *uživatelům nevrácené zprávy s přihlašovacími údaji* . Organizace můžou převzít služby při selhání synchronizace hodnot hash hesel, pokud jejich primární metoda přihlašování selže a byla nakonfigurovaná před událostí selhání.

> [!NOTE]
> Azure AD Identity Protection vyžadují licence [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) .

## <a name="detailed-considerations"></a>Podrobné pokyny

### <a name="cloud-authentication-password-hash-synchronization"></a>Cloudové ověřování: synchronizace hodnot hash hesel

* **Úsilí**. Synchronizace hodnot hash hesel vyžaduje nejméně úsilí týkající se nasazení, údržby a infrastruktury.  Tato úroveň úsilí se obvykle týká organizací, které potřebují, aby se uživatelé přihlásili k Microsoft 365, aplikacím SaaS a dalším prostředkům založeným na službě Azure AD. Pokud je tato funkce zapnutá, synchronizace hodnot hash hesel je součástí procesu Azure AD Connect synchronizace a spouští se každé dvě minuty.

* **Činnost koncového uživatele**. Pro zlepšení přihlašovacího prostředí uživatelů nasaďte bezproblémové jednotné přihlašování se synchronizací hodnot hash hesel. Bezproblémové jednotné přihlašování eliminuje zbytečné výzvy při přihlášení uživatelů.

* **Pokročilé scénáře**. Pokud organizace zvolí, je možné využít přehledy z identit a Azure AD Identity Protection sestav pomocí Azure AD Premium P2. Příkladem je nevrácená sestava přihlašovacích údajů. Windows Hello pro firmy má [specifické požadavky, pokud používáte synchronizaci hodnot hash hesel](/windows/access-protection/hello-for-business/hello-identity-verification). [Azure AD Domain Services](../../active-directory-domain-services/tutorial-create-instance.md) vyžaduje synchronizaci hodnot hash hesel, aby bylo možné zřídit uživatele se svými podnikovými přihlašovacími údaji ve spravované doméně.

    Organizace, které vyžadují vícefaktorové ověřování pomocí synchronizace hodnot hash hesel, musí používat Azure Multi-Factor Authentication nebo [vlastní ovládací prvky podmíněného přístupu](../../active-directory/conditional-access/controls.md#custom-controls-preview). Tyto organizace nemůžou používat jiné metody ověřování, které se spoléhají na federace.

> [!NOTE]
> Podmíněný přístup Azure AD vyžaduje licence [Azure AD Premium P1](https://azure.microsoft.com/pricing/details/active-directory/) .

* **Provozní kontinuita**. Použití synchronizace hodnot hash hesel s cloudovým ověřováním je vysoce dostupné jako cloudová služba, která se škáluje na všechna datová centra Microsoftu. Abyste se ujistili, že synchronizace hodnot hash hesel nefunguje po delší dobu, nasaďte druhý Azure AD Connect Server v pracovním režimu v pohotovostní konfiguraci.

* **Požadavky**. Synchronizace hodnot hash hesel v současné době neuplatňuje okamžitě změny v místních stavech účtů. V takové situaci má uživatel přístup ke cloudovým aplikacím, dokud se stav uživatelského účtu nesynchronizuje do Azure AD. Organizace můžou toto omezení překonat spuštěním nového synchronizačního cyklu poté, co správci hromadně aktualizují stavy místních uživatelských účtů. Příkladem je zakázání účtů.

> [!NOTE]
> Platnost hesla vypršela a stav uzamčeného účtu není v současné době synchronizovaný s Azure AD pomocí Azure AD Connect. Když změníte heslo uživatele a nastavíte, že *uživatel musí změnit heslo při příštím přihlášení* , hodnota hash hesla nebude synchronizovaná s Azure AD s Azure AD Connect, dokud uživatel nezmění heslo.

Postup nasazení najdete v tématu [implementace synchronizace hodnot hash hesel](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) .

### <a name="cloud-authentication-pass-through-authentication"></a>Cloudové ověřování: předávací ověřování  

* **Úsilí**. Pro předávací ověřování budete potřebovat jeden nebo více (doporučujeme tři) odlehčené agenty nainstalované na stávajících serverech. Tito agenti musí mít přístup k místním Active Directory Domain Services, včetně místních řadičů domény služby AD. Potřebují odchozí přístup k Internetu a přístup k řadičům domény. Z tohoto důvodu není podporováno nasazení agentů v hraniční síti.

    Předávací ověřování vyžaduje přístup k síti bez omezení přístupu k řadičům domény. Veškerý síťový provoz je zašifrovaný a omezený na požadavky na ověřování. Další informace o tomto procesu najdete v tématu [zabezpečení s hloubkovým podrobně](../../active-directory/hybrid/how-to-connect-pta-security-deep-dive.md) při předávacím ověřování.

* **Činnost koncového uživatele**. Pro zlepšení přihlašovacího prostředí uživatelů nasaďte bezproblémové jednotné přihlašování pomocí předávacího ověřování. Bezproblémové jednotné přihlašování eliminuje zbytečné výzvy po přihlášení uživatelů.

* **Pokročilé scénáře**. Předávací ověřování vynutilo zásady místních účtů v době přihlášení. Například přístup je odepřený, když je stav účtu místního uživatele zakázaný, uzamčený nebo [vypršela jeho platnost](../../active-directory/hybrid/how-to-connect-pta-faq.md#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) , nebo se pokus o přihlášení porovná mimo hodiny, kdy se uživatel může přihlásit.

    Organizace, které vyžadují vícefaktorové ověřování s předávacím ověřováním, musí používat službu Azure Multi-Factor Authentication (MFA) nebo [vlastní ovládací prvky podmíněného přístupu](../../active-directory/conditional-access/controls.md#custom-controls-preview). Tyto organizace nemůžou používat třetí nebo místní metodu ověřování, která se spoléhá na federaci. Rozšířené funkce vyžadují, aby se synchronizace hodnot hash hesel nasadila bez ohledu na to, jestli zvolíte předávací ověřování. Příkladem je nevrácená sestava přihlašovacích údajů Identity Protection.

* **Provozní kontinuita**. Doporučujeme, abyste nasadili dva další agenty pro předávací ověřování. Tyto doplňky jsou kromě prvního agenta na serveru Azure AD Connect. Toto další nasazení zajišťuje vysokou dostupnost žádostí o ověření. Pokud máte nasazené tři agenty, může se i tak, že jeden Agent selže, když je jiný agent k údržbě.

    Kromě předávacího ověřování existuje další výhoda pro nasazení synchronizace hodnot hash hesel. Funguje jako metoda ověření zálohy, když primární metoda ověřování již není k dispozici.

* **Požadavky**. Můžete použít synchronizaci hodnot hash hesel jako metodu ověření zálohování pro předávací ověřování, když agenti nemohou ověřit přihlašovací údaje uživatele z důvodu významného místního selhání. Převzetí služeb při selhání na synchronizaci hodnoty hash hesla se neprovádí automaticky a je nutné použít Azure AD Connect k ručnímu přepínání metody přihlášení.

    Další informace o předávacím ověřování, včetně podpory alternativních ID, najdete v tématu [Nejčastější dotazy](../../active-directory/hybrid/how-to-connect-pta-faq.md).

Postup nasazení najdete v tématu [implementace předávacího ověřování](../../active-directory/hybrid/how-to-connect-pta.md) .

### <a name="federated-authentication"></a>Federovaného ověřování

* **Úsilí**. Federované ověřování systém spoléhá na externí důvěryhodný systém pro ověřování uživatelů. Některé společnosti chtějí své stávající investice do federovaného systému znovu použít s řešením hybridních identit Azure AD. Údržba a Správa federovaného systému spadá mimo kontrolu Azure AD. Je až do organizace s využitím federovaného systému, aby bylo zajištěno, že je nasazené bezpečně a může zpracovávat zatížení ověřování.

* **Činnost koncového uživatele**. Uživatelské prostředí federovaného ověřování závisí na implementaci funkcí, topologie a konfigurace farmy federace. Některé organizace potřebují tuto flexibilitu pro přizpůsobení a konfiguraci přístupu ke farmě federace, aby vyhovovaly požadavkům na zabezpečení. Například je možné nakonfigurovat interně připojené uživatele a zařízení pro automatické přihlašování uživatelů bez výzvy k zadání přihlašovacích údajů. Tato konfigurace funguje, protože už je přihlášená ke svým zařízením. V případě potřeby jsou některé rozšířené funkce zabezpečení, které usnadňují proces přihlášení uživatelů, obtížnější.

* **Pokročilé scénáře**. Řešení federovaného ověřování se vyžaduje v případě, že zákazníci mají požadavek na ověření, že služba Azure AD nativně nepodporuje. V tématu Podrobné informace vám pomůžou [zvolit možnost správného přihlášení](/archive/blogs/samueld/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365). Vezměte v úvahu následující běžné požadavky:

  * Ověřování, které vyžaduje čipové karty nebo certifikáty.
  * Místní MFA servery nebo poskytovatelé vícefaktorového ověřování od jiných výrobců vyžadují federovaného poskytovatele identity.
  * Ověřování pomocí řešení ověřování třetích stran. Podívejte se na [seznam kompatibility federace Azure AD](../../active-directory/hybrid/how-to-connect-fed-compatibility.md).
  * Přihlaste se, který vyžaduje jako hlavní název uživatele (UPN), třeba doména \ uživatelské jméno (UPN), jako například user@domain.com .

* **Provozní kontinuita**. Federované systémy obvykle vyžadují pole serverů s vyrovnáváním zatížení, označované jako farma. Tato farma je nakonfigurovaná v interní síti a topologii hraniční sítě, aby se zajistila vysoká dostupnost požadavků na ověření.

    Nasaďte synchronizaci hodnot hash hesel společně se federovaném ověřováním jako metodou ověření zálohy, když primární metoda ověřování už není dostupná. Příkladem je, že místní servery nejsou k dispozici. Několik velkých podnikových organizací vyžaduje federační řešení pro podporu několika internetových vstupních bodů nakonfigurovaných s geografickým DNS pro žádosti o ověření s nízkou latencí.

* **Požadavky**. Federované systémy obvykle vyžadují významnou investici do místní infrastruktury. Většina organizací tuto možnost zvolí, pokud už mají místní federační investici. A pokud se jedná o silný podnikový požadavek na použití zprostředkovatele s jedinou identitou. Federace je složitější pro provoz a řešení problémů v porovnání s řešením ověřování v cloudu.

V případě Nesměrovatelné domény, která se nedá ověřit ve službě Azure AD, potřebujete dodatečnou konfiguraci k implementaci přihlašovacího ID uživatele. Tento požadavek se označuje jako alternativní podpora přihlašovacích ID. Omezení a požadavky najdete v tématu [Konfigurace alternativního přihlašovacího ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) . Pokud se rozhodnete použít poskytovatele Multi-Factor Authentication od jiného výrobce s federaci, zajistěte, aby zprostředkovatel podporoval WS-Trust, aby se zařízení mohla připojit k Azure AD.

Postup nasazení najdete v tématu [nasazení federačních serverů](/windows-server/identity/ad-fs/deployment/deploying-federation-servers) .

> [!NOTE]
> Když nasadíte řešení hybridní identity Azure AD, musíte implementovat jeden z podporovaných topologií Azure AD Connect. Přečtěte si další informace o podporovaných a nepodporovaných konfiguracích v [topologiích pro Azure AD Connect](../../active-directory/hybrid/plan-connect-topologies.md).

## <a name="architecture-diagrams"></a>Diagramy architektury

Následující diagramy popisují komponenty architektury nejvyšší úrovně, které jsou potřeba pro jednotlivé metody ověřování, které můžete použít s řešením hybridní identity Azure AD. Poskytují přehledy, které vám pomůžou porovnat rozdíly mezi řešeními.

* Jednoduchost řešení synchronizace hodnot hash hesel:

    ![Hybridní identita Azure AD se synchronizací hodnot hash hesel](./media/choose-ad-authn/azure-ad-authn-image2.png)

* Požadavky na agenta předávacího ověřování pomocí dvou agentů pro redundanci:

    ![Azure AD Hybrid identity s předávacím ověřováním](./media/choose-ad-authn/azure-ad-authn-image3.png)

* Komponenty požadované pro federaci v hraničních a interních sítích vaší organizace:

    ![Hybridní identita Azure AD s federovaným ověřováním](./media/choose-ad-authn/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Porovnávání metod

|Aspekty|Synchronizace hodnot hash hesel + bezproblémové jednotné přihlašování|Předávací ověřování + bezproblémové jednotné přihlašování|Federace se službou AD FS|
|:-----|:-----|:-----|:-----|
|Kde k ověřování dochází?|V cloudu|V cloudu po výměně zabezpečeného ověřování hesla pomocí místního ověřovacího agenta|Místní|
|Jaké jsou požadavky na místní server nad rámec zřizovacího systému: Azure AD Connect?|Žádné|Jeden server pro každého dalšího ověřovacího agenta|Dva nebo více AD FS serverů<br><br>Dva nebo více serverů WAP v hraniční/DMZ síti|
|Jaké jsou požadavky na místní Internet a sítě mimo zřizovací systém?|Žádné|[Odchozí internetový přístup](../../active-directory/hybrid/how-to-connect-pta-quick-start.md) ze serverů používajících ověřovací agenty|[Příchozí internetový přístup](/windows-server/identity/ad-fs/overview/ad-fs-requirements) k serverům WAP v hraniční síti<br><br>Příchozí síťový přístup k serverům AD FS ze serverů WAP v hraniční síti<br><br>Vyrovnávání zatížení sítě|
|Existuje požadavek na certifikát TLS/SSL?|No|No|Yes|
|Existuje nějaké řešení pro monitorování stavu?|Nevyžadováno|Stav agenta poskytnutý [centrem pro správu Azure Active Directory](../../active-directory/hybrid/tshoot-connect-pass-through-authentication.md)|[Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md)|
|Přihlašuje uživatelé k prostředkům cloudu jednotné přihlašování ze zařízení připojených k doméně v podnikové síti?|Ano, [bez problémů s jednotným PŘIhlašováním](../../active-directory/hybrid/how-to-connect-sso.md)|Ano, [bez problémů s jednotným PŘIhlašováním](../../active-directory/hybrid/how-to-connect-sso.md)|Yes|
|Jaké typy přihlašování se podporují?|UserPrincipalName + heslo<br><br>Windows-Integrated ověřování pomocí [bezproblémového jednotného PŘIhlašování](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[Alternativní ID přihlášení](../../active-directory/hybrid/how-to-connect-install-custom.md)|UserPrincipalName + heslo<br><br>Windows-Integrated ověřování pomocí [bezproblémového jednotného PŘIhlašování](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[Alternativní ID přihlášení](../../active-directory/hybrid/how-to-connect-pta-faq.md)|UserPrincipalName + heslo<br><br>sAMAccountName + heslo<br><br>Ověřování Windows-Integrated<br><br>[Ověřování pomocí certifikátu a čipové karty](/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Alternativní ID přihlášení](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Podporuje se Windows Hello pro firmy?|[Klíčový model vztahu důvěryhodnosti](/windows/security/identity-protection/hello-for-business/hello-identity-verification)|[Klíčový model vztahu důvěryhodnosti](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>*Vyžaduje úroveň funkčnosti domény Windows Server 2016.*|[Klíčový model vztahu důvěryhodnosti](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Model vztahu důvěryhodnosti certifikátu](/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Jaké jsou možnosti vícefaktorového ověřování?|[Azure MFA](/azure/multi-factor-authentication/)<br><br>[Vlastní ovládací prvky s podmíněným přístupem *](../../active-directory/conditional-access/controls.md)|[Azure MFA](/azure/multi-factor-authentication/)<br><br>[Vlastní ovládací prvky s podmíněným přístupem *](../../active-directory/conditional-access/controls.md)|[Azure MFA](/azure/multi-factor-authentication/)<br><br>[Azure MFA Server](../../active-directory/authentication/howto-mfaserver-deploy.md)<br><br>[Vícefaktorové ověřování třetích stran](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[Vlastní ovládací prvky s podmíněným přístupem *](../../active-directory/conditional-access/controls.md)|
|Jaké stavy uživatelských účtů se podporují?|Zakázané účty<br>(až 30 minut zpoždění)|Zakázané účty<br><br>Účet uzamčen<br><br>Platnost účtu vypršela.<br><br>Platnost hesla vypršela.<br><br>Přihlašovací hodiny|Zakázané účty<br><br>Účet uzamčen<br><br>Platnost účtu vypršela.<br><br>Platnost hesla vypršela.<br><br>Přihlašovací hodiny|
|Jaké jsou možnosti podmíněného přístupu?|[Podmíněný přístup Azure AD s Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Podmíněný přístup Azure AD s Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Podmíněný přístup Azure AD s Azure AD Premium](../../active-directory/conditional-access/overview.md)<br><br>[AD FS pravidla deklarace identity](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Je podporováno blokování starších verzí protokolů?|[Ano](../../active-directory/conditional-access/overview.md)|[Ano](../../active-directory/conditional-access/overview.md)|[Ano](/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Můžete přizpůsobit logo, obrázek a popis na přihlašovacích stránkách?|[Ano, s Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Ano, s Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Ano](../../active-directory/hybrid/how-to-connect-fed-management.md)|
|Jaké pokročilé scénáře jsou podporovány?|[Inteligentní uzamčení hesla](../../active-directory/authentication/howto-password-smart-lockout.md)<br><br>[Nevrácené sestavy pověření s Azure AD Premium P2](../identity-protection/overview-identity-protection.md)|[Inteligentní uzamčení hesla](../../active-directory/authentication/howto-password-smart-lockout.md)|Systém ověřování s nízkou latencí ve více lokalitách<br><br>[AD FS uzamčení extranetu](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[Integrace se systémy identit třetích stran](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)|

> [!NOTE]
> Vlastní ovládací prvky ve službě Azure AD podmíněný přístup aktuálně nepodporují registraci zařízení.

## <a name="recommendations"></a>Doporučení
Váš systém identity zajišťuje uživatelům přístup ke cloudovým aplikacím a obchodním aplikacím, které migrujete a zpřístupníte v cloudu. K zajištění produktivity a nesprávných aktérů z citlivých dat vaší organizace kontroluje ověřování oprávnění k přístupu k aplikacím.

Použijte nebo povolte synchronizaci hodnot hash hesel podle zvolené metody ověřování, a to z následujících důvodů:

1. **Vysoká dostupnost a zotavení po havárii**. Předávací ověřování a federace spoléhají na místní infrastrukturu. V případě předávacího ověřování zahrnuje místní nároky serverový hardware a sítě, které agenti předávacího ověřování vyžadují. U federace jsou místní nároky ještě větší. Vyžaduje servery v hraniční síti pro žádosti o ověření proxy serveru a interní federační servery.

    Aby nedocházelo k jednomu bodu selhání, nasaďte redundantní servery. Požadavky na ověření budou vždy obsluhované, pokud dojde k chybě nějaké součásti. Předávací ověřování i federace také spoléhají na řadiče domény, aby reagovaly na žádosti o ověření. to může také selhat. Mnohé z těchto komponent potřebují údržbu, aby zůstaly v dobrém stavu. Výpadky jsou pravděpodobnější, pokud údržba není plánována a implementována správně. Vyhněte se výpadkům pomocí synchronizace hodnot hash hesel, protože služba Microsoft Azure AD Cloud Authentication se globálně škáluje a je vždy k dispozici.

2. **Přežití místního výpadku**.  Důsledky místního výpadku v důsledku internetového útoku nebo havárie můžou být zásadní, od poškození značky reputace až po paralyzed organizaci, která není schopná zabývat se útokem. V poslední době bylo mnoho organizací obětí malwarových útoků, včetně cílových ransomwarem, což způsobilo, že jejich místní servery přestanou platit. Když Microsoft pomáhá zákazníkům pracovat s těmito typy útoků, uvidí dvě kategorie organizací:

   * Organizace, které dříve zároveň zapnuly synchronizaci hodnot hash hesel nad federovaným nebo předávacím ověřováním, změnily primární metodu ověřování tak, aby pak používaly synchronizaci hodnot hash hesel. Během několika hodin byly zpět online. Pomocí přístupu k e-mailu prostřednictvím Microsoft 365 pracovali s cílem vyřešit problémy a přistupovat k dalším cloudovým úlohám.

   * Organizace, které dříve nepovolily synchronizaci hodnot hash hesel, musely při komunikaci s řešeními problémů nedůvěřovat externím e-mailovým systémům externích uživatelů. V těchto případech trvalo, že jsou týdny obnoveny místní infrastruktury identit, než se uživatelé budou moci znovu přihlásit ke cloudovým aplikacím.

3. **Identity Protection**. Jedním z nejlepších způsobů ochrany uživatelů v cloudu je Azure AD Identity Protection s Azure AD Premium P2. Microsoft nepřetržitě vyhledává v Internetu seznam uživatelů a hesel, které jsou v tmavém webu k dispozici jako chybné. Azure AD může tyto informace využít k tomu, aby ověřil, jestli jsou některá z uživatelských jmen a hesel ve vaší organizaci ohrožená. Proto je důležité povolit synchronizaci hodnot hash hesel bez ohledu na to, jakou metodu ověřování používáte, ať už se jedná o federované nebo předávací ověřování. Nevrácená pověření jsou uvedena jako sestava. Tyto informace slouží k blokování nebo vynucení změn hesel uživatelů při pokusu o přihlášení pomocí nevrácených hesel.

## <a name="conclusion"></a>Závěr

Tento článek popisuje různé možnosti ověřování, které můžou organizace konfigurovat a nasazovat pro podporu přístupu ke cloudovým aplikacím. Organizace si můžou vybrat mezi synchronizací hodnot hash hesel, předávacím ověřováním a federacem, aby splnila různé obchodní požadavky.

Vezměte v úvahu jednotlivé metody ověřování. Je úsilí k nasazení řešení a uživatelské prostředí procesu přihlašování řešit vaše podnikové požadavky? Vyhodnoťte, jestli vaše organizace potřebuje pokročilé scénáře a funkce pro provozní kontinuitu jednotlivých metod ověřování. Nakonec vyhodnoťte požadavky každé metody ověřování. Je možné, že vám některé z nich zabrání v implementaci vaší volby?

## <a name="next-steps"></a>Další kroky

V dnešním světě jsou hrozby k dispozici 24 hodin denně a pocházejí od všech. Implementujte správnou metodu ověřování a bude zmírnit rizika zabezpečení a chránit vaše identity.

[Začněte s Azure](../fundamentals/active-directory-whatis.md) AD a nasaďte správné řešení ověřování pro vaši organizaci.

Pokud uvažujete o migraci z federovaných na cloudové ověřování, přečtěte si další informace o tom, jak [se mění Metoda přihlašování](../../active-directory/hybrid/plan-connect-user-signin.md). Abychom vám pomohli naplánovat a implementovat migraci, použít [tyto plány nasazení projektu](https://aka.ms/deploymentplans) nebo zvážit použití nové [fáze přípravy](../../active-directory/hybrid/how-to-connect-staged-rollout.md) pro migraci federovaných uživatelů na použití cloudového ověřování v rámci dvoufázového přístupu.