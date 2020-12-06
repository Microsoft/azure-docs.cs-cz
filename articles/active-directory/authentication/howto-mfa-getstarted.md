---
title: Požadavky na nasazení pro Azure AD Multi-Factor Authentication
description: Přečtěte si o faktorech nasazení a strategii pro úspěšnou implementaci služby Azure AD Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 978f404aa9b99819460e46ea89df19d27431b8b8
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743135"
---
# <a name="plan-an-azure-ad-multi-factor-authentication-deployment"></a>Plánování nasazení Multi-Factor Authentication služby Azure AD

Uživatelé se připojují k prostředkům organizace ve stále složitějších scénářích. Uživatelé se k podnikové síti připojují ze svých podnikových, osobních a veřejných zařízení pomocí inteligentních telefonů, tabletů, počítačů a notebooků, často na různých platformách. V tomto případě se trvale připojuje k více zařízením a na světě více platforem je zabezpečení uživatelských účtů důležitější než kdykoli dřív. Hesla, bez ohledu na jejich složitost, používaná na zařízeních, sítích a platformách již nejsou dostačující k zajištění zabezpečení uživatelského účtu, zejména v případě, že uživatelé mají v rámci účtů často opakovaně používat hesla. Sofistikované útoky phishing a další sociální inženýrství můžou vést k tomu, že se budou uživatelská jména a hesla publikovat a prodávat v tmavém webu.

[Služba Azure AD Multi-Factor Authentication (MFA)](concept-mfa-howitworks.md) pomáhá chránit přístup k datům a aplikacím. Poskytuje další úroveň zabezpečení s použitím druhé formy ověřování. Organizace můžou pomocí [podmíněného přístupu](../conditional-access/overview.md) zajistit, aby řešení vyhovovalo konkrétním potřebám.

V této příručce pro nasazení se dozvíte, jak naplánovat a otestovat Multi-Factor Authentication služby Azure AD.

K rychlému zobrazení Multi-Factor Authentication Azure AD v akci a pak se vraťte k pochopení dalších hledisek nasazení:

> [!div class="nextstepaction"]
> [Povolení vícefaktorového ověřování Azure AD](tutorial-enable-azure-mfa.md)

## <a name="prerequisites"></a>Předpoklady

Před zahájením nasazení Multi-Factor Authentication služby Azure AD existují nezbytné položky, které byste měli vzít v úvahu.

| Scénář | Požadavek |
| --- | --- |
| **Pouze cloudové** prostředí identity s moderním ověřováním | **Žádné další úlohy požadavků** |
| **Hybridní** scénáře identity | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) je nasazený a identity uživatelů se synchronizují nebo federované s použitím místní Active Directory Domain Services Azure Active Directory. |
| Místní starší verze aplikací publikované pro cloudový přístup | [Proxy aplikace](../manage-apps/application-proxy.md) služby Azure AD je nasazená. |
| Použití Azure AD MFA s ověřováním RADIUS | Je nasazen [Server NPS (Network Policy Server)](howto-mfa-nps-extension.md) . |
| Uživatelé mají systém Microsoft Office 2010 nebo starší nebo Apple Mail pro iOS 11 nebo starší. | Upgradujte na [systém Microsoft Office 2013 nebo novější](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) a Apple Mail pro iOS 12 nebo novější. Protokoly pro ověřování starší verze nepodporují podmíněný přístup. |

## <a name="plan-user-rollout"></a>Plánování zavedení uživatelů

Plán zavedení MFA by měl zahrnovat pilotní nasazení, po kterém následují vlny nasazení, které jsou v rámci vaší kapacity podpory. Zahajte zavedení tak, že použijete zásady podmíněného přístupu na malou skupinu uživatelů pilotního nasazení. Po vyhodnocení účinku u pilotních uživatelů, použitých procesů a chování registrace můžete přidat do této zásady další skupiny nebo přidat další uživatele do stávajících skupin.

### <a name="user-communications"></a>Komunikace uživatelů

Je důležité informovat uživatele, v plánované komunikaci, o nadcházejících změnách, požadavcích na registraci Azure AD MFA a všech nezbytných akcích uživatele. Doporučujeme, abyste komunikaci vyvinuli společně se zástupci z vaší organizace, jako je komunikace, Správa změn nebo oddělení lidských zdrojů.

Společnost Microsoft poskytuje [komunikační šablony](https://aka.ms/mfatemplates) a [dokumentaci pro koncové uživatele](../user-help/security-info-setup-signin.md) , které vám pomůžou při vypracovávání komunikace. Můžete odeslat uživatele do, [https://myprofile.microsoft.com](https://myprofile.microsoft.com) aby se zaregistrovali přímo, a to tak, že na této stránce vyberete odkazy na **informace o zabezpečení** .

## <a name="deployment-considerations"></a>Aspekty nasazování

Multi-Factor Authentication Azure AD se nasazuje vynucenými zásadami s podmíněným přístupem. Zásada podmíněného přístupu může vyžadovat, aby uživatelé při splnění určitých kritérií prováděli vícefaktorové ověřování, například:

* Všichni uživatelé, konkrétní uživatel, člen skupiny nebo přiřazená role
* Konkrétní cloudová aplikace, ke které se přistupoval
* Platforma zařízení
* Stav zařízení
* Síťové umístění nebo IP adresa geograficky umístěné
* Klientské aplikace
* Riziko přihlášení (vyžaduje ochranu identity)
* Odpovídající zařízení
* Zařízení připojené k hybridní službě Azure AD
* Schválená klientská aplikace

Používejte přizpůsobitelné plakáty a e-mailové šablony v [materiálech pro Multi-Factor Authentication](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) k zavedení služby Multi-Factor Authentication do vaší organizace.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Povolení Multi-Factor Authentication s podmíněným přístupem

Zásady podmíněného přístupu vynutily registraci, což vyžaduje, aby registraci při prvním přihlášení vyžadovali Neregistrovaní uživatelé, což je důležité bezpečnostní hledisko.

[Azure AD Identity Protection](../identity-protection/howto-identity-protection-configure-risk-policies.md) do Multi-Factor Authenticationho scénáře služby Azure AD přispěje jak zásady registrace, tak i automatizované zásady detekce rizik a nápravy. Zásady je možné vytvořit pro vynucení změny hesla, pokud dojde k ohrožení zabezpečení identity nebo pokud je přihlášení považováno za rizikové pomocí následujících [událostí](../identity-protection/overview-identity-protection.md):

* Uniklé přihlašovací údaje
* Přihlášení z anonymních IP adres
* Nemožná cesta do netypických míst
* Přihlášení z neznámých míst
* Přihlášení z nakažených zařízení
* Přihlášení z IP adres s podezřelými aktivitami

K některým detekcím rizik zjištěným Azure Active Directory Identity Protection dochází v reálném čase a některé vyžadují offline zpracování. Správci se můžou rozhodnout zablokovat uživatelům, kteří mají rizikové chování a opravovat je ručně, vyžadovat změnu hesla nebo vyžadují službu Multi-Factor Authentication v rámci zásad podmíněného přístupu.

## <a name="define-network-locations"></a>Definovat síťová umístění

Doporučujeme, aby organizace používaly podmíněný přístup k definování sítě pomocí [pojmenovaných umístění](../conditional-access/location-condition.md#named-locations). Pokud vaše organizace používá Identity Protection, zvažte použití zásad na základě rizik namísto pojmenovaných umístění.

### <a name="configuring-a-named-location"></a>Konfigurace pojmenovaného umístění

1. Otevřít **Azure Active Directory** v Azure Portal
2. Vybrat **zabezpečení**
3. V části **Spravovat** vyberte **pojmenovaná umístění** .
4. Vybrat **nové umístění**
5. Do pole **název** zadejte smysluplný název.
6. Vyberte, jestli definujete umístění pomocí *rozsahů IP adres* nebo *zemí nebo oblastí* .
   1. Pokud používáte *rozsahy IP adres*
      1. Rozhodněte, jestli se má *Označit jako důvěryhodné umístění*. Přihlášení z důvěryhodného umístění snižuje riziko při přihlášení uživatele. Tuto složku označte jako důvěryhodnou jenom v případě, že víte, že zadané rozsahy IP adres jsou ve vaší organizaci zřízené a důvěryhodné.
      2. Zadejte rozsahy IP adres.
   2. Pokud používáte *země nebo oblasti*
      1. Rozbalte rozevírací nabídku a vyberte země nebo oblasti, které chcete definovat pro toto pojmenované umístění.
      2. Rozhodněte, jestli se mají *Zahrnout neznámé oblasti*. Neznámé oblasti jsou IP adresy, které nejdou namapovat na zemi nebo oblast.
7. Vyberte **Vytvořit**.

## <a name="plan-authentication-methods"></a>Plánování metod ověřování

Správci mohou zvolit [metody ověřování](../authentication/concept-authentication-methods.md) , které mají být uživatelům k dispozici. Je důležité, abyste povolili více než jednu metodu ověřování, takže uživatelé mají k dispozici metodu zálohování pro případ, že jejich primární metoda není k dispozici. Pro povolení správců jsou k dispozici následující metody:

> [!TIP]
> Microsoft doporučuje používat Microsoft Authenticator (mobilní aplikace) jako primární metodu pro Azure AD Multi-Factor Authentication pro bezpečnější a vylepšené uživatelské prostředí. Aplikace Microsoft Authenticator také [splňuje](https://azure.microsoft.com/resources/microsoft-nist/) Národní ústav standardů a úrovně záruky na technologie ověřovatelů. 

### <a name="notification-through-mobile-app"></a>Oznámení prostřednictvím mobilní aplikace

Do aplikace Microsoft Authenticator na vašem mobilním zařízení se pošle nabízené oznámení. Uživatel zobrazí oznámení a vybere **schválit** k dokončení ověření. Nabízená oznámení prostřednictvím mobilní aplikace poskytují uživatelům nejméně rušivou možnost. Jsou také nejspolehlivější a zabezpečený způsob, protože místo telefonního subsystému používá datové připojení.

> [!NOTE]
> Pokud má vaše organizace zaměstnanci pracující v nebo na cestách na Čínu, **oznámení prostřednictvím metody mobilní aplikace** v **zařízeních s Androidem** nefunguje v dané zemi nebo oblasti. Pro tyto uživatele by měly být k dispozici alternativní metody.

### <a name="verification-code-from-mobile-app"></a>Ověřovací kód z mobilní aplikace

Mobilní aplikace, jako je Microsoft Authenticator aplikace, vygeneruje nový ověřovací kód OATH každých 30 sekund. Uživatel zadá ověřovací kód do přihlašovacího rozhraní. Možnost mobilní aplikace se dá použít bez ohledu na to, jestli telefon obsahuje data nebo mobilní signál.

### <a name="call-to-phone"></a>Zavolat na telefon

Automatické volání hlasu je umístěno uživateli. Uživatel přijme volání a stiskne **#** na klávesnici telefonu a schválí jejich ověření. Volání na telefon je skvělou metodou zálohování pro oznámení nebo ověřovací kód z mobilní aplikace.

### <a name="text-message-to-phone"></a>Textová zpráva na telefon

Uživateli se pošle textová zpráva obsahující ověřovací kód. uživateli se zobrazí výzva k zadání ověřovacího kódu do přihlašovacího rozhraní.

### <a name="choose-verification-options"></a>Zvolit možnosti ověřování

1. Přejděte na **Azure Active Directory**, **uživatele** **Multi-Factor Authentication**.

   ![Přístup k portálu Multi-Factor Authentication z okna uživatelé Azure AD v Azure Portal](media/howto-mfa-getstarted/users-mfa.png)

1. Na nové kartě, která se otevře, přejděte na **nastavení služby**.
1. V části **Možnosti ověřování** zaškrtněte všechna políčka pro metody dostupné pro uživatele.

   ![Konfigurace metod ověřování na kartě nastavení služby Multi-Factor Authentication](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Klikněte na **Uložit**.
1. Zavřete kartu **nastavení služby** .

## <a name="plan-registration-policy"></a>Plánování zásad registrace

Správci musí určit, jak budou uživatelé registrovat své metody. Organizace by měly [Povolit nové kombinované registrační prostředí](howto-registration-mfa-sspr-combined.md) pro Azure AD MFA a Samoobslužné resetování hesla (SSPR). SSPR umožňuje uživatelům obnovit heslo zabezpečeným způsobem pomocí stejných metod, které používají pro službu Multi-Factor Authentication. Tuto kombinovanou registraci doporučujeme, protože se jedná o Skvělé prostředí pro uživatele a možnost Registrovat se jednou pro obě služby. Povolení stejných metod pro SSPR a Azure AD MFA umožní uživatelům zaregistrovat se k používání obou funkcí.

### <a name="registration-with-identity-protection"></a>Registrace pomocí Identity Protection

Pokud vaše organizace používá Azure Active Directory Identity Protection, [nakonfigurujte zásady registrace MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) , aby vyzvat uživatele k registraci při příštím přihlášení.

### <a name="registration-without-identity-protection"></a>Registrace bez ochrany identity

Pokud vaše organizace nemá licence, které umožňují ochranu identity, zobrazí se uživatelům výzva k registraci při příštím přihlášení ke službě MFA. Uživatelé nemusí být registrováni pro vícefaktorové ověřování, pokud nepoužívají aplikace chráněné s MFA. Je důležité, abyste získali všechny registrované uživatele, aby Neplatní aktéri nemohly uhodnout heslo uživatele a zaregistrovali je pro MFA jménem a účinně přebírají kontrolu nad účtem.

#### <a name="enforcing-registration"></a>Vynucování registrace

Pomocí následujících kroků může zásada podmíněného přístupu vynutit, aby se uživatelé zaregistrovali Multi-Factor Authentication

1. Vytvořte skupinu a přidejte všechny uživatele, kteří nejsou aktuálně zaregistrovaní.
2. Pomocí podmíněného přístupu vynuťte službu Multi-Factor Authentication pro tuto skupinu pro přístup ke všem prostředkům.
3. Pravidelně znovu vyhodnoťte členství ve skupině a odeberte uživatele, kteří se zaregistrovali ze skupiny.

Můžete identifikovat registrované a neregistrované uživatele Azure AD MFA pomocí příkazů PowerShellu, které se spoléhají na [modul MSOnline PowerShellu](/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).

#### <a name="identify-registered-users"></a>Identifikace registrovaných uživatelů

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Identifikace neregistrovaných uživatelů

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Převod uživatelů z ověřování MFA na uživatele na vícefaktorové ověřování na základě podmíněného přístupu

Pokud byly vaši uživatelé povoleni pomocí služby Azure AD, která je povolená pro jednotlivé uživatele, a vynutila Multi-Factor Authentication vám následující PowerShell může pomoct při převodu na Multi-Factor Authentication Azure AD založené na podmíněném přístupu.

Spusťte tento PowerShell v okně ISE nebo uložte jako `.PS1` soubor pro místní spuštění.

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
> V nedávné době jsme změnili chování a skript PowerShellu. Dříve byl skript uložen mimo metody MFA, zakázal MFA a obnovil metody. Už to není nutné, když výchozí chování pro Disable nevymaže tyto metody.

## <a name="plan-conditional-access-policies"></a>Plánování zásad podmíněného přístupu

Pokud chcete naplánovat strategii zásad podmíněného přístupu, která určí, kdy se vyžadují MFA a jiné ovládací prvky, přečtěte si téma [společné zásady podmíněného přístupu](../conditional-access/concept-conditional-access-policy-common.md).

Je důležité, abyste zabránili neúmyslnému uzamčení vašeho tenanta Azure AD. Dopad tohoto neúmyslného přístupu správce můžete zmírnit tím, že [ve svém tenantovi vytvoříte dva nebo víc účtů pro nouzový přístup](../roles/security-emergency-access.md) a vyloučíte je ze zásad podmíněného přístupu.

### <a name="create-conditional-access-policy"></a>Vytvořit zásady podmíněného přístupu

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce.
1. Vyhledejte **Azure Active Directory**  >  **Security**  >  **podmíněný přístup** zabezpečení.
1. Vyberte **nové zásady**.
   ![Vytvoření zásady podmíněného přístupu pro povolení MFA pro uživatele Azure Portal v pilotní skupině](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. Zadejte smysluplný název pro zásady.
1. V části **Uživatelé a skupiny**:
   * Na kartě **Zahrnout** klikněte na přepínač **Všichni uživatelé** .
   * Na kartě **vyloučit** zaškrtněte políčko pro **uživatele a skupiny** a vyberte účty pro nouzový přístup.
   * Klikněte na **Hotovo**.
1. V části **cloudové aplikace** vyberte přepínač **všechny cloudové aplikace** .
   * Volitelně: na kartě **vyloučit** vyberte cloudové aplikace, pro které vaše organizace nevyžaduje MFA.
   * Klikněte na **Hotovo**.
1. Oddíl **podmínky** :
   * Volitelně: Pokud jste povolili Azure Identity Protection, můžete v rámci této zásady vyhodnotit riziko přihlášení.
   * Volitelně: Pokud jste nakonfigurovali důvěryhodná umístění nebo pojmenovaná umístění, můžete určit, jestli se mají tato umístění zahrnout nebo vyloučit ze zásad.
1. V části **udělit** se ujistěte, že je vybrán přepínač **udělení přístupu** .
    * Zaškrtněte políčko pro **vyžadovat službu Multi-Factor Authentication**.
    * Klikněte na **Vybrat**.
1. Přeskočit část **relace** .
1. Nastavte přepínač **Povolit zásady** na **zapnuto**.
1. Klikněte na **Vytvořit**.

## <a name="plan-integration-with-on-premises-systems"></a>Plánování integrace s místními systémy

Některé starší verze a místní aplikace, které se neověřují přímo proti službě Azure AD, vyžadují další kroky pro použití vícefaktorového ověřování (MFA), včetně:

* Starší verze místních aplikací, které budou muset používat proxy aplikace.
* Místní aplikace RADIUS, které budou muset používat adaptér MFA se serverem NPS.
* Místní AD FS aplikace, které budou muset používat adaptér MFA s AD FS 2016 nebo novějším.

Aplikace, které se ověřují přímo pomocí Azure AD a mají moderní ověřování (WS-dodává, SAML, OAuth, OpenID Connect), můžou přímo využívat zásady podmíněného přístupu.

### <a name="use-azure-ad-mfa-with-azure-ad-application-proxy"></a>Použití Azure AD MFA s Azure Proxy aplikací služby AD

Aplikace, které jsou umístěné místně, se dají publikovat do vašeho tenanta Azure AD prostřednictvím [Azure proxy aplikací služby AD](../manage-apps/application-proxy.md) a můžou využít výhod služby azure AD Multi-Factor Authentication, pokud jsou nakonfigurované tak, aby používaly předběžné ověřování Azure AD.

Tyto aplikace podléhají zásadám podmíněného přístupu, které vynutily Multi-Factor Authentication služby Azure AD, stejně jako jakékoli jiné aplikace integrované v Azure AD.

Podobně platí, že pokud se pro všechna přihlášení uživatelů vynutila Multi-Factor Authentication Azure AD, budou místní aplikace publikované s Azure Proxy aplikací služby AD chráněné.

### <a name="integrating-azure-ad-multi-factor-authentication-with-network-policy-server"></a>Integrace Multi-Factor Authentication služby Azure AD se serverem NPS (Network Policy Server)

Rozšíření serveru NPS (Network Policy Server) pro Azure AD MFA přidává cloudové možnosti MFA do vaší ověřovací infrastruktury pomocí vašich stávajících serverů. S rozšířením NPS můžete do stávajícího toku ověřování přidat ověřování pomocí telefonního hovoru, textové zprávy nebo aplikace pro telefon. Tato integrace má následující omezení:

* Pomocí protokolu CHAPv2 jsou podporovány pouze nabízená oznámení a volání aplikace ověřovatele.
* Zásady podmíněného přístupu nelze použít.

Rozšíření serveru NPS funguje jako adaptér mezi POLOMĚRem a cloudovou službou Azure AD MFA, která poskytuje druhý faktor ověřování pro ochranu [VPN](howto-mfa-nps-extension-vpn.md), [Brána vzdálené plochy připojení](howto-mfa-nps-extension-rdg.md)nebo jiné aplikace podporující protokol RADIUS. Uživatelům, kteří se registrují pro Azure AD MFA v tomto prostředí, budou pro všechny pokusy o ověření vyhlášeni. nedostatečná zásada podmíněného přístupu znamená, že se vždy vyžaduje MFA.

#### <a name="implementing-your-nps-server"></a>Implementace serveru NPS

Pokud máte nasazenou instanci serveru NPS a používáte ji již v provozu, odkaz [integruje stávající infrastrukturu serveru NPS se službou Azure AD Multi-Factor Authentication](howto-mfa-nps-extension.md). Pokud instalujete NPS poprvé, přečtěte si pokyny v tématu [NPS (Network Policy Server)](/windows-server/networking/technologies/nps/nps-top) . Pokyny k řešení potíží najdete v článku [řešení chybových zpráv z rozšíření serveru NPS pro Azure AD Multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Příprava serveru NPS pro uživatele, kteří nejsou zaregistrovaní pro MFA

Vyberte, co se stane, když se uživatelé, kteří nejsou zaregistrovaní pomocí MFA, pokoušejí ověřit. `REQUIRE_USER_MATCH`Pro řízení chování funkcí použijte nastavení registru v cestě `HKLM\Software\Microsoft\AzureMFA` k registru. Toto nastavení má jedinou možnost konfigurace.

| Klíč | Hodnota | Výchozí |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | TRUE NEBO FALSE | Nenastaveno (ekvivalent hodnoty TRUE) |

Účelem tohoto nastavení je určit, co dělat, když není uživatel zaregistrovaný pro MFA. Účinky změny tohoto nastavení jsou uvedené v následující tabulce.

| Nastavení | Stav MFA uživatele | Účinek |
| --- | --- | --- |
| Klíč neexistuje. | Zaregistrováno | Výzva MFA není úspěšná. |
| Hodnota nastavená na true/Nenastaveno | Zaregistrováno | Výzva MFA není úspěšná. |
| Nastavit klíč na false | Zaregistrováno | Ověřování bez MFA |
| Klíč nastavený na hodnotu false nebo true | Zaregistrované | Je nutné provést ověření pomocí MFA |

### <a name="integrate-with-active-directory-federation-services"></a>Integrace s Active Directory Federation Services (AD FS)

Pokud je vaše organizace federované s Azure AD, můžete použít [Azure ad Multi-Factor Authentication k zabezpečení prostředků AD FS](multi-factor-authentication-get-started-adfs.md)místních i cloudových prostředků. Azure AD MFA umožňuje omezit hesla a poskytnout bezpečnější způsob ověřování. Počínaje Windows serverem 2016 teď můžete nakonfigurovat Azure AD MFA pro primární ověřování.

Na rozdíl od AD FS ve Windows Serveru 2012 R2 se AD FS 2016 adaptér Azure AD MFA integruje přímo se službou Azure AD a nevyžaduje místní Azure MFA Server. Adaptér Azure AD MFA je integrovaný do systému Windows Server 2016 a nevyžaduje žádnou další instalaci.

Při použití Azure AD MFA s AD FS 2016 a cílová aplikace podléhá zásadám podmíněného přístupu, existují další okolnosti:

* Podmíněný přístup je dostupný, když je aplikace předávající stranou Azure AD, federované s AD FS 2016 nebo novější.
* Podmíněný přístup není k dispozici, pokud je aplikace předávající stranou AD FS 2016 nebo AD FS 2019 a je spravovaná nebo federované pomocí AD FS 2016 nebo AD FS 2019.
* Podmíněný přístup není dostupný i v případě, že AD FS 2016 nebo AD FS 2019 je nakonfigurovaná tak, aby jako primární metodu ověřování používala Azure AD MFA.

#### <a name="ad-fs-logging"></a>Protokolování AD FS

Standardní AD FS 2016 a 2019 protokolování v protokolu zabezpečení systému Windows a v protokolu AD FS správce obsahuje informace o požadavcích ověřování a jejich úspěchu nebo neúspěchu. Data protokolu událostí v těchto událostech určují, jestli se používala služba Azure AD MFA. Například ID události auditování AD FS 1200 může obsahovat:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Obnovení a Správa certifikátů

Na každém AD FSovém serveru bude v místním počítači moje úložiště k dispozici certifikát Azure AD MFA s názvem OU = Microsoft AD FS Azure MFA, který obsahuje datum vypršení platnosti certifikátu. Zkontrolujte dobu platnosti tohoto certifikátu na každém serveru AD FS, abyste zjistili datum vypršení platnosti.

Pokud se doba platnosti vašich certifikátů blíží k vypršení platnosti, [vygenerujte a ověřte nový certifikát MFA na každém serveru AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

Následující pokyny vám poradí, jak spravovat certifikáty Azure AD MFA na serverech AD FS. Když nakonfigurujete AD FS s využitím Azure AD MFA, certifikáty vygenerované pomocí `New-AdfsAzureMfaTenantCertificate` rutiny PowerShellu jsou platné po dobu dvou let. Obnovte a nainstalujte obnovené certifikáty před vypršením platnosti ovoid výpadků ve službě MFA.

## <a name="implement-your-plan"></a>Implementace plánu

Teď, když jste naplánovali řešení, můžete implementovat podle následujících kroků:

1. Splnění všech nezbytných požadavků
   1. Nasazení [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) pro všechny hybridní scénáře
   1. Nasazení [Azure proxy aplikací služby AD](../manage-apps/application-proxy.md) pro na všech místních aplikacích publikovaných pro přístup z cloudu
   1. Nasazení [serveru NPS](/windows-server/networking/technologies/nps/nps-top) pro jakékoli ověřování pomocí protokolu RADIUS
   1. Zajistěte, aby byly uživatelé upgradováni na podporované verze systém Microsoft Office s povoleným moderním ověřováním.
1. Konfigurovat zvolené [metody ověřování](#choose-verification-options)
1. Definice [pojmenovaných síťových umístění](../conditional-access/location-condition.md#named-locations)
1. Vyberte skupiny, které zahájí zavedení vícefaktorového ověřování.
1. Konfigurace [zásad podmíněného přístupu](#create-conditional-access-policy)
1. Konfigurace zásad registrace MFA
   1. [Kombinované MFA a SSPR](howto-registration-mfa-sspr-combined.md)
   1. S [identitou Protection](../identity-protection/howto-identity-protection-configure-mfa-policy.md)
1. Odeslat komunikaci uživatele a získat uživatele k registraci na [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Udržujte si přehled o tom, kdo je zaregistrovaný](#identify-non-registered-users)

> [!TIP]
> Uživatelé cloudu pro státní správu se můžou zaregistrovat na [https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>Správa řešení

Sestavy pro Azure AD MFA

Služba Azure AD Multi-Factor Authentication poskytuje sestavy prostřednictvím Azure Portal:

| Sestava | Umístění | Popis |
| --- | --- | --- |
| Používání a výstrahy týkající se podvodů | Přihlášení > Azure AD | Poskytuje informace o celkovém využití, souhrnu uživatelů a podrobnostech uživatelů. a také historii výstrah podvodů odeslaných během zadaného rozsahu dat. |

## <a name="troubleshoot-mfa-issues"></a>Řešení potíží s MFA

Řešení běžných problémů se službou Azure AD MFA najdete v [článku řešení potíží s Multi-Factor Authentication Azure AD](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) na centra podpora Microsoftu.

## <a name="next-steps"></a>Další kroky

Pokud chcete zobrazit Multi-Factor Authentication Azure AD v akci, proveďte následující kurz:

> [!div class="nextstepaction"]
> [Povolení vícefaktorového ověřování Azure AD](tutorial-enable-azure-mfa.md)
