---
title: 'Azure AD Connect: cloudové ověřování – dvoufázové zavedení | Microsoft Docs'
description: Vysvětluje, jak migrovat z federovaného ověřování do cloudového ověřování pomocí připraveného zavedení.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/28/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 254fa924ea2104e76a475c106f16f4672dd1eec1
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033313"
---
# <a name="cloud-authentication-staged-rollout-public-preview"></a>Cloudové ověřování: fáze zavedení (Public Preview)

Tato funkce umožňuje migrovat z federovaného ověřování na cloudové ověřování pomocí dvoufázového přístupu.

Přesun směrem od federovaného ověřování má vliv na. Například pokud máte některou z následujících možností:
    
-  místní MFA Server 
-  používají pro ověřování čipové karty 
-  jenom další federace – funkce

Tyto funkce by se měly vzít v úvahu před přechodem na cloudové ověřování.  Než se pustíte do této funkce, doporučujeme vám projít si náš průvodce pro výběr správné metody ověřování. Další podrobnosti najdete v [této tabulce](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods) .

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Předpoklady

-   Máte tenanta Azure AD se federované domény.

-   Rozhodli jste se přesunout buď synchronizaci hodnot hash hesel + bezproblémové jednotné přihlašování **(možnost A),** nebo předávací ověřování + bezproblémové přihlašování **(možnost B).** I když je bezproblémové jednotné přihlašování volitelné, doporučujeme, abyste povolili bezproblémové jednotné přihlašování pro uživatele, kteří používají počítače připojené k doméně z interní podnikové sítě.

-   Pro uživatele, kteří se migrují do cloudového ověřování, jste nakonfigurovali všechny příslušné zásady tenanta a zásady podmíněného přístupu.

-   Pokud plánujete použít Azure Multi-Factor Authentication, doporučujeme použít [sblíženou registraci pro Samoobslužné resetování hesla (SSPR) a Azure MFA a](../authentication/concept-registration-mfa-sspr-combined.md) získat tak uživatelům, aby jednou zaregistrovali své metody ověřování.

-   Aby bylo možné tuto funkci používat, musíte být globálním správcem vašeho tenanta.

-   Pokud chcete povolit bezproblémové přihlašování v konkrétní doménové struktuře služby AD, musíte být správce domény.

## <a name="supported-scenarios"></a>Podporované scénáře

Tyto scénáře jsou podporovány pro připravené zavedení:

- Tato funkce funguje jenom pro uživatele zřízené ve službě Azure AD pomocí Azure AD Connect a nedá se použít jenom pro cloudové uživatele.

- Tato funkce funguje jenom pro přihlašovací data uživatelů v prohlížečích a v klientech moderního ověřování. Aplikace nebo cloudové služby využívající starší verze ověřování se vrátí do toků federovaného ověřování. (Příklad: Exchange Online s moderním ověřováním vypnuté nebo Outlook 2010, který nepodporuje moderní ověřování.)

## <a name="unsupported-scenarios"></a>Nepodporované scénáře

Tyto scénáře nejsou podporované pro fáze zavedení:

- Určité aplikace odesílají parametr dotazu "Domain\_Hint" do služby Azure AD během ověřování. Tyto toky budou pokračovat a uživatelé s povoleným postupným zaváděním budou pro ověřování nadále používat federaci.

<!-- -->

- Správce může zavedení cloudového ověřování pomocí skupin zabezpečení. (Při používání místních skupin zabezpečení služby AD se doporučuje použít skupiny zabezpečení cloudu, aby nedocházelo k latenci synchronizace.)

    - Můžete použít **maximálně 10 skupin na jednu funkci**; To znamená, že pro každou synchronizaci hodnot hash hesel/předávací ověřování/bezproblémové jednotné přihlašování.

    - Vnořené skupiny se **nepodporují**. Toto je také obor pro verzi Public Preview.

    - Pro fáze zavedení se **nepodporují** dynamické skupiny.

    - Objekty kontaktu uvnitř skupiny budou blokovat přidávané formuláře skupiny.

- Konečné přímou migraci z federovaných na cloudové ověřování se pořád musí vyskytnout pomocí Azure AD Connect nebo PowerShellu. Tato funkce nepřepne domény ze federovat do spravovaného.

- Když poprvé přidáte skupinu zabezpečení pro přípravu do přípravy, bude omezena na 200 uživatelů, aby nedošlo k vypršení časového limitu uživatelského prostředí. Po přidání skupiny do uživatelského rozhraní můžete podle potřeby přidat další uživatele přímo do skupiny.

## <a name="get-started-with-staged-rollout"></a>Začínáme s fází uvedení do provozu

Pokud chcete testovat synchronizaci hodnot hash hesel (KOSMETICE) pomocí připraveného zavedení, dokončete prosím níže uvedenou předběžnou práci, aby se aktivovala fáze zavedení synchronizace hodnot hash hesel.

## <a name="pre-work-for-password-hash-sync"></a>Předběžná práce pro synchronizaci hodnot hash hesel

1. Povolte synchronizaci hodnot hash hesel na stránce [volitelné funkce](how-to-connect-install-custom.md#optional-features) v Azure AD Connect. 

   ![Snímek obrazovky volitelných funkcí na stránce Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Zajistěte, aby cyklus synchronizace hodnot hash celého hesla běžel prostřednictvím, aby všechny hodnoty hash hesel uživatelů byly synchronizovány do Azure AD. K ověření stavu synchronizace hodnot hash hesel můžete [použít diagnostiku](tshoot-connect-password-hash-synchronization.md) PowerShellu.

   ![Snímek obrazovky s protokolem pro řešení potíží s AADConnect](./media/how-to-connect-staged-rollout/sr2.png)

Pokud chcete otestovat přihlášení Pass over-Authentication (PTA) pomocí připraveného zavedení, dokončete prosím níže uvedenou předběžnou práci a povolte PTA pro připravené zavedení.

## <a name="pre-work-for-pass-through-authentication"></a>Předběžná práce pro předávací ověřování

1. Identifikujte Server se systémem Windows Server 2012 R2 nebo novějším, kde chcete spustit ověřovacího agenta pro předávání (**nevybírejte Azure AD Connect Server**). Ujistěte se, že je server připojený k doméně, umožňuje ověřovat vybrané uživatele pomocí služby Active Directory a může komunikovat se službou Azure AD na odchozích portech/adresách URL (viz podrobné [požadavky](how-to-connect-sso-quick-start.md)).

1. [Stáhněte si](https://aka.ms/getauthagent) & nainstalujte na server ověřovacího agenta Microsoft Azure AD Connect. 

1. Pokud chcete povolit [vysokou dostupnost](how-to-connect-sso-quick-start.md), nainstalujte další ověřovací agenty na jiné servery.

1. Ujistěte se, že jste správně nakonfigurovali [Nastavení inteligentního uzamčení](../authentication/howto-password-smart-lockout.md) . K tomu je potřeba zajistit, aby účty uživatelů místní služby Active Directory nebyly uzamčeny nesprávnými aktéry.

Doporučujeme povolit bezproblémové přihlašování bez ohledu na metodu přihlašování (KOSMETICE nebo PTA), kterou jste vybrali pro připravené zavedení. Dokončete prosím níže uvedenou předběžnou práci, abyste umožnili bezproblémové přihlašování k předběžnému přihlášení k připravenému zavedení.

## <a name="pre-work-for-seamless-sso"></a>Předběžná práce pro bezproblémové přihlašování

Povolte bezproblémové jednotné přihlašování v doménových strukturách AD pomocí PowerShellu. Pokud máte více než jednu doménovou strukturu služby Active Directory, povolte ji jednotlivě pro každou doménovou strukturu. Bezproblémové jednotné přihlašování se bude spouštět jenom pro uživatele vybrané pro připravené zavedení a nebude mít vliv na stávající nastavení federace.

**Souhrn kroků**

1. Nejprve se přihlaste k serveru Azure AD Connect.

2. Přejděte do složky% ProgramFiles%\\Microsoft Azure Active Directory Connect.

3. Pomocí tohoto příkazu Importujte modul bezproblémového prostředí PowerShell pro jednotné přihlašování: `Import-Module .\\AzureADSSO.psd1`.

4. Spusťte PowerShell jako správce. V prostředí PowerShell volejte `New-AzureADSSOAuthenticationContext`. Tento příkaz by měl poskytnout dialogové okno, kde můžete zadat přihlašovací údaje globálního správce vašeho tenanta.

5. Zavolejte `Get-AzureADSSOStatus \| ConvertFrom-Json`. Tento příkaz vám poskytne seznam doménových struktur AD (podívejte se na seznam\" \"domény), na kterém je tato funkce povolená. Ve výchozím nastavení je nastavena na hodnotu false na úrovni tenanta.

   > **Příklad:** 
   > ![příklad výstupu Windows powershellu](./media/how-to-connect-staged-rollout/sr3.png)

6. Zavolejte `\$creds = Get-Credential`. Po zobrazení výzvy zadejte přihlašovací údaje správce domény pro zamýšlenou doménovou strukturu služby AD.

7. Zavolejte `Enable-AzureADSSOForest -OnPremCredentials \$creds`. Tento příkaz vytvoří účet počítače AZUREADSSOACC z místního řadiče domény pro tuto konkrétní doménovou strukturu služby Active Directory, která je nutná pro bezproblémové jednotné přihlašování.

8. Bezproblémové jednotné přihlašování vyžaduje, aby adresy URL byly v zóně intranetu. K nasazení těchto adres URL pomocí zásad skupiny se prosím podívejte na [bezproblémové rychlé zprovoznění jednotného přihlašování](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature) .

9.  Můžete si také stáhnout [plány nasazení](https://aka.ms/SeamlessSSODPDownload) pro bezproblémové přihlašování, které vám umožní kompletní postup.

## <a name="enable-staged-rollout"></a>Povolit připravené zavedení

Pomocí následujících kroků můžete zavést konkrétní funkci (předávací ověřování/hodnota hash hesla/bezproblémové jednotné přihlašování) pro vybranou skupinu uživatelů ve skupině:

### <a name="enable-the-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Povolit připravené zavedení konkrétní funkce ve vašem tenantovi

Jednu z těchto možností můžete zavést

-   Synchronizace hodnot hash hesel + bezproblémové jednotné přihlašování **(možnost A)**

-   Předávací ověřování + bezproblémové jednotné přihlašování **(možnost B)**

-   Synchronizace hodnot hash hesel + předávací ověřování + bezproblémové přihlašování **-\>** ***nepodporované***

Proveďte tyto kroky:

1. Přihlaste se k portálu Azure AD pomocí níže uvedené adresy URL pro přístup k UŽIVATELSKÉmu rozhraní verze Preview.

   > <https://aka.ms/stagedrolloutux>

2. Klikněte na povolit dvoufázové zavedení pro přihlášení ke spravovanému uživateli (Preview).

   *Například:* (**možnost B**) Pokud chcete povolit synchronizaci hodnot hash hesel a bezproblémové jednotné přihlašování, přepište prosím synchronizaci hodnot hash hesel a bezproblémové jednotné přihlašování na **zapnuto** , jak je znázorněno níže.

   ![](./media/how-to-connect-staged-rollout/sr4.png)

   ![](./media/how-to-connect-staged-rollout/sr5.png)

3. Přidejte příslušné skupiny do funkce pro povolení předávacího ověřování a bezproblémového jednotného přihlašování. Zajistěte, aby skupiny zabezpečení neprošly více než 200 členů, aby nedocházelo k vypršení časového limitu uživatelského rozhraní.

   ![](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >Členové ve skupině jsou automaticky povoleni pro připravené zavedení. Vnořené a dynamické skupiny se pro připravené zavedení nepodporují.

## <a name="auditing"></a>Auditování

Povolili jsme události auditu pro různé akce, které provádíme pro připravené zavedení.

- Událost auditu, když povolíte postupné zavedení pro synchronizaci hodnot hash hesel/předávací ověřování/bezproblémové jednotné přihlašování.

  >[!NOTE]
  >Událost auditu, která se protokoluje **při zapnutí SeamlessSSO pomocí StagedRollout**

  ![](./media/how-to-connect-staged-rollout/sr7.png)

  ![](./media/how-to-connect-staged-rollout/sr8.png)

- Událost auditu, když se přidá skupina ke synchronizaci hodnot hash hesel, předávacímu ověřování/bezproblémovému přihlášení SSO.

  >[!NOTE]
  >Událost auditu zaznamenané při přidání skupiny do synchronizace hodnot hash hesel pro připravené zavedení

  ![](./media/how-to-connect-staged-rollout/sr9.png)

  ![](./media/how-to-connect-staged-rollout/sr10.png)

- Událost auditu, pokud je povolený uživatel, který byl přidán do skupiny pro připravené zavedení

  ![](media/how-to-connect-staged-rollout/sr11.png)

  ![](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Ověření

Testování přihlášení pomocí synchronizace hodnot hash hesel nebo předávacího ověřování (přihlášení uživatele/hesla):

1. Vyhledejte <https://myapps.microsoft.com> v privátní relaci prohlížeče z extranetu a zadejte hodnotu UserPrincipalName (UPN) uživatelského účtu vybraného pro přípravu.

1. Pokud je uživatel cílen na dvoufázové zavedení, uživatel nebude přesměrován na vaši stránku federovaného přihlášení a místo toho se zobrazí výzva k přihlášení na přihlašovací stránce klienta Azure AD se značkou.

1. Zajistěte, aby se přihlášení úspěšně zobrazilo v [sestavě aktivit přihlášení služby Azure AD](../reports-monitoring/concept-sign-ins.md) pomocí filtrování pomocí třídy userPrincipalName..

Testování přihlášení pomocí bezproblémového jednotného přihlašování:

1. Přejděte do <https://myapps.microsoft.com>/v privátní relaci prohlížeče z intranetu a zadejte hodnotu UserPrincipalName (UPN) uživatelského účtu vybraného pro přípravu.

1. Pokud byl uživatel cílen na přípravu bezproblémové přihlašování (SSO), zobrazí se uživateli "pokus o přihlášení"... Zpráva předtím, než se tiše přihlásí.

1. Zajistěte, aby se přihlášení úspěšně zobrazilo v [sestavě aktivit přihlášení Azure AD](../reports-monitoring/concept-sign-ins.md) pomocí filtrování pomocí třídy userPrincipalName.

Chcete-li kontrolovat přihlašování uživatelů i u zprostředkovatelů federačních služeb:

Tady je postup, jak můžete sledovat přihlašování uživatelů, ke kterým dochází AD FS pro vybrané pracovní uživatele podle [těchto pokynů](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events). Podívejte se na dokumentaci od dodavatele, kde najdete informace o tom, jak tohoto poskytovatele federačních služeb třetích stran vrátit

## <a name="roll-back"></a>Vrátit zpět

### <a name="remove-a-user-from-staged-rollout"></a>Odebrání uživatele z připraveného zavedení

1.  Odebrání uživatele ze skupiny zakáže pro daného uživatele fázi zavádění.

2.  Pokud chcete zakázat funkci přípravy na přípravu, posuňte prosím tuto funkci zpátky na stav **vypnuto** a vypněte přípravu do fází.


## <a name="frequently-asked-questions"></a>Nejčastější dotazy

-   **Otázka: může zákazník tuto možnost používat v produkčním prostředí?**

-   Odpověď: Ano, tato funkce se dá použít ve vašem produkčním tenantovi, ale doporučujeme, abyste tuto funkci nejdřív vyzkoušeli ve vašem tenantovi test.

-   **Otázka: Tato funkce se dá použít k udržení trvalé "společné existence", kde někteří uživatelé používají federované ověřování a jiné cloudové ověřování?**

-   Odpověď: Ne, tato funkce je navržena pro migraci z federovaných do cloudového ověřování ve fázích a pak na jejich následné vyjmutí do cloudového ověřování. Nedoporučujeme Stálý smíšený stav, protože by to mohlo vést k neočekávaným tokům ověřování.

-   **Otázka: můžeme použít PowerShell k provádění připraveného zavedení?**

-   Odpověď: Ano, přečtěte si dokumentaci k použití prostředí PowerShell k provedení připraveného zavedení.
