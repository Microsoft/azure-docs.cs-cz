---
title: 'Azure AD Connect: Cloudové ověřování prostřednictvím postupného zavádění | Dokumenty společnosti Microsoft'
description: Tento článek vysvětluje, jak migrovat z federovaného ověřování na cloudové ověřování pomocí fázovaného zavedení.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3044ebdd716eb85dc63d3a77089912d0d51d8b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74915236"
---
# <a name="migrate-to-cloud-authentication-by-using-staged-rollout-preview"></a>Migrace na cloudové ověřování pomocí fázovaného zavedení (preview)

Pomocí fázovaného přístupu k zavádění můžete přejít z federovaného ověřování na cloudové ověřování. Tento článek popisuje, jak provést přepínač. Před zahájením fázovaného zavádění byste však měli zvážit důsledky, pokud platí jedna nebo více z následujících podmínek:
    
-  Aktuálně používáte místní server vícefaktorového ověřování. 
-  Používáte čipové karty pro ověřování. 
-  Aktuální server nabízí určité funkce pouze pro federaci.

Než tuto funkci vyzkoušíte, doporučujeme, abyste si přečetli našeho průvodce výběrem správné metody ověřování. Další informace najdete v tabulce Porovnání metod v [části Volba správné metody ověřování pro řešení hybridní identity služby Azure Active Directory](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods).

Přehled této funkce naleznete v tomto "Azure Active Directory: Co je postupné zavádění?" Video:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Požadavky

-   Máte klienta Azure Active Directory (Azure AD) s federovanými doménami.

-   Rozhodli jste se přejít na jednu ze dvou možností:
    - **Možnost Synchronizace** - *hash hesla (synchronizace)* + *bezproblémové jednotné přihlašování (SSO)*
    - **Možnost B** - *předávací ověřování* + *bezproblémové jednotné přihlaštení*
    
    Přestože *bezproblémové jednotné přihlašování* je volitelné, doporučujeme povolit k dosažení tiché přihlašovací prostředí pro uživatele, kteří jsou spuštěny počítače spojené s doménou z uvnitř podnikové sítě.

-   Nakonfigurovali jste všechny příslušné zásady značky klienta a podmíněného přístupu, které potřebujete pro uživatele, kteří jsou migrováni do cloudového ověřování.

-   Pokud máte v plánu používat Azure Multi-Factor Authentication, doporučujeme použít [konvergované registrace pro samoobslužné resetování hesla (SSPR) a vícefaktorové ověřování,](../authentication/concept-registration-mfa-sspr-combined.md) aby vaši uživatelé zaregistrovat své metody ověřování jednou.

-   Chcete-li použít funkci postupného zavádění, musíte být globálním správcem ve vašem tenantovi.

-   Chcete-li povolit *bezproblémové jednotné přihlašování* v určité doménové struktuře služby Active Directory, musíte být správcem domény.

## <a name="supported-scenarios"></a>Podporované scénáře

Následující scénáře jsou podporovány pro postupné zavádění. Tato funkce funguje pouze pro:

- Uživatelé, kteří jsou zřizované do Azure AD pomocí Azure AD Connect. Nevztahuje se na uživatele pouze pro cloud.

- Provoz přihlašování uživatelů v prohlížečích a *moderních ověřovacích* klientech. Aplikace nebo cloudové služby, které používají starší verze ověřování, se vrátí k federovaným tokům ověřování. Příkladem může být Exchange online s vypnutým moderním ověřováním nebo Outlook 2010, který nepodporuje moderní ověřování.

## <a name="unsupported-scenarios"></a>Nepodporované scénáře

Následující scénáře nejsou podporovány pro postupné zavádění:

- Některé aplikace odeslat parametr dotazu "domain_hint" do Služby Azure AD během ověřování. Tyto toky budou pokračovat a uživatelé, kteří jsou povoleni pro postupné zavedení, budou nadále používat federaci pro ověřování.

<!-- -->

- Správci můžou zavést cloudové ověřování pomocí skupin zabezpečení. Chcete-li se vyhnout latenci synchronizace při používání místních skupin zabezpečení služby Active Directory, doporučujeme použít skupiny zabezpečení cloudu. Platí tyto podmínky:

    - Můžete použít maximálně 10 skupin na funkci. To znamená, že můžete použít 10 skupin pro *synchronizaci hash hesla*, *předávací ověřování*a *bezproblémové jednotné přihlašovat.*
    - Vnořené skupiny *nejsou podporovány*. Tento obor platí také pro verzi Public Preview.
    - Dynamické skupiny *nejsou podporovány* pro postupné zavádění.
    - Objekty kontaktu uvnitř skupiny zabrání přidání skupiny.

- Pořád potřebujete provést konečné cutover z federované ho cloudu ověřování pomocí Azure AD Connect nebo PowerShell. Postupné zavádění nepřepíná domény z federovaných na spravované.

- Při prvním přidání skupiny zabezpečení pro postupné zavádění jste omezeni na 200 uživatelů, abyste se vyhnuli časovému limitu uživatelského uživatelského nastavení. Po přidání skupiny můžete podle potřeby přidat další uživatele přímo do skupiny.

## <a name="get-started-with-staged-rollout"></a>Začínáme s fázovaným zaváděním

Chcete-li otestovat přihlášení *synchronizace hash hesla* pomocí fázovaného zavedení, postupujte podle pokynů pro předběžnou práci v další části.

Informace o tom, které rutiny prostředí PowerShell použít, naleznete v [tématu Azure AD 2.0 preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="pre-work-for-password-hash-sync"></a>Předprací pro synchronizaci hash hesel

1. Povolte  *synchronizaci hash hesel*ze stránky [Volitelné funkce](how-to-connect-install-custom.md#optional-features) ve službě Azure AD Connect. 

   ![Snímek obrazovky se stránkou Volitelné funkce ve službě Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Ujistěte se, že byl spuštěn úplný cyklus *synchronizace hash hesel,* aby byly všechny hashy hesel uživatelů synchronizovány se službou Azure AD. Chcete-li zkontrolovat stav *synchronizace hash hesla*, můžete použít diagnostiku prostředí PowerShell v [řešení synchronizace hash hesla pomocí synchronizace azure ad connect](tshoot-connect-password-hash-synchronization.md).

   ![Snímek obrazovky s protokolem poradce při potížích s připojením AADConnect](./media/how-to-connect-staged-rollout/sr2.png)

Pokud chcete otestovat *předávací ověřování* přihlášení pomocí fázované zavedení, povolte jej podle pokynů před prací v další části.

## <a name="pre-work-for-pass-through-authentication"></a>Předprací pro předávací ověřování

1. Identifikujte server se systémem Windows Server 2012 R2 nebo novějším, na kterém chcete spustit *předávacího agenta ověřování.* 

   *Nevybírejte* server Azure AD Connect.Ujistěte se, že server je připojen k doméně, můžete ověřit vybrané uživatele pomocí služby Active Directory a můžete komunikovat s Azure AD na odchozí porty a adresy URL. Další informace naleznete v části "Krok 1: Kontrola požadavků" [na úvodním startu: Azure AD bezproblémové jednotné přihlašování](how-to-connect-sso-quick-start.md).

1. [Stáhněte si agenta ověřování Azure AD Connect](https://aka.ms/getauthagent)a nainstalujte ho na server. 

1. Chcete-li povolit [vysokou dostupnost](how-to-connect-sso-quick-start.md), nainstalujte další agenty ověřování na jiné servery.

1. Ujistěte se, že jste správně nakonfigurovali [nastavení inteligentního uzamčení.](../authentication/howto-password-smart-lockout.md) To pomáhá zajistit, aby místní účty služby Active Directory vašich uživatelů nebyly uzamčeny špatnými aktéry.

Doporučujeme povolit *bezproblémové jednotné přihlašování* bez ohledu na metodu přihlášení *(synchronizace hash hesel* nebo *předávací ověřování),* kterou vyberete pro postupné zavedení. Chcete-li povolit *bezproblémové jednotné přihlašující*, postupujte podle pokynů pro předběžnou práci v další části.

## <a name="pre-work-for-seamless-sso"></a>Před-práce pro bezproblémové jednotné přihlašované

Povolte *bezproblémové jednotné přihlašování* v doménových strukturách služby Active Directory pomocí prostředí PowerShell. Pokud máte více doménové struktury služby Active Directory, povolte ji pro každou doménovou strukturu jednotlivě.  *Bezproblémové jednotné přihlašování* se aktivuje pouze pro uživatele, kteří jsou vybráni pro postupné zavedení. Nemá vliv na stávající nastavení federace.

Povolit *bezproblémové jednotné přihlašuje* tím, že provádí tenásledující:

1. Přihlaste se k serveru Azure AD Connect.

2. Přejděte do složky *%programfiles%\\služby Microsoft Azure Active Directory Connect.* 

3. Importujte *bezešvý* modul Jednotné ho so powershellu spuštěním následujícího příkazu: 

   `Import-Module .\AzureADSSO.psd1`

4. Spusťte PowerShell jako správce. V Prostředí PowerShell volejte `New-AzureADSSOAuthenticationContext`. Tento příkaz otevře podokno, ve kterém můžete zadat pověření globálního správce klienta.

5. Zavolejte `Get-AzureADSSOStatus | ConvertFrom-Json`. Tento příkaz zobrazí seznam doménových struktur služby Active Directory (viz seznam Domény), ve kterých byla tato funkce povolena. Ve výchozím nastavení je nastavena na false na úrovni klienta.

   ![Příklad výstupu prostředí Windows PowerShell](./media/how-to-connect-staged-rollout/sr3.png)

6. Zavolejte `$creds = Get-Credential`. Na výzvu zadejte pověření správce domény pro zamýšlenou doménovou strukturu služby Active Directory.

7. Zavolejte `Enable-AzureADSSOForest -OnPremCredentials $creds`. Tento příkaz vytvoří účet počítače AZUREADSSOACC z místního řadiče domény pro doménovou strukturu služby Active Directory, která je vyžadována pro *bezproblémové jednotné přihlašování*.

8. *Bezproblémové jednotné přihlašování* vyžaduje adresy URL, které mají být v zóně intranet. Informace o nasazení těchto adres URL pomocí zásad skupiny najdete v [tématu Úvodní příručka: Azure AD bezproblémové jednotné přihlašování](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature).

9. Pro úplný návod, můžete si také stáhnout naše [plány nasazení](https://aka.ms/SeamlessSSODPDownload) pro *bezproblémové jednotné přihlašování*.

## <a name="enable-staged-rollout"></a>Povolení postupného zavádění

Chcete-li zavést určitou funkci (*předávací ověřování*, *synchronizace hash hesel*nebo *bezproblémové jednotné přihlašování*) do vybrané sady uživatelů ve skupině, postupujte podle pokynů v následujících částech.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Povolení postupného zavedení určité funkce v tenantovi

Můžete zavést jednu z těchto možností:

- **Možnost Synchronizace** - *nastavení* + hash hesla*bezproblémová jednotné přihlašuje*
- **Možnost B** - *předávací ověřování* + *bezproblémové jednotné přihlaštení*
- **Není podporováno** - *heslo hash synchronizace* + *předávací ověřování* + *bezproblémové jednotné přihlašuje*

Udělejte toto:

1. Chcete-li získat přístup k uživatelskému uživatelskému panelu ve verzi Preview, přihlaste se na [portál Azure AD](https://aka.ms/stagedrolloutux).

2. Vyberte odkaz **Povolit postupné zavádění pro přihlašování spravovaného uživatele (preview).**

   Chcete-li například povolit *možnost A*, posuňte ovládací prvky **synchronizace hash hesel** a **bezešvé jednotné přihlašování** do **možnosti Zapnuto**, jak je znázorněno na následujících obrázcích.

   ![Stránka Azure AD Connect](./media/how-to-connect-staged-rollout/sr4.png)

   ![Stránka Povolit funkce postupného zavádění (náhled)"](./media/how-to-connect-staged-rollout/sr5.png)

3. Přidejte skupiny do funkce povolit *předávací ověřování* a *bezproblémové jednotné přihlašovat*. Chcete-li se vyhnout časovému odpočitadlu uživatelského nastavení, ujistěte se, že skupiny zabezpečení zpočátku neobsahují více než 200 členů.

   ![Stránka Správa skupin pro synchronizaci hash hesel (preview)"](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >Členové ve skupině jsou automaticky povoleny pro postupné zavádění. Vnořené a dynamické skupiny nejsou podporovány pro postupné zavádění.

## <a name="auditing"></a>Auditování

Povolili jsme události auditu pro různé akce, které provádíme pro postupné zavádění:

- Auditovat událost, když povolíte postupné zavedení *synchronizace hash hesel*, *předávacího ověřování*nebo *bezproblémového jednotného přihlašování*.

  >[!NOTE]
  >Událost auditu je zaznamenána při *bezproblémové jednotné přihlašování* je zapnuto pomocí postupného zavádění.

  ![Podokno Vytvořit zásadu zavedení funkce – karta Aktivita](./media/how-to-connect-staged-rollout/sr7.png)

  ![Podokno Vytvořit zásadu zavedení funkce – karta Upravená vlastnosti](./media/how-to-connect-staged-rollout/sr8.png)

- Auditovat událost při přidání skupiny do *synchronizace hash hesel*, *předávacího ověřování*nebo *bezproblémového jednotného přihlašování*.

  >[!NOTE]
  >Událost auditu je zaznamenána, když je skupina přidána do *synchronizace hodnoty hash hesla* pro postupné zavedení.

  ![Podokno "Přidat skupinu k zavedení prvku" – karta Aktivita](./media/how-to-connect-staged-rollout/sr9.png)

  ![Podokno "Přidat skupinu k zavedení prvku" – karta Upravená vlastnosti](./media/how-to-connect-staged-rollout/sr10.png)

- Auditovat událost, když je uživatel, který byl přidán do skupiny, povolen pro postupné zavedení.

  ![Podokno Přidat uživatele k zavedení funkce – karta Aktivita](media/how-to-connect-staged-rollout/sr11.png)

  ![Podokno "Přidat uživatele k zavedení funkce" – karta Cíl (cíle)](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Ověřování

Chcete-li otestovat přihlášení pomocí *synchronizace hash hesel* nebo *předávacího ověřování* (uživatelské jméno a přihlášení k heslu), postupujte takto:

1. Na extranetu přejděte na [stránku Aplikace](https://myapps.microsoft.com) v relaci soukromého prohlížeče a zadejte UserPrincipalName (UPN) uživatelského účtu, který je vybrán pro postupné zavedení.

   Uživatelé, kteří byli zacíleni na postupné zavedení, nebudou přesměrováni na federovanou přihlašovací stránku. Místo toho jsou požádáni o přihlášení na přihlašovací stránce značky tenanta Azure AD.

1. Ujistěte se, že přihlášení úspěšně zobrazí v [sestavě aktivity přihlášení Služby Azure AD](../reports-monitoring/concept-sign-ins.md) filtrováním s UserPrincipalName.

Testování přihlášení pomocí *bezproblémového jednotného přihlašování*:

1. V intranetu přejděte na [stránku Aplikace](https://myapps.microsoft.com) v soukromé relaci prohlížeče a zadejte UserPrincipalName (UPN) uživatelského účtu, který je vybrán pro postupné zavedení.

   Uživatelům, kteří byli zacíleni na postupné zavádění *bezproblémového jednotného přihlašování,* se zobrazí "Pokus o přihlášení ..." zprávu před tichým přihlášením.

1. Ujistěte se, že přihlášení úspěšně zobrazí v [sestavě aktivity přihlášení Služby Azure AD](../reports-monitoring/concept-sign-ins.md) filtrováním s UserPrincipalName.

   Chcete-li sledovat přihlášení uživatelů, ke kterým stále dochází ve službě AD FS (AD FS) u vybraných uživatelů postupného zavádění, postupujte podle pokynů při [řešení potíží služby AD FS: Události a protokolování](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events). Zkontrolujte dokumentaci dodavatele o tom, jak zkontrolovat to na zprostředkovatele federace třetích stran.

## <a name="remove-a-user-from-staged-rollout"></a>Odebrání uživatele z postupného zavedení

Odebráním uživatele ze skupiny zakážete postupné zavedení pro tohoto uživatele. Chcete-li funkci fázovaného zavedení zakázat, posuňte ovládací prvek zpět na **vypnuto**.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka: Lze tuto funkci použít v produkčním prostředí?**

A: Ano, můžete použít tuto funkci ve vašem produkčním tenantovi, ale doporučujeme nejprve vyzkoušet v testovacím tenantovi.

**Otázka: Lze tuto funkci použít k zachování trvalého "koexistence", kde někteří uživatelé používají federované ověřování a jiní používají cloudové ověřování?**

A: Ne, tato funkce je určena pro migraci z federované ho do cloudu ověřování ve fázích a nakonec přejít na cloud ověřování. Nedoporučujeme používat trvalý smíšený stav, protože tento přístup může vést k neočekávaným tokům ověřování.

**Otázka: Můžu použít PowerShell k provedení postupného zavádění?**

Odpověď: Ano. Informace o tom, jak pomocí PowerShellu provádět postupné zavádění, najdete v [tématu Azure AD Preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="next-steps"></a>Další kroky
- [Azure AD 2.0 preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
