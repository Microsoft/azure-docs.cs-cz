---
title: Nasazení azure vícefaktorového ověřování – Azure Active Directory
description: Plánování nasazení vícefaktorového ověřování Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ae58482ced524958ffcdd6094ae57856d088eaf
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653959"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication-deployment"></a>Plánování cloudového nasazení Azure Multi-Factor Authentication

Lidé se připojují k organizačním zdrojům ve stále složitějších scénářích. Lidé se připojují z organizací vlastněných, osobních a veřejných zařízení v podnikové síti i mimo ni pomocí chytrých telefonů, tabletů, počítačů a přenosných počítačů, často na více platformách. V tomto neustále připojeném světě s více zařízeními a více platformami je zabezpečení uživatelských účtů důležitější než kdy předtím. Hesla, bez ohledu na jejich složitost, používaná na různých zařízeních, sítích a platformách již nejsou dostatečná k zajištění zabezpečení uživatelského účtu, zejména pokud uživatelé mají tendenci opakovaně používat hesla mezi účty. Sofistikované útoky phishingu a další sociální inženýrství mohou vést k tomu, že uživatelská jména a hesla budou zveřejněna a prodávána na tmavém webu.

[Azure Multi-Factor Authentication (MFA)](concept-mfa-howitworks.md) pomáhá zabezpečit přístup k datům a aplikacím. Poskytuje další vrstvu zabezpečení pomocí druhé formy ověřování. Organizace mohou použít [podmíněný přístup,](../conditional-access/overview.md) aby řešení vyhovovalo jejich specifickým potřebám.

## <a name="prerequisites"></a>Požadavky

Před zahájením nasazení Azure Vícefaktorové ověřování, existují nezbytné položky, které by měly být považovány za.

| Scénář | Požadavek |
| --- | --- |
| **Prostředí** identit pouze pro cloud s moderním ověřováním | **Žádné další požadované úkoly** |
| **Scénáře hybridní** identity | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) se nasazuje a identity uživatelů se synchronizují nebo federují s místní službou Active Directory Domain Services pomocí služby Azure Active Directory. |
| Místní starší aplikace publikované pro přístup ke cloudu | Azure AD [Proxy aplikace](../manage-apps/application-proxy.md) je nasazený. |
| Použití Azure MFA s ověřováním RADIUS | Je nasazen [server nps (Network Policy Server).](howto-mfa-nps-extension.md) |
| Uživatelé mají Microsoft Office 2010 nebo starší nebo Apple Mail pro iOS 11 nebo starší | Upgradujte na [Microsoft Office 2013 nebo novější](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) a Apple mail pro iOS 12 nebo novější. Podmíněný přístup není podporován staršími ověřovacími protokoly. |

## <a name="plan-user-rollout"></a>Plánování zavedení uživatele

Váš plán zavádění vícefaktorové informace by měl zahrnovat pilotní nasazení následované vlnami nasazení, které jsou v rámci kapacity podpory. Začněte s rozjezdem použitím zásad podmíněného přístupu na malou skupinu pilotních uživatelů. Po vyhodnocení vlivu na pilotní uživatele, použitého procesu a chování registrace můžete do zásad přidat další skupiny nebo přidat další uživatele do existujících skupin.

### <a name="user-communications"></a>Komunikace s uživateli

Je důležité informovat uživatele v rámci plánované komunikace o nadcházejících změnách, požadavcích na registraci Azure MFA a všech nezbytných uživatelských akcích. Doporučujeme, aby se komunikace vyvíjela ve shodě se zástupci z vaší organizace, jako jsou oddělení komunikace, řízení změn nebo oddělení lidských zdrojů.

Společnost Microsoft poskytuje [šablony komunikace](https://aka.ms/mfatemplates) a [dokumentaci pro koncové uživatele,](../user-help/security-info-setup-signin.md) které pomáhají při přípravě komunikace. Uživatelé se [https://myprofile.microsoft.com](https://myprofile.microsoft.com) mohou zaregistrovat přímo výběrem odkazů **Bezpečnostní údaje** na této stránce.

## <a name="deployment-considerations"></a>Aspekty nasazování

Azure Vícefaktorové ověřování se nasadí vynucením zásad s podmíněným přístupem. [Zásady podmíněného přístupu](../conditional-access/overview.md) mohou vyžadovat, aby uživatelé prováděli vícefaktorové ověřování, pokud jsou splněna určitá kritéria, například:

* Všichni uživatelé, konkrétní uživatel, člen skupiny nebo přiřazená role
* Přístup ke konkrétní cloudové aplikaci
* Platforma zařízení
* Stav zařízení
* Umístění v síti nebo geograficky umístěná IP adresa
* Klientské aplikace
* Riziko přihlášení (vyžaduje ochranu identity)
* Odpovídající zařízení
* Hybridní zařízení azure ad připojeno
* Schválená klientská aplikace

Pomocí přizpůsobitelných plakátů a šablon e-mailů v [návlacích vícefaktorového ověřování](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) můžete ve vaší organizaci zavést vícefaktorové ověřování.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Povolení vícefaktorového ověřování pomocí podmíněného přístupu

Zásady podmíněného přístupu vynucují registraci, která vyžaduje, aby neregistrovaní uživatelé dokončili registraci při prvním přihlášení, což je důležité bezpečnostní hledisko.

[Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md) přispívá jak zásady registrace pro a automatizované zásady detekce rizik a nápravu článku Azure Multi-Factor Ověřování. Zásady mohou být vytvořeny tak, aby vynucovaly změny hesla, pokud existuje hrozba ohrožení identity nebo vyžadují vícefaktorové ověřování, pokud je přihlášení považováno za rizikové [následujícími událostmi](../reports-monitoring/concept-risk-events.md):

* Uniklá pověření
* Přihlášení z anonymních IP adres
* Nemožná cesta do netypických míst
* Přihlášení z neznámých míst
* Přihlášení z nakažených zařízení
* Přihlášení z IP adres s podezřelými aktivitami

Některé detekce rizik zjištěné službou Azure Active Directory Identity Protection se vyskytují v reálném čase a některé vyžadují offline zpracování. Správci se mohou rozhodnout blokovat uživatele, kteří vykazují rizikové chování, a ručně napravit, vyžadovat změnu hesla nebo vyžadovat vícefaktorové ověřování jako součást svých zásad podmíněného přístupu.

## <a name="define-network-locations"></a>Definování síťových umístění

Doporučujeme organizacím, aby pomocí podmíněného přístupu definovaly svou síť pomocí [pojmenovaných umístění](../conditional-access/location-condition.md#named-locations). Pokud vaše organizace používá ochranu identity, zvažte použití zásad založených na rizicích namísto pojmenovaných umístění.

### <a name="configuring-a-named-location"></a>Konfigurace pojmenovaného umístění

1. Otevření **Služby Azure Active Directory** na webu Azure Portal
2. Vybrat **zabezpečení**
3. V části **Správa**zvolte **Pojmenované umístění.**
4. Vybrat **nové umístění**
5. V poli **Název** zadejte smysluplný název.
6. Vyberte, zda definujete umístění pomocí *rozsahů IP adres* nebo *oblastí/oblastí.*
   1. Pokud *používáte rozsahy IP*
      1. Rozhodněte *se,* zda chcete označit jako důvěryhodné umístění . Přihlášení z důvěryhodného umístění snižuje riziko při přihlášení uživatele. Toto umístění označte jako důvěryhodné pouze v případě, že víte, že zadané rozsahy IP adres jsou ve vaší organizaci zavedené a důvěryhodné.
      2. Určení rozsahů IP adres
   2. Pokud *používáte země/oblasti*
      1. Rozbalte rozevírací nabídku a vyberte země nebo oblasti, které chcete pro toto pojmenované umístění definovat.
      2. Rozhodněte se, zda chcete *zahrnout neznámé oblasti*. Neznámé oblasti jsou IP adresy, které nelze mapovat na zemi nebo oblast.
7. Vybrat **vytvořit**

## <a name="plan-authentication-methods"></a>Plánování metod ověřování

Správci mohou zvolit [metody ověřování,](../authentication/concept-authentication-methods.md) které chtějí zpřístupnit uživatelům. Je důležité povolit více než jednu metodu ověřování, aby uživatelé měli k dispozici metodu zálohování v případě, že jejich primární metoda není k dispozici. Pro správce jsou k dispozici následující metody:

### <a name="notification-through-mobile-app"></a>Oznámení prostřednictvím mobilní aplikace

Nabízené oznámení se odešle do aplikace Microsoft Authenticator na vašem mobilním zařízení. Uživatel zobrazí oznámení a vybere **schválit** k dokončení ověření. Nabízená oznámení prostřednictvím mobilní aplikace poskytují uživatelům nejméně rušivou možnost. Jsou také nejspolehlivější a nejbezpečnější možností, protože používají datové připojení spíše než telefonní subsystém.

> [!NOTE]
> Pokud má vaše organizace zaměstnance, kteří pracují v Číně nebo cestují do Číny, metoda **Oznámení prostřednictvím mobilní aplikace** na zařízeních se systémem **Android** v této zemi nefunguje. Pro tyto uživatele by měly být k dispozici alternativní metody.

### <a name="verification-code-from-mobile-app"></a>Ověřovací kód z mobilní aplikace

Mobilní aplikace, jako je aplikace Microsoft Authenticator, generuje každých 30 sekund nový ověřovací kód OATH. Uživatel zadá ověřovací kód do přihlašovacího rozhraní. Možnost mobilní aplikace lze použít bez ohledu na to, zda má telefon datový nebo mobilní signál.

### <a name="call-to-phone"></a>Volání na telefon

Uživateli je umístěn automatický hlasový hovor. Uživatel přijme hovor a **#** stisknutím klávesnice telefonu schválí jejich ověření. Volání do telefonu je skvělá metoda zálohování pro oznámení nebo ověřovací kód z mobilní aplikace.

### <a name="text-message-to-phone"></a>Textová zpráva do telefonu

Uživateli je odeslána textová zpráva obsahující ověřovací kód, uživatel je vyzván k zadání ověřovacího kódu do přihlašovacího rozhraní.

### <a name="choose-verification-options"></a>Zvolte možnosti ověření

1. Přejděte na **Azure Active Directory**, **Users**, **Multi-Factor Authentication**.

   ![Přístup k portálu vícefaktorového ověřování z okna Uživatelé Azure AD na webu Azure Portal](media/howto-mfa-getstarted/users-mfa.png)

1. Na nové kartě, která se otevře, přejděte k **nastavení služby**.
1. V **části možnosti ověření**zaškrtněte všechny políček pro metody, které jsou k dispozici uživatelům.

   ![Konfigurace metod ověřování na kartě Nastavení služby Vícefaktorové ověřování](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Klikněte na **Uložit**.
1. Zavřete kartu **Nastavení služby.**

## <a name="plan-registration-policy"></a>Zásady registrace plánu

Správci musí určit, jak budou uživatelé registrovat své metody. Organizace by měly [povolit nové kombinované registrační prostředí](howto-registration-mfa-sspr-combined.md) pro Azure MFA a samoobslužné resetování hesla (SSPR). SSPR umožňuje uživatelům resetovat své heslo bezpečným způsobem pomocí stejných metod, které používají pro vícefaktorové ověřování. Doporučujeme tuto kombinovanou registraci, která je v současné době ve verzi Public Preview, protože je to skvělá zkušenost pro uživatele s možností registrace jednou pro obě služby. Povolení stejných metod pro samoobslužné právo a Azure MFA umožní uživatelům zaregistrovat používat obě funkce.

### <a name="registration-with-identity-protection"></a>Registrace s ochranou identity

Pokud vaše organizace používá Azure Active Directory Identity Protection, [nakonfigurujte zásady registrace vícefaktorové registrace,](../identity-protection/howto-mfa-policy.md) aby se uživatelé zaregistrovali při příštím interaktivním přihlášení.

### <a name="registration-without-identity-protection"></a>Registrace bez ochrany identity

Pokud vaše organizace nemá licence, které umožňují ochranu identity, uživatelé jsou vyzváni k registraci při příštím získání vícefaktorové informace při přihlášení. Uživatelé nemusí být registrováni pro vícefaktorové finanční pomocí, pokud nepoužívají aplikace chráněné vícefaktorovým faktorem. Je důležité získat všechny uživatele registrované tak, aby chybní aktéři nemohou uhodnout heslo uživatele a zaregistrovat se k vícefaktorové muča, které jejich jménem, účinně převzetí kontroly nad účtem.

#### <a name="enforcing-registration"></a>Vynucení registrace

Pomocí následujících kroků může zásada podmíněného přístupu přinutit uživatele k registraci pro vícefaktorové ověřování

1. Vytvořte skupinu, přidejte všechny uživatele, kteří nejsou aktuálně registrováni.
2. Pomocí podmíněného přístupu vynucujte vícefaktorové ověřování pro tuto skupinu pro přístup ke všem prostředkům.
3. Pravidelně přehodnocujte členství ve skupině a odeberte uživatele, kteří se zaregistrovali ze skupiny.

Registrované a neregistrované uživatele Azure MFA můžete identifikovat pomocí příkazů prostředí PowerShell, které jsou závislé na [modulu Prostředí MSOnline PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).

#### <a name="identify-registered-users"></a>Identifikace registrovaných uživatelů

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Identifikace neregistrovaných uživatelů

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Převod uživatelů z vícefaktorové ověřování pro jednotlivé uživatele na vícefaktorové ověřování založené na podmíněném přístupu

Pokud vaši uživatelé byli povoleni pomocí vícefaktorového ověřování Azure pro jednotlivé uživatele, může vám následující Prostředí PowerShell pomoci při převodu na azure multifaktorové ověřování založené na podmíněném přístupu.

Spusťte toto prostředí PowerShell v okně ise nebo jej uložte jako . PS1 pro spuštění místně.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Nedávno jsme změnili chování a skript prostředí PowerShell výše odpovídajícím způsobem. Dříve skript uložen mimo metody MFA, zakázal vícefaktorové financování a obnovil metody. To již není nutné nyní, že výchozí chování pro zakázat nevymaže metody.

## <a name="plan-conditional-access-policies"></a>Plánování zásad podmíněného přístupu

Chcete-li naplánovat strategii zásad podmíněného přístupu, která určí, kdy jsou vyžadovány vícefaktorové informace a další ovládací prvky, přečtěte si informace o [tom, co je podmíněný přístup ve službě Azure Active Directory?](../conditional-access/overview.md).

Je důležité, abyste zabránili nechtěnému uzamčení klienta Azure AD. Dopad tohoto neúmyslného nedostatku přístupu pro správu můžete zmírnit [vytvořením dvou nebo více účtů pro nouzový přístup ve vašem tenantovi](../users-groups-roles/directory-emergency-access.md) a jejich vyloučením ze zásad podmíněného přístupu.

### <a name="create-conditional-access-policy"></a>Vytvořit zásadu podmíněného přístupu

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu globálního správce.
1. Přejděte na azure **active directory** > **zabezpečení** > podmíněný**přístup**.
1. Vyberte **nové zásady**.
   ![Vytvoření zásad podmíněného přístupu pro povolení vícefaktorové informace pro uživatele portálu Azure v pilotní skupině](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. Zadejte smysluplný název zásad.
1. V části **Uživatelé a skupiny**:
   * Na kartě **Zahrnout** vyberte přepínací tlačítko **Všichni uživatelé.**
   * Na kartě **Vyloučit** zaškrtněte políčko **Uživatelé a skupiny** a zvolte účty pro nouzový přístup.
   * Klikněte na **Done** (Hotovo).
1. V části **Cloud apps**vyberte přepínací tlačítko **Všechny cloudové aplikace.**
   * VOLITELNĚ: Na kartě **Vyloučit** zvolte cloudové aplikace, pro které vaše organizace nevyžaduje vícefaktorové zabezpečení.
   * Klikněte na **Done** (Hotovo).
1. V **části Podmínky:**
   * Volitelně: Pokud jste povolili Azure Identity Protection, můžete vyhodnotit riziko přihlášení jako součást zásady.
   * NEPOVINNĚ: Pokud jste nakonfigurovali důvěryhodná umístění nebo pojmenovaná umístění, můžete určit, zda chcete tato umístění zahrnout ze zásady nebo je vyloučit.
1. V části **Grant**zkontrolujte, zda je zaškrtnuté přepínací tlačítko **Udělit přístup.**
    * Zaškrtněte políčko **Vyžadovat vícefaktorové ověřování**.
    * Klepněte na **tlačítko Vybrat**.
1. Přeskočte část **Relace.**
1. Nastavte přepínač **Povolit zásady** na **Zapnuto**.
1. Klikněte na **Vytvořit**.

## <a name="plan-integration-with-on-premises-systems"></a>Plánování integrace s místními systémy

Některé starší a místní aplikace, které se neověřují přímo proti službě Azure AD, vyžadují další kroky k použití vícefaktorové ověřování, včetně:

* Starší místní aplikace, které budou muset používat proxy server aplikace.
* Místní aplikace RADIUS, které budou muset používat adaptér MFA se serverem NPS.
* Místní aplikace služby AD FS, které budou muset používat adaptér MFA se službou AD FS 2016 nebo novější.

Aplikace, které se ověřují přímo pomocí Azure AD a mají moderní ověřování (WS-Fed, SAML, OAuth, OpenID Connect) můžete přímo využívat zásady podmíněného přístupu.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Použití Azure MFA s proxy aplikací Azure AD

Aplikace, které se nacházejí v místním prostředí, se dají publikovat do vašeho klienta Azure AD prostřednictvím [proxy aplikací Azure AD](../manage-apps/application-proxy.md) a můžou využít azure multifaktorové ověřování, pokud jsou nakonfigurované pro použití předběžného ověřování Azure AD.

Tyto aplikace podléhají zásadám podmíněného přístupu, které vynucují azure multifaktorové ověřování, stejně jako všechny ostatní aplikace integrované s Azure AD.

Podobně pokud azure vícefaktorové ověřování je vynuceno pro všechny přihlášení uživatelů, místní aplikace publikované pomocí proxy aplikací Azure AD budou chráněny.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>Integrace azure vícefaktorového ověřování se serverem síťových zásad

Rozšíření servernps (Network Policy Server) pro Azure MFA přidává cloudové funkce MFA do infrastruktury ověřování pomocí stávajících serverů. Pomocí rozšíření NPS můžete do stávajícího toku ověřování přidat telefonní hovor, textovou zprávu nebo ověření telefonní aplikace. Tato integrace má následující omezení:

* S protokolem CHAPv2 jsou podporována pouze ověřovací aplikace nabízená oznámení a hlasové volání.
* Zásady podmíněného přístupu nelze použít.

Rozšíření nps funguje jako adaptér mezi RADIUS a cloudové Azure MFA poskytnout druhý faktor ověřování k ochraně [VPN](howto-mfa-nps-extension-vpn.md), [připojení brány vzdálené plochy](howto-mfa-nps-extension-rdg.md)nebo jiné aplikace podporující RADIUS. Uživatelé, kteří se zaregistrují pro Azure MFA v tomto prostředí budou napadeny pro všechny pokusy o ověření, nedostatek zásad podmíněného přístupu znamená, že je vždy vyžadováno vícefaktorové ověřování.

#### <a name="implementing-your-nps-server"></a>Implementace serveru NPS

Pokud máte instanci NPS nasazenou a už se používá, [odkazujte na integraci stávající infrastruktury NPS s vícefaktorovým ověřováním Azure](howto-mfa-nps-extension.md). Pokud nastavujete server NPS poprvé, naleznete pokyny na [serveru NPS (Network Policy Server).](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) Pokyny pro řešení potíží najdete v článku [Vyřešit chybové zprávy z rozšíření NPS pro azure multi-factor authentication](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Příprava serveru NPS pro uživatele, kteří nejsou zaregistrovaní pro vícefaktorové hodnocení

Zvolte, co se stane, když se uživatelé, kteří nejsou zaregistrovaní pomocí vícefaktorové ověřování, pokusí ověřit. Pomocí nastavení `REQUIRE_USER_MATCH` registru v `HKLM\Software\Microsoft\AzureMFA` cestě registru můžete řídit chování funkcí. Toto nastavení má jednu možnost konfigurace.

| Klíč | Hodnota | Výchozí |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | PRAVDA / NEPRAVDIVÉ | Není nastaveno (odpovídá hodnotě TRUE) |

Účelem tohoto nastavení je určit, co dělat, když uživatel není zapsán pro vícefaktorové ověřování. Účinky změny tohoto nastavení jsou uvedeny v následující tabulce.

| Nastavení | Stav vícefaktorového přístupu k uživateli | Účinek |
| --- | --- | --- |
| Klíč neexistuje. | Není zapsáno | Výzva vícefaktorové a finanční hod. |
| Hodnota nastavená na hodnotu True / není nastavena | Není zapsáno | Výzva vícefaktorové a finanční hod. |
| Klíč nastaven ý na hodnotu False | Není zapsáno | Ověřování bez vícefaktorové ověřování |
| Klíč nastavený na hodnotu False nebo True | Zaregistrované | Musí se ověřit pomocí vícefaktorové ověřování. |

### <a name="integrate-with-active-directory-federation-services"></a>Integrace se službou AD FS

Pokud je vaše organizace federovaná pomocí Azure AD, můžete pomocí [Azure Multi-Factor Authentication zabezpečit prostředky služby AD FS](multi-factor-authentication-get-started-adfs.md), a to jak místně, tak v cloudu. Azure MFA umožňuje snížit hesla a poskytnout bezpečnější způsob ověřování. Počínaje Windows Serverem 2016 teď můžete nakonfigurovat Azure MFA pro primární ověřování.

Na rozdíl od služby AD FS v systému Windows Server 2012 R2 se adaptér Azure MFA služby AD FS 2016 integruje přímo se službou Azure AD a nevyžaduje místní server Azure MFA. Adaptér Azure MFA je integrovaný do Windows Serveru 2016 a není potřeba další instalace.

Při použití Azure MFA s AD FS 2016 a cílové aplikace podléhá zásady podmíněného přístupu, existují další důležité informace:

* Podmíněný přístup je k dispozici, když je aplikace předávající stranou Azure AD, federovaná se službou AD FS 2016 nebo novější.
* Podmíněný přístup není k dispozici, pokud je aplikace předávající stranou služby AD FS 2016 nebo AD FS 2019 a spravuje se nebo federuje se službou AD FS 2016 nebo AD FS 2019.
* Podmíněný přístup taky není k dispozici, když je služba AD FS 2016 nebo AD FS 2019 nakonfigurovaná tak, aby jako primární metodu ověřování používala Azure MFA.

#### <a name="ad-fs-logging"></a>Protokolování služby AD FS

Standardní protokolování služby AD FS 2016 a 2019 v protokolu zabezpečení systému Windows i v protokolu správce služby AD FS obsahuje informace o požadavcích na ověření a jejich úspěchu či neúspěchu. Data protokolu událostí v rámci těchto událostí označují, zda byl použit Azure MFA. ID události auditování ad FS ve služebnach y 1200 může například obsahovat:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Obnovení a správa certifikátů

Na každém serveru služby AD FS v místním počítači My Store bude certifikát Azure MFA podepsaný svým držitelem s názvem OU=Microsoft AD FS Azure MFA, který obsahuje datum vypršení platnosti certifikátu. Zkontrolujte dobu platnosti tohoto certifikátu na každém serveru služby AD FS a určete datum vypršení platnosti.

Pokud se doba platnosti certifikátů blíží k vypršení platnosti, [vygenerujte a ověřte nový certifikát MFA na každém serveru služby AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

Následující pokyny podrobně popisuje, jak spravovat certifikáty Azure MFA na serverech služby AD FS. Při konfiguraci služby AD FS s Azure MFA jsou certifikáty generované prostřednictvím rutiny `New-AdfsAzureMfaTenantCertificate` prostředí PowerShell platné po dobu 2 let. Obnovte a nainstalujte obnovené certifikáty před vypršením platnosti na vyprázdnění přerušení služby MFA.

## <a name="implement-your-plan"></a>Implementace plánu

Nyní, když jste naplánovali řešení, můžete implementovat pomocí následujících kroků:

1. Splnění všech nezbytných předpokladů
   1. Nasazení [služby Azure AD Connect](../hybrid/whatis-hybrid-identity.md) pro všechny hybridní scénáře
   1. Nasazení [proxy aplikací Azure AD](../manage-apps/application-proxy.md) pro všechny místní aplikace publikované pro přístup ke cloudu
   1. Nasazení [serveru NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) pro jakékoli ověřování RADIUS
   1. Zajistěte, aby uživatelé upgradovali na podporované verze sady Microsoft Office s povoleným moderním ověřováním
1. Konfigurace zvolených [metod ověřování](#choose-verification-options)
1. Definování [pojmenovaných síťových umístění](../conditional-access/location-condition.md#named-locations)
1. Vyberte skupiny, které chcete začít zavádět vícefaktorové finanční příkazy.
1. Konfigurace [zásad podmíněného přístupu](#create-conditional-access-policy)
1. Konfigurace zásad registrace vícefaktorové registrace
   1. [Kombinované vícefaktorové finanční a finanční spojení a sspr](howto-registration-mfa-sspr-combined.md)
   1. S [ochranou identity](../identity-protection/howto-mfa-policy.md)
1. Posílejte komunikaci s uživateli a přimějte uživatele k registraci na[https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Sledování toho, kdo je zaregistrovaný](#identify-non-registered-users)

> [!TIP]
> Uživatelé vládního cloudu se mohou zaregistrovat na[https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>Správa řešení

Sestavy pro Azure MFA

Azure Multi-Factor Authentication poskytuje sestavy prostřednictvím portálu Azure:

| Sestava | Umístění | Popis |
| --- | --- | --- |
| Upozornění na používání a podvody | Azure AD > přihlášení | Obsahuje informace o celkovém využití, souhrnu uživatelů a podrobnostech o uživateli. a také historii záznamů o podvodech předložených během uvedeného časového období. |

## <a name="troubleshoot-mfa-issues"></a>Poradce při potížích s mfa

Najděte řešení běžných problémů s Azure MFA v [článku Řešení potíží s azure multi-factor authentication](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) v Centru podpory Microsoftu.

## <a name="next-steps"></a>Další kroky

* [Co jsou metody ověřování?](concept-authentication-methods.md)
* [Povolení konvergované registrace pro azure vícefaktorové ověřování a samoobslužné resetování hesla Azure AD](concept-registration-mfa-sspr-converged.md)
* Proč byl uživatel vyzván nebo nebyl vyzván k provedení vícefaktorového ověřování? Podívejte se na část [sestavy přihlášení Azure AD v dokumentu Sestavy v Azure vícefaktorové ověřování](howto-mfa-reporting.md#azure-ad-sign-ins-report).
