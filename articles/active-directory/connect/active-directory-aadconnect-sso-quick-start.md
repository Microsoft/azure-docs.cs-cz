---
title: 'Azure AD Connect: Bezproblémové jednotné přihlašování – rychlý start | Dokumentace Microsoftu'
description: Tento článek popisuje, jak začít s Azure Active Directory bezproblémové jednotné přihlašování
services: active-directory
keywords: Co je Azure AD Connect, instalace Active Directory, požadované součásti pro službu Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 4710e30ad38485f47c115ac2e0e914e91c96d582
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522177"
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Azure Active Directory bezproblémové jednotné přihlašování: rychlý start

## <a name="deploy-seamless-single-sign-on"></a>Nasazení bezproblémové jednotné přihlašování

Azure Active Directory (Azure AD) bezproblémové jednotné přihlašování (bezproblémové jednotné přihlašování) se automaticky přihlásí uživatelé při práci na podnikové stolní počítače, které jsou připojené k vaší podnikové síti. Bezproblémové jednotné přihlašování poskytuje uživatelům snadný přístup k vaší cloudové aplikace bez nutnosti jakékoli další místní komponenty.

K zavedení bezproblémového jednotného přihlašování, postupujte podle těchto kroků.

## <a name="step-1-check-the-prerequisites"></a>Krok 1: Kontrola požadavků

Ujistěte se, že jsou splněné následující požadavky:

* **Vytvoření vašeho serveru Azure AD Connect**: Pokud používáte [předávací ověřování](active-directory-aadconnect-pass-through-authentication.md) jako způsob přihlášení, je požadovaná žádné další kontroly požadavků. Pokud používáte [synchronizaci hodnot hash hesel](active-directory-aadconnectsync-implement-password-hash-synchronization.md) jako způsob přihlášení a pokud je brána firewall mezi Azure AD Connect a službou Azure AD, ujistěte se, že:
   - Použít verzi 1.1.644.0 nebo novější služby Azure AD Connect. 
   - Pokud vaše brána firewall nebo proxy server umožňuje DNS na seznam povolených, seznam povolených připojení k  **\*. msappproxy.net** adresy URL přes port 443. Pokud ne, povolit přístup k [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653), který se každý týden aktualizuje. Tento požadavek platí jenom v případě, že povolíte tuto funkci. To není nutné pro skutečný uživatel přihlásil.

    >[!NOTE]
    >Azure AD Connect verze 1.1.557.0, 1.1.558.0, 1.1.561.0 a 1.1.614.0 máte problém související se synchronizace hodnot hash hesel. Pokud jste _není_ používat synchronizaci hodnot hash hesel ve spojení se předávací ověřování, přečtěte si [poznámky k verzi Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) Další informace.

* **Použití podporované topologie služby Azure AD Connect**: Ujistěte se, že používáte jednu z Azure AD Connect podporované topologie popsané [tady](active-directory-aadconnect-topologies.md).

* **Nastavit přihlašovací údaje správce domény**: musíte mít přihlašovací údaje správce domény pro každé služby Active Directory doménové struktury, které:
    * Synchronizovat s Azure AD prostřednictvím služby Azure AD Connect.
    * Obsahuje uživatele, které chcete povolit pro bezproblémové jednotné přihlašování.
    
* **Povolit moderní ověřování**: je nutné povolit [moderní ověřování](https://aka.ms/modernauthga) ve svém tenantovi pro tuto funkci pracovat.

* **Použít nejnovější verze klientů Office 365**: Pokud chcete získat tiché přihlašování s klienty Office 365 (Outlook, Word, Excel a další), uživatelé muset použít verze 16.0.8730.xxxx nebo vyšší.

## <a name="step-2-enable-the-feature"></a>Krok 2: Povolení funkce

Povolte bezproblémové jednotné přihlašování prostřednictvím [služby Azure AD Connect](active-directory-aadconnect.md).

Pokud provádíte novou instalaci služby Azure AD Connect, zvolte [vlastní instalační cesta](active-directory-aadconnect-get-started-custom.md). Na **přihlášení uživatele** stránky, vyberte **povolit jednotné přihlašování** možnost.

>[!NOTE]
> Možnost nebude k dispozici pro výběr, pouze pokud je metoda přihlašování **synchronizaci hodnot Hash hesel** nebo **předávací ověřování**.

![Azure AD Connect: Přihlášení uživatele](./media/active-directory-aadconnect-sso/sso8.png)

Pokud již máte instalaci služby Azure AD Connect, vyberte **změnit přihlášení uživatele** stránky ve službě Azure AD Connect a pak vyberte **Další**. Pokud používáte Azure AD Connect verze 1.1.880.0 nebo vyšší, **povolit jednotné přihlašování** ve výchozím nastavení bude vybrána možnost. Pokud používáte starší verze služby Azure AD Connect, vyberte **povolit jednotné přihlašování** možnost.

![Azure AD Connect: Změňte přihlášení uživatele](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Pokračujte v průvodci, dokud nezískáte **povolit jednotné přihlašování** stránky. Poskytněte přihlašovací údaje správce domény pro každou službu Active Directory doménové struktury, které:
    * Synchronizovat s Azure AD prostřednictvím služby Azure AD Connect.
    * Obsahuje uživatele, které chcete povolit pro bezproblémové jednotné přihlašování.

Po dokončení průvodce je bezproblémového jednotného přihlašování povolená ve svém tenantovi.

>[!NOTE]
> Přihlašovací údaje správce domény nejsou uložené ve službě Azure AD Connect nebo ve službě Azure AD. Slouží jenom k povolení této funkce.

Postupujte podle těchto pokynů a ověřte, že jste povolili bezproblémového jednotného přihlašování správně:

1. Přihlaste se k [Centrum správy služby Azure Active Directory](https://aad.portal.azure.com) pomocí přihlašovacích údajů globálního správce pro vašeho tenanta.
2. Vyberte **Azure Active Directory** v levém podokně.
3. Vyberte **služby Azure AD Connect**.
4. Ověřte, že **bezproblémové jednotné přihlašování** funkce se zobrazí jako **povoleno**.

![Azure portal: podokně služby Azure AD Connect](./media/active-directory-aadconnect-sso/sso10.png)

>[!IMPORTANT]
> Bezproblémové jednotné přihlašování vytvoří účet počítače `AZUREADSSOACC` (která představuje Azure AD) ve vaší místní služby Active Directory (AD) v každé doménové struktuře AD. Tento účet počítače je potřeba pro funkci pracovat. Přesunout `AZUREADSSOACC` počítačový účet k organizaci jednotky (OU) ukládat další účty počítačů zajistit, že je spravovat stejným způsobem a se neodstraní.

## <a name="step-3-roll-out-the-feature"></a>Krok 3: Zavedení funkce

Můžete postupně zavedení bezproblémového jednotného přihlašování pro vaše uživatele pomocí níže uvedených pokynů. Začněte přidáním následující adresu URL služby Azure AD pro všechny nebo vybrané nastavení zóny Intranet uživatele pomocí zásad skupiny ve službě Active Directory:

- https://autologon.microsoftazuread-sso.com

Kromě toho je potřeba povolit zásadu zóny intranetu názvem **povolit aktualizace stavového řádku prostřednictvím skriptu** prostřednictvím zásad skupiny. 

>[!NOTE]
> Postupujte podle následujících pokynů fungují pouze u Internet Exploreru a Google Chrome na Windows (Pokud je sada adres URL důvěryhodných lokalit, která sdílí s aplikací Internet Explorer). Přečtěte si další části Pokyny o tom, jak nastavit Mozilla Firefox a Google Chrome v systému macOS.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Proč je třeba změnit nastavení zóny Intranet uživatele?

Ve výchozím nastavení prohlížeč automaticky vypočítá správné zóně, Internetu nebo intranetu, z konkrétní adresy URL. Například "http://contoso/"mapuje do zóny Intranet, zatímco"http://intranet.contoso.com/" mapuje na zóně Internet (vzhledem k tomu, že adresa URL obsahuje tečku). Prohlížeče nebude odesílat lístky protokolu Kerberos do koncového bodu cloudu, podobně jako adresu URL Azure AD, pokud explicitně přidat adresu URL do zóny intranetu prohlížeče.

Existují dva způsoby, jak upravit nastavení zóny Intranet uživatele:

| Možnost | Zvážení správce | Činnost koncového uživatele |
| --- | --- | --- |
| Zásady skupiny | Správce zámků dolů úpravy nastavení zóny Intranet | Uživatelé nemohou upravovat svoje vlastní nastavení |
| Předvoleb zásad skupiny |  Správce umožňuje úpravy nastavení zóny Intranet | Uživatelé mohou upravovat svoje vlastní nastavení |

### <a name="group-policy-option---detailed-steps"></a>Možnost "Zásady skupiny" – podrobný postup

1. Otevřete nástroj Editor správy zásad skupiny.
2. Upravit zásady skupiny, které se použije k některým nebo všem uživatelům. Tento příklad používá **výchozí zásady domény**.
3. Přejděte do **konfigurace uživatele** > **šablony pro správu** > **součásti Windows**  >   **Aplikace Internet Explorer** > **ovládací Panel Možnosti Internetu** > **stránku zabezpečení**. Potom vyberte **web k zóně Assignment List**.
    ![Jednotné přihlašování](./media/active-directory-aadconnect-sso/sso6.png)
4. Povolte zásady a pak v dialogovém okně zadejte následující hodnoty:
   - **Název hodnoty**: přesměrována lístky protokolu Kerberos adresu URL služby Azure AD.
   - **Hodnota** (Data): **1** označuje zóny intranetu.

    Výsledek vypadá takto:

    Hodnota: https://autologon.microsoftazuread-sso.com
  
    Data: 1

   >[!NOTE]
   > Pokud chcete zakázat některé uživatele pomocí bezproblémového jednotného přihlašování (například pokud tito uživatelé přihlásit na sdílené veřejné terminály), nastavte předchozí hodnoty na **4**. Tato akce přidá adresu URL služby Azure AD k zóně s omezeným přístupem a neustále se nezdaří bezproblémového jednotného přihlašování.
   >

5. Vyberte **OK**a pak vyberte **OK** znovu.

    ![Jednotné přihlašování](./media/active-directory-aadconnect-sso/sso7.png)

6. Přejděte do **konfigurace uživatele** > **šablony pro správu** > **součásti Windows**  >   **Aplikace Internet Explorer** > **ovládací Panel Možnosti Internetu** > **stránku zabezpečení** > **zóny intranetu**. Potom vyberte **povolit aktualizace stavového řádku prostřednictvím skriptu**.

    ![Jednotné přihlašování](./media/active-directory-aadconnect-sso/sso11.png)

7. Povolit nastavení zásady a pak vyberte **OK**.

    ![Jednotné přihlašování](./media/active-directory-aadconnect-sso/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>Možnost "Předvoleb zásad skupiny" – podrobný postup

1. Otevřete nástroj Editor správy zásad skupiny.
2. Upravit zásady skupiny, které se použije k některým nebo všem uživatelům. Tento příklad používá **výchozí zásady domény**.
3. Přejděte do **konfigurace uživatele** > **Předvolby** > **nastavení Windows** > **registru**  >  **Nový** > **položka registru**.

    ![Jednotné přihlašování](./media/active-directory-aadconnect-sso/sso15.png)

4. Do příslušných polí zadejte následující hodnoty a klikněte na tlačítko **OK**.
   - **Cesta klíče**: ***Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-sso.com\autologon***
   - **Název hodnoty**: ***https***.
   - **Typ hodnoty**: ***REG_DWORD***.
   - **Údaj hodnoty**: ***00000001***.
 
    ![Jednotné přihlašování](./media/active-directory-aadconnect-sso/sso16.png)
 
    ![Jednotné přihlašování](./media/active-directory-aadconnect-sso/sso17.png)

6. Přejděte do **konfigurace uživatele** > **šablony pro správu** > **součásti Windows**  >   **Aplikace Internet Explorer** > **ovládací Panel Možnosti Internetu** > **stránku zabezpečení** > **zóny intranetu**. Potom vyberte **povolit aktualizace stavového řádku prostřednictvím skriptu**.

    ![Jednotné přihlašování](./media/active-directory-aadconnect-sso/sso11.png)

7. Povolit nastavení zásady a pak vyberte **OK**.

    ![Jednotné přihlašování](./media/active-directory-aadconnect-sso/sso12.png)

### <a name="browser-considerations"></a>Důležité informace o prohlížeči

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (všechny platformy)

Mozilla Firefox nebude automaticky používat ověřování protokolem Kerberos. Každý uživatel musí ručně přidat adresu URL služby Azure AD jejich nastavení Firefox pomocí následujících kroků:
1. Spusťte Firefox a zadejte `about:config` do adresního řádku. Zavřete všechna naše oznámení, které se zobrazí.
2. Hledat **identifikátory URI network.negotiate auth.trusted** předvoleb. Tato předvolba obsahuje seznam důvěryhodných serverů pro Firefox pro ověřování protokolem Kerberos.
3. Klikněte pravým tlačítkem a vyberte **změnit**.
4. Zadejte https://autologon.microsoftazuread-sso.com v poli.
5. Vyberte **OK** a pak znovu otevřít prohlížeč.

#### <a name="safari-macos"></a>Safari (macOS)

Ujistěte se, že počítač s macOS připojený ke službě AD. Pokyny pro připojení AD, najdete v článku [osvědčené postupy pro OS X integraci se službou Active Directory](http://www.isaca.org/Groups/Professional-English/identity-management/GroupDocuments/Integrating-OS-X-with-Active-Directory.pdf).

#### <a name="google-chrome-all-platforms"></a>Google Chrome (všechny platformy)

Pokud mají přepsat [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) nebo [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) nastavení zásad ve vašem prostředí, ujistěte se, že můžete přidat adresu URL služby Azure AD (https://autologon.microsoftazuread-sso.com) k nim také.

#### <a name="google-chrome-macos-only"></a>Google Chrome (pouze v systému macOS)

Google Chrome na Mac OS a jiných platformách než Windows, najdete v tématu [chromu projektu Policy List](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) informace o tom, jak přidat na seznam povolených adresu URL služby Azure AD pro integrované ověřování.

Použití rozšíření zásad skupiny služby Active Directory třetích stran zavedení adresu URL služby Azure AD pro Firefox a Google Chrome na uživatele počítačů Mac je mimo rámec tohoto článku.

#### <a name="known-browser-limitations"></a>Omezení známé prohlížeče

Bezproblémové jednotné přihlašování nefunguje v privátním režimu procházení prohlížeče Firefox a hraničními zařízeními. Je také nebude fungovat v Internet Exploreru Pokud prohlížeč běží v rozšířené chráněný režim.

## <a name="step-4-test-the-feature"></a>Krok 4: Testování funkce

Otestovat funkci pro konkrétního uživatele, ujistěte se, že všechny následující podmínky jsou splněné:
  - Uživatel se přihlásí do podnikové zařízení.
  - Zařízení je připojené k vaší doméně služby Active Directory. Zařízení _nebude_ musí být [připojeno k Azure AD](../active-directory-azureadjoin-overview.md).
  - Zařízení má přímé připojení k řadiči domény (DC) v podnikové síti drátovou nebo bezdrátovou nebo prostřednictvím připojení vzdáleného přístupu, jako je například připojení k síti VPN.
  - Máte [zavádí funkci](##step-3-roll-out-the-feature) s tímto uživatelem prostřednictvím zásad skupiny.

K otestování tohoto scénáře, ve kterém uživatel zadá jenom uživatelské jméno, ale ne heslo:
   - Přihlaste se k https://myapps.microsoft.com/ v nové relaci privátního prohlížeče.

K otestování tohoto scénáře, ve kterém uživatel nemusí zadávat uživatelské jméno nebo heslo, použijte jeden z následujících kroků: 
   - Přihlaste se k https://myapps.microsoft.com/contoso.onmicrosoft.com v nové relaci privátního prohlížeče. Nahraďte *contoso* s názvem vašeho tenanta.
   - Přihlaste se k https://myapps.microsoft.com/contoso.com v nové relaci privátního prohlížeče. Nahraďte *contoso.com* s ověřenou doménou (nikoli do federované domény) ve svém tenantovi.

## <a name="step-5-roll-over-keys"></a>Krok 5: Nespotřebujete klíče

V kroku 2 vytvoří účty počítačů (reprezentace služby Azure AD) ve všech struktur služby Active Directory, na které jste povolili bezproblémového jednotného přihlašování Azure AD Connect. Další informace najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování: podrobné technické informace](active-directory-aadconnect-sso-how-it-works.md).

>[!IMPORTANT]
>Dešifrovací klíč protokolu Kerberos na účet počítače, pokud úniku, lze použít ke generování lístky protokolu Kerberos pro všechny uživatele v doménové struktuře AD. Útočníky můžete poté k zosobnění přihlášení ohrožených uživatelů Azure AD. Důrazně doporučujeme pravidelná obměna přes tyto dešifrovací klíče Kerberos – alespoň jednou za 30 dnů.

Pokyny k výměně klíčů najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování: Nejčastější dotazy k](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account). Pracujeme na schopnost zavádět automatické vrácení přes klíčů.

>[!IMPORTANT]
>Není nutné tento krok _okamžitě_ budete mít povolenou funkci. Ho znovu vygenerovat dešifrovací klíče Kerberos alespoň jednou za 30 dnů.

## <a name="next-steps"></a>Další postup

- [Podrobné technické informace](active-directory-aadconnect-sso-how-it-works.md): pochopit, jak funguje funkce bezproblémové jednotné přihlašování.
- [Nejčastější dotazy k](active-directory-aadconnect-sso-faq.md): Získejte odpovědi na nejčastější dotazy o bezproblémové jednotné přihlašování.
- [Řešení potíží s](active-directory-aadconnect-troubleshoot-sso.md): Zjistěte, jak řešit běžné problémy s funkcí bezproblémové jednotné přihlašování.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): použijte fórum Azure Active Directory do souboru žádostí o nové funkce.
