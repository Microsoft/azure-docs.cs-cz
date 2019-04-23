---
title: Plánování a spusťte nasazení služby Azure Multi-Factor Authentication – Azure Active Directory
description: Plánování nasazení Microsoft Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c2c5006eb050b70b783ab8199724e0e98766381
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60359304"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication"></a>Plánování ověřování Azure Multi-Factor Authentication založené na cloudu

Uživatelé se připojují k prostředkům organizace v čím dál složitější scénáře. Lidé připojení ze zařízení vlastněná organizací, osobní a veřejné zapnutí a vypnutí podnikové síti prostřednictvím smartphonů, tabletů, počítačů a přenosné počítače, často ve více platformách. V tomto světě vždy připojen, různá zařízení a multiplatformní zabezpečení uživatelských účtů je důležitější než kdy dřív. Hesla, bez ohledu na jejich složitost, použít napříč zařízeními, sítěmi a platformami, už nejsou dostatečná k zajištění zabezpečení účtu uživatele, zejména v případě, že uživatelé mívají sklony opakovaného použití předchozích hesel pro účty. Pokročilé útoky phishing a dalších sociálního inženýrství, útoky může způsobit uživatelských jmen a hesel se pošle a prodaných v dark web.

[Azure Multi-Factor Authentication (MFA)](concept-mfa-howitworks.md) pomáhá chránit přístup k datům a aplikacím. Poskytuje další vrstvu zabezpečení pomocí druhou podobu ověřování. Organizace můžou použít [podmíněného přístupu](../conditional-access/overview.md) aby řešení podle svých potřeb.

## <a name="prerequisites"></a>Požadavky

Před zahájením nasazení služby Azure Multi-Factor Authentication, jsou položky požadovaných součástí, které by se měly zvažovat.

| Scénář | Požadavek |
| --- | --- |
| **Výhradně cloudový** prostředí identit s moderním ověřováním | **Žádné další požadované úkoly** |
| **Hybridní** identity scénáře | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) nasazuje a identity uživatelů jsou synchronizovaných nebo sdružených se službou místní Active Directory Domain Services se službou Azure Active Directory. |
| Místní starší verze aplikací publikovaných pro přístup k cloudu | Azure AD [Proxy aplikací](../manage-apps/application-proxy.md) nasazení. |
| Použití Azure MFA s ověřováním protokolu RADIUS | A [Server NPS (Network Policy Server)](howto-mfa-nps-extension.md) nasazení. |
| Uživatelé mají aplikace Microsoft Office 2010 nebo starší, nebo e-mailu společnosti Apple pro iOS 11 nebo starší | Upgrade na [Microsoft Office 2013 nebo novější](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) a e-mailu společnosti Apple pro iOS 12 nebo novější. Podmíněný přístup není podporovaný ve starší verzi ověřovacích protokolů. |

## <a name="plan-user-rollout"></a>Plán zavedení uživatele

Pilotní nasazení za nímž následuje vlny nasazení, které jsou v rámci kapacitu pro podporu by měl obsahovat plánu nasazení identifikujete vícefaktorové ověřování. Zavedení začněte tím, že uplatnění zásad podmíněného přístupu pro malou skupinu uživatelů pilotního nasazení. Po vyhodnocení účinku na uživatelů pilotního nasazení, proces a chování registrace, může do zásady přidat další skupiny nebo přidat další uživatele do existujících skupin.

### <a name="user-communications"></a>Komunikaci uživatele

Je velmi důležité informovat uživatele, v rámci plánované komunikace o nadcházejících změnách, požadavky na registraci Azure MFA a všechny akce nezbytné uživatele. Doporučujeme, abyste komunikace se vyvíjejí společně s zástupců v rámci vaší organizace, jako je například komunikace, Správa změn nebo lidské zdroje oddělení.

Společnost Microsoft poskytuje [šablony komunikace](https://aka.ms/mfatemplates) a [dokumentaci pro koncové uživatele](../user-help/security-info-setup-signin.md) umožňující koncept komunikace. Odešlete uživatelům [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com) zaregistrovat přímo tak, že vyberete **bezpečnostní údaje** odkazy na této stránce.

## <a name="deployment-considerations"></a>Důležité informace o nasazení

Azure Multi-Factor Authentication se nasadí formou vynucování zásad s podmíněným přístupem. A [zásady podmíněného přístupu](../conditional-access/overview.md) vyžadovat, aby uživatelé provést ověřování službou Multi-Factor Authentication při splnění určitých kritérií, jako:

* Všichni uživatelé, konkrétního uživatele, skupiny nebo přiřazených rolí
* Konkrétních cloudových aplikací, ke kterému přistupujete
* Platforma zařízení
* Stav zařízení
* Umístění v síti nebo geograficky rozmístěná IP adresa
* Klientské aplikace
* Riziko přihlášení (vyžaduje Identity Protection)
* Odpovídající zařízení
* Zařízení připojené k hybridní službě Azure AD
* Schválené klientské aplikace
 

Pomocí přizpůsobitelných plakáty a e-mailové šablony [materiálů uvedení služby Multi-Factor authentication] zavedení služby Multi-Factor authentication pro vaši organizaci. (https://www.microsoft.com/en-us/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all)

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Povolení služby Multi-Factor Authentication s podmíněným přístupem

Zásady podmíněného přístupu vynucovat registrace vyžadující neregistrovaným uživatelů k dokončení registrace při prvním přihlášení, k posouzení důležité zabezpečení.


[Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md) přispívá zásadu registrace i zásady rizik automatizované zjišťování a náprava na scénář ověřování Azure Multi-Factor Authentication. Zásady je možné vytvořit přinutit změnu hesla při ohrožení ohrožení zabezpečení identity nebo vyžadovat vícefaktorové ověřování při přihlašování se změnami se bude považovat za riziková provedením následujícího [události](../reports-monitoring/concept-risk-events.md):

* Uniklé přihlašovací údaje
* Přihlášení z anonymních IP adres
* Nemožná cesta do netypických míst
* Přihlášení z neznámých míst
* Přihlášení z nakažených zařízení
* Přihlášení z IP adresy s podezřelými aktivitami

Některé z rizikových událostí detekovaných službou Azure Active Directory Identity Protection dochází v reálném čase a některé vyžadují zpracování v režimu offline. Správci můžou k blokování uživatelů, kteří je nebezpečné chování a nápravě ručně, vyžadovat změnu hesla nebo vyžadovat vícefaktorové ověřování jako součást své zásady podmíněného přístupu.

## <a name="define-network-locations"></a>Definovat umístění v síti

Doporučujeme, že organizace používat podmíněný přístup k definování jejich síť používání [pojmenovaná umístění](../conditional-access/location-condition.md#named-locations). Pokud vaše organizace používá Identity Protection, zvažte použití zásady na základě rizik místo pojmenovaná umístění.

### <a name="configuring-a-named-location"></a>Konfigurace pojmenovaných umístění

1. Otevřít **Azure Active Directory** na webu Azure Portal
2. Klikněte na tlačítko **podmíněného přístupu**
3. Klikněte na tlačítko **pojmenovaná umístění**
4. Klikněte na tlačítko **nové umístění**
5. V **název** pole, zadejte výstižný název
6. Vyberte, zda jsou definovat umístění pomocí rozsahy IP adres nebo země/oblasti
   1. Pokud používáte rozsahy IP adres
      1. Rozhodněte, jestli se má označit jako důvěryhodné umístění. Přihlášení z důvěryhodného umístění snižuje riziko při přihlášení uživatele. Toto umístění jako důvěryhodné označte, pouze pokud víte, že zadané rozsahy IP adres jsou zřízené a důvěryhodné ve vaší organizaci.
      2. Zadejte rozsahy IP adres
   2. Pokud používáte země/oblasti
      1. Rozbalte rozevírací nabídku a vyberte zemích nebo oblastech, které chcete definovat pro toto umístění s názvem.
      2. Rozhodněte, jestli se mají zahrnout neznámé oblasti. Neznámé oblasti jsou IP adresy, které nejdou namapovat na zemi nebo oblast.
7. Klikněte na **Vytvořit**

## <a name="plan-authentication-methods"></a>Plánování metody ověřování

Správci mohou rozhodnout, [metody ověřování](../authentication/concept-authentication-methods.md) , které chtějí zpřístupnit uživatelům. Je důležité a více než jedné ověřovací metody umožňuje, aby uživatelé mají je záložní metoda, která je dostupná v případě, že primární metodu není k dispozici. Jsou k dispozici pro správce, aby tyto metody:

### <a name="notification-through-mobile-app"></a>Oznámení přes mobilní aplikaci

Nabízené oznámení, přijde na aplikaci Microsoft Authenticator na vašem mobilním zařízení. Uživatel zobrazí oznámení a vybere **schválit** dokončete ověření. Nabízená oznámení přes mobilní aplikaci poskytují možnost nejméně rušivá pro uživatele. Jsou také možnost nejvíce spolehlivou a zabezpečenou protože používají datové připojení spíše než telefonního subsystému.

> [!NOTE]
> Pokud má vaše organizace zaměstnanci práci nebo cestě do Číny, **oznámení přes mobilní aplikaci** metodu na **zařízení s Androidem** nefunguje v dané zemi. Alternativní metody by měla být k dispozici pro uživatele.

### <a name="verification-code-from-mobile-app"></a>Ověřovací kód z mobilní aplikace

Mobilní aplikace, jako aplikace Microsoft Authenticator generuje každých 30 sekund nový ověřovací kód OATH. Ověřovací kód, který uživatel zadá do rozhraní pro přihlášení. Možnost mobilní aplikace je možné, jestli má telefonu mobilní signál a data.

### <a name="call-to-phone"></a>Telefonní hovor

Automatizovaný hlasový hovor je umístěn na uživatele. Uživatel odpoví volání a stisknutí **#** na klávesnici telefonu provede ověření schválení. Volání na telefon je skvělé záložní metoda pro oznámení nebo ověřovací kód z mobilní aplikace.

### <a name="text-message-to-phone"></a>Textová zpráva na telefon

Textovou zprávu obsahující ověřovací kód je odeslán uživateli, bude uživatel vyzván k zadání ověřovacího kódu do rozhraní pro přihlášení.

### <a name="choose-verification-options"></a>Vyberte možnosti ověřování

1. Přejděte do **Azure Active Directory**, **uživatelé**, **ověřování službou Multi-Factor Authentication**.

   ![Přístup k portálu Multi-Factor Authentication v okně uživatelé Azure AD na webu Azure portal](media/howto-mfa-getstarted/users-mfa.png)

1. V nové záložce, která se otevře procházením **nastavení služby**.
1. V části **možnosti ověření**, zkontrolujte všechna pole pro metody dostupné pro uživatele.

   ![Konfigurace metody ověření v kartě nastavení služby Multi-Factor Authentication](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Klikněte na **Uložit**.
1. Zavřít **nastavení služby** kartu.

## <a name="plan-registration-policy"></a>Zásady registrace pro plán

Správci musí zjistit, jak budou uživatelé registrovat svoje metody. Organizace by si měla [povolit nové prostředí kombinovaná registrace](howto-registration-mfa-sspr-combined.md) pro Azure MFA a samoobslužného obnovení (SSPR). Samoobslužné resetování HESLA umožňuje uživatelům resetovat své heslo bezpečně doručovat pomocí stejných metod, které používají pro ověřování službou Multi-Factor Authentication. Doporučujeme, abyste že toto kombinované registrace aktuálně ve verzi public preview, protože je skvělé prostředí pro uživatele, s možností registrace jednou pro obě služby. Povolení stejné metody pro samoobslužné resetování HESLA a vícefaktorové ověřování Azure vám umožní vašim uživatelům zaregistrované k používání obou funkcí.

### <a name="registration-with-identity-protection"></a>Registrace ve službě Identity Protection

Pokud vaše organizace používá Azure Active Directory Identity Protection, [nakonfigurovat zásady registrace MFA](../identity-protection/howto-mfa-policy.md) vyzvat uživatele k registraci při příštím přihlášení interaktivně.

### <a name="registration-without-identity-protection"></a>Registrace bez identity Protection

Pokud vaše organizace nemá žádné licence, které umožňují Identity Protection, uživatelům se výzva k zaregistrovat další čas, který se vyžaduje při přihlášení vícefaktorové ověřování. Uživatele nelze registrovat pro vícefaktorové ověřování, není použití aplikací chráněných pomocí vícefaktorového ověřování. Je důležité získat všichni uživatelé zaregistrovat, aby nesprávnými účastníky nemůže odhadnout heslo uživatele a registrace pro vícefaktorové ověřování jejich jménem efektivně převzetí kontroly nad účet.

#### <a name="enforcing-registration"></a>Vynucení registrace

Pomocí následujících kroků zásady podmíněného přístupu můžete vynutit uživatelé zaregistrovali k ověření službou Multi-Factor Authentication

1. Vytvořte skupinu a přidejte všechny uživatele není aktuálně registrován.
2. Pomocí podmíněného přístupu, vynutit ověřování Multi-Factor Authentication pro tuto skupinu pro přístup ke všem prostředkům.
3. Pravidelně znovu vyhodnoťte členství ve skupině a odebrat ze skupiny uživatelů, kteří zaregistrovali.

Zaregistrovaná a neregistrované uživatelé vícefaktorového ověřování Azure může identifikovat pomocí příkazů prostředí PowerShell, které využívají [modulu MSOnline Powershellu](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).

#### <a name="identify-registered-users"></a>Identifikaci registrovaných uživatelů

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Identifikace uživatele služby není zaregistrovaný

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

## <a name="plan-conditional-access-policies"></a>Plánování zásad podmíněného přístupu

Plánování strategie zásad podmíněného přístupu, které určí, kdy se vyžaduje vícefaktorové ověřování a další ovládací prvky, najdete v tématu [co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md).

Je důležité, abyste zabránili neúmyslně zamknutí mimo vašeho tenanta Azure AD. Můžete zmírnit dopad tato zvyšuje ochranu před nechtěnými nedostatečná přístup pro správu podle [vytvoření dvou nebo více účtů pro nouzový přístup ve vašem tenantovi](../users-groups-roles/directory-emergency-access.md) a nezahrne je ze své zásady podmíněného přístupu.

### <a name="create-conditional-access-policy"></a>Vytvoření zásad podmíněného přístupu

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí účtu globálního správce.
1. Přejděte do **Azure Active Directory**, **podmíněného přístupu**.
1. Vyberte **nové zásady**.
1. Zadejte výstižný název zásady.
1. V části **uživatelů a skupin**:
   * Na **zahrnout** kartu, vyberte **všichni uživatelé** přepínací tlačítko
   * Na **vyloučit** kartu, zaškrtněte políčko u **uživatelů a skupin** a vyberte vaše účty pro nouzový přístup.
   * Klikněte na **Done** (Hotovo).
1. V části **cloudové aplikace**, vyberte **všechny cloudové aplikace** přepínač.
   * VOLITELNĚ MŮŽETE: Na **vyloučit** , vyberte cloudové aplikace, které vaše organizace nevyžaduje vícefaktorové ověřování pro.
   * Klikněte na **Done** (Hotovo).
1. V části **podmínky** části:
   * VOLITELNĚ MŮŽETE: Pokud jste povolili službu Azure Identity Protection, můžete vyhodnotit riziko přihlášení jako součást této zásady.
   * VOLITELNĚ MŮŽETE: Pokud jste nakonfigurovali důvěryhodných umístění nebo pojmenovaná umístění, můžete zahrnout nebo vyloučit ze zásad těchto umístěních.
1. V části **udělení**, ujistěte se, že **udělit přístup** je přepínač vybrán.
    * Zaškrtněte políčko u **vyžadovat vícefaktorové ověřování**.
    * Klikněte na **Vybrat**.
1. Přeskočit **relace** oddílu.
1. Nastavte **povolit zásady** přepnutím **na**.
1. Klikněte na možnost **Vytvořit**.

![Vytvoření zásad podmíněného přístupu povolit MFA pro uživatele Azure portal v pilotní skupině](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

## <a name="plan-integration-with-on-premises-systems"></a>Plánování integrace s místními systémy

Některé starší verze a místní aplikace, které nejsou ověřeny přímo ve službě Azure AD vyžadují další kroky pro použití vícefaktorového ověřování, včetně:

* Starší verze místních aplikací, které budete muset použít proxy aplikace.
* Místní aplikace pomocí protokolu RADIUS, které bude nutné použít adaptér vícefaktorové ověřování se serverem NPS.
* Místní služby AD FS aplikace, které bude nutné použít adaptér MFA s AD FS 2016.

Aplikace, které přímé ověření u služby Azure AD a využívají moderní ověřování (WS-Fed, SAML, OAuth a OpenID Connect) můžete provést přímo pomocí zásad podmíněného přístupu.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Použití Azure MFA s Azure AD Application Proxy

Aplikace nacházejícím místní mohou být publikovány do služby Azure AD tenant prostřednictvím [Proxy aplikací Azure AD](../manage-apps/application-proxy.md) a můžete využít ověřování Azure Multi-Factor Authentication jsou nakonfigurovány pro použití služby Azure AD-li předběžné ověření.

Tyto aplikace se vztahují zásady podmíněného přístupu, které vynutit ověřování Azure Multi-Factor Authentication, stejně jako jakékoli jiné Azure integrované s Active Directory.

Podobně pokud Azure Multi-Factor Authentication se vynucují pro všechna přihlášení uživatele, s místními budou chráněné aplikace publikované pomocí Proxy aplikace Azure AD.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>Integrace s Network Policy Server Azure Multi-Factor Authentication

Rozšíření serveru NPS (Network Policy Server) pro Azure MFA přidává funkce vícefaktorové ověřování založené na cloudu pro vaši infrastrukturu ověřování pomocí existujících serverů. Pomocí rozšíření NPS můžete přidat do vašeho existujícího toku ověřování telefonní hovor, textová zpráva nebo ověřovací telefonní aplikace. Tato integrace má následující omezení:

* U protokolu CHAPv2 jsou podporovány pouze nabízená oznámení v ověřovací aplikaci a hlasový hovor.
* Nejde použít zásady podmíněného přístupu.

Rozšíření NPS funguje jako adaptér mezi RADIUS a založené na cloudu Azure MFA poskytuje druhý faktor ověřování k ochraně [VPN](howto-mfa-nps-extension-vpn.md), [připojení brány vzdálené plochy](howto-mfa-nps-extension-rdg.md), nebo jiné podporující RADIUS aplikace. Uživatele, že registrace pro Azure MFA v tomto prostředí budou být ovlivňuje všechny pokusy o ověření, chybějící střední zásady podmíněného přístupu vícefaktorové ověřování je vždy povinné.

#### <a name="implementing-your-nps-server"></a>Implementace serveru NPS

Pokud máte nasazené instance NPS a používá už, odkazovat [integrace vaší stávající infrastruktury NPS pomocí ověřování Azure Multi-Factor Authentication](howto-mfa-nps-extension.md). Pokud server NPS nastavujete poprvé, přečtěte si [Server NPS (Network Policy Server)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) pokyny. Pokyny při řešení potíží najdete v článku [řešení chybových zpráv z rozšíření NPS pro Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Příprava serveru NPS pro uživatele, která nejsou zaregistrovaná pro vícefaktorové ověřování

Zvolte, co se stane při pokusu o ověření uživatele, která nejsou zaregistrovaná pomocí vícefaktorového ověřování. Pomocí nastavení registru `REQUIRE_USER_MATCH` v cestě registru `HKLM\Software\Microsoft\AzureMFA` můžete řídit chování funkce. Toto nastavení nemá možnost jediné konfiguraci.

| Klíč | Value | Výchozí |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | TRUE / FALSE | Není nastavený (ekvivalentní na hodnotu TRUE) |

Účelem tohoto nastavení je určit, co dělat, když uživatel není zaregistrovaný pro MFA. V následující tabulce jsou uvedeny účinky změnou tohoto nastavení.

| Nastavení | Stav uživatele vícefaktorové ověřování | Účinky |
| --- | --- | --- |
| Klíč neexistuje. | Nezaregistrováno | Neproběhne úspěšně ověřovacím testem MFA |
| Hodnota nastavena na hodnotu True / Nenastaveno | Nezaregistrováno | Neproběhne úspěšně ověřovacím testem MFA |
| Klíč nastavený na hodnotu False | Nezaregistrováno | Ověřování bez MFA |
| Klíč nastavený na hodnotu False nebo hodnotu True | Zaregistrovat | Musí ověřit pomocí vícefaktorového ověřování |

### <a name="integrate-with-active-directory-federation-services"></a>Integrace s Active Directory Federation Services

Pokud je vaše organizace Federovaná pomocí služby Azure AD, můžete použít [ověřování Azure Multi-Factor Authentication pro zabezpečení služby AD FS prostředků](multi-factor-authentication-get-started-adfs.md), v místním prostředí i v cloudu. Azure MFA můžete omezit hesla a poskytují bezpečnější způsob ověřování. Od verze Windows serveru 2016, můžete teď nakonfigurovat Azure MFA pro primární ověřování.

Na rozdíl od se službou AD FS ve Windows serveru 2012 R2, Azure MFA adapterem služby AD FS 2016 se integruje přímo do Azure AD a nevyžaduje serveru Azure MFA v místním. Azure MFA adapterem je integrovaný do Windows serveru 2016 a není nutné pro další instalace.

Při použití Azure MFA s AD FS 2016 a cílovou aplikaci podléhají zásadám podmíněného přístupu, existují další požadavky:

* Podmíněný přístup je k dispozici, když je aplikace předávající strany do služby Azure AD Federovaná pomocí služby AD FS 2016.
* Podmíněný přístup není k dispozici, když je aplikace předávající strany k AD FS 2016 a je spravovaná nebo sdružených se službou AD FS 2016.
* Podmíněný přístup také není k dispozici a pokud AD FS 2016 je nakonfigurovaný na použití Azure MFA jako primární ověřování.

#### <a name="ad-fs-logging"></a>AD FS protokolování

Standardní služby AD FS 2016 protokolování v protokolu zabezpečení Windows a protokolu Správce služby AD FS obsahuje informace o žádosti o ověření a jejich úspěchu nebo neúspěchu. Data protokolu událostí v rámci těchto událostí označí, zda byla použita Azure MFA. Například může obsahovat ID události auditování služby FS AD 1200:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Obnovení a spravovat certifikáty

Na každém serveru služby AD FS na místním počítači My Store, bude certifikát podepsaný svým držitelem Azure MFA s názvem organizační jednotky = Microsoft AD FS Azure MFA, který obsahuje datum vypršení platnosti certifikátu. Zkontrolujte dobu platnosti tohoto certifikátu na každém serveru služby AD FS určit datum vypršení platnosti.

Pokud doba platnosti certifikátu je blížícím se koncem platnosti, [generování a ověřování nový certifikát na každém serveru služby AD FS MFA](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

Následující pokyny podrobně popisuje, jak spravovat certifikáty Azure MFA na serverech služby AD FS. Při konfiguraci služby AD FS pomocí Azure MFA, certifikáty vygenerované prostřednictvím `New-AdfsAzureMfaTenantCertificate` rutiny Powershellu jsou platné po dobu 2 let. Obnovit a nainstalujte obnovené certifikáty před vypršením platnosti vejčitého narušení služeb vícefaktorové ověřování.

## <a name="implement-your-plan"></a>Implementace plánu

Teď, když jste naplánovali vaše řešení, můžete implementovat podle kroků níže:

1. Splnění nezbytných předpokladů
   1. Nasazení [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) pro jakékoli hybridních scénářů
   1. Nasazení [Azure AD Application Proxy](../manage-apps/application-proxy.md) pro na všechny místní aplikace publikována pro přístup k cloudu
   1. Nasazení [NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) pro ověřování pomocí protokolu RADIUS
   1. Zajistěte, aby uživatelé přešli na podporované verze sady Microsoft Office pomocí povolené moderní ověřování
1. Konfigurace zvolené [metody ověřování](#choose-verification-options)
1. Definujte vaši [pojmenovaná umístění sítě](../conditional-access/location-condition.md#named-locations)
1. Vyberte skupiny zahájíte zavádět vícefaktorové ověřování.
1. Konfigurace vašeho [zásady podmíněného přístupu](#create-conditional-access-policy)
1. Konfigurace zásady registrace MFA
   1. [Kombinované vícefaktorové ověřování a samoobslužné resetování HESLA](howto-registration-mfa-sspr-combined.md)
   1. S [Identity Protection](../identity-protection/howto-mfa-policy.md)
1. Odeslat komunikaci uživatele a získat uživatelé museli zaregistrovat na [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Sledovat, kdo se zaregistruje](#identify-non-registered-users)

## <a name="manage-your-solution"></a>Správa vašeho řešení

Sestavy pro Azure MFA

Azure Multi-Factor Authentication poskytuje sestavy na webu Azure portal:

| Sestava | Location | Popis |
| --- | --- | --- |
| Využití a odhalování výstrahy | Azure AD > přihlášení | Poskytuje informace o celkové využití: uživatelský souhrn a podrobnosti o uživateli; také historie upozornění na podvod odeslaných během období zadán. |

## <a name="troubleshoot-mfa-issues"></a>Řešení potíží s MFA

Najít řešení běžných problémů s Azure MFA na [článku řešení potíží s Azure Multi-Factor Authentication](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) na webu Microsoft Support Center.

## <a name="next-steps"></a>Další postup

* [Co jsou metody ověřování?](concept-authentication-methods.md)
* [Povolit sblížené registraci pro resetování hesla pomocí samoobslužné služby Azure Multi-Factor Authentication a Azure AD](concept-registration-mfa-sspr-converged.md)
* Proč byla uživateli zobrazí výzva nebo nechcete zobrazit výzvu k provedení vícefaktorové ověřování? V části [sestavy přihlášení Azure AD v sestavách v dokumentu ověřování Azure Multi-Factor Authentication](howto-mfa-reporting.md#azure-ad-sign-ins-report).
