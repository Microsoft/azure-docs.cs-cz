---
title: "Azure AD Connect: Bezproblémové Single Sign-On - úvodní | Microsoft Docs"
description: "Tento článek popisuje, jak začít pracovat s Azure Active Directory bezproblémové jednotné přihlašování"
services: active-directory
keywords: "Co je Azure AD Connect, instalace služby Active Directory, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: billmath
ms.openlocfilehash: 58ca992f9fcf9a03d917f0dc250a292c4d5f49e5
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2018
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Azure Active Directory bezproblémové jednotné přihlašování: rychlý start

## <a name="deploy-seamless-single-sign-on"></a>Nasazení bezproblémové jednotné přihlašování

Azure Active Directory (Azure AD) bezproblémové jednotné přihlašování (SSO bezproblémové) automaticky přihlásí uživatele při práci na podnikové stolní počítače, které jsou připojené k vaší podnikové síti. Bezproblémové SSO poskytuje uživatelům snadný přístup k vaší cloudové aplikace bez nutnosti jakékoli další místní komponenty.

Pokud chcete nasadit bezproblémové jednotné přihlašování, postupujte takto.

## <a name="step-1-check-the-prerequisites"></a>Krok 1: Kontrola předpokladů

Ujistěte se, že jsou splněné následující požadavky:

* **Nastavit server Azure AD Connect**: Pokud používáte [předávací ověřování](active-directory-aadconnect-pass-through-authentication.md) jako způsob přihlášení je požadovaná žádné další kontrolu požadovaných součástí. Pokud používáte [synchronizaci hodnoty hash hesla](active-directory-aadconnectsync-implement-password-synchronization.md) jako způsob přihlášení a pokud je brána firewall mezi Azure AD Connect a službou Azure AD, ověřte, že:
   - Používáte verzi 1.1.644.0 nebo novější služby Azure AD Connect. 
   - Pokud brána firewall nebo proxy server umožňuje povolených DNS, povolených připojení k  **\*. msappproxy.net** adresy URL přes port 443. Pokud ne, povolit přístup k [rozsahy IP adres Azure datacenter](https://www.microsoft.com/download/details.aspx?id=41653), se aktualizují každý týden. Tento požadavek se vztahuje pouze v případě, že povolíte funkci. Není nutné pro skutečné uživatelská přihlášení.

    >[!NOTE]
    >Azure AD Connect verze 1.1.557.0, 1.1.558.0, 1.1.561.0 a 1.1.614.0 mít problém související s synchronizaci hodnoty hash hesla. Pokud jste _nemáte_ v úmyslu použít synchronizaci hodnoty hash hesla ve spojení se předávací ověřování, přečtěte si [poznámky k verzi Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) Další informace.

* **Nastavení přihlašovacích údajů správce domény**: musíte mít pověření správce domény pro každou služby Active Directory doménové struktury, která:
    * Můžete synchronizovat do Azure AD přes Azure AD Connect.
    * Obsahuje uživatele, které chcete povolit pro bezproblémové jednotné přihlašování.

## <a name="step-2-enable-the-feature"></a>Krok 2: Povolení funkce

Povolení bezproblémové jednotného přihlašování prostřednictvím [Azure AD Connect](active-directory-aadconnect.md).

Pokud provádíte novou instalací služby Azure AD Connect, vyberte [vlastní cesta](active-directory-aadconnect-get-started-custom.md). Na **přihlášení uživatele** vyberte **povolení jednotného přihlašování na** možnost.

![Azure AD Connect: Přihlášení uživatele](./media/active-directory-aadconnect-sso/sso8.png)

Pokud již máte instalace služby Azure AD Connect, vyberte **změnit přihlášení uživatele** v Azure AD Connect a pak vyberte **Další**.

![Azure AD Connect: Změňte přihlášení uživatele](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Postupujte podle pokynů Průvodce až na **povolení jednotného přihlašování na** stránky. Zadejte, že pověření správce domény pro každou službu Active Directory doménové struktury, která:
    * Můžete synchronizovat do Azure AD přes Azure AD Connect.
    * Obsahuje uživatele, které chcete povolit pro bezproblémové jednotné přihlašování.

Po dokončení průvodce je povoleno bezproblémové jednotného přihlašování na klientovi.

>[!NOTE]
> Pověření správce domény nejsou uložené ve službě Azure AD Connect nebo ve službě Azure AD. Používají se pouze k povolení této funkce.

Postupujte podle těchto pokynů k ověření, že je povoleno jednotné přihlašování bezproblémové správně:

1. Přihlaste se k [Centrum správy služby Azure Active Directory](https://aad.portal.azure.com) pomocí přihlašovacích údajů globálního správce pro vašeho klienta.
2. Vyberte **Azure Active Directory** v levém podokně.
3. Vyberte **Azure AD Connect**.
4. Ověřte, zda **bezproblémové jednotné přihlašování** funkce se zobrazuje jako **povoleno**.

![Portál Azure: podokně Azure AD Connect](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="step-3-roll-out-the-feature"></a>Krok 3: Přejít na funkci

K zavedení funkci pro vaše uživatele, je nutné přidat následující adresy URL Azure AD k nastavení zóny intranetu uživatelů pomocí zásad skupiny ve službě Active Directory:

- https://autologon.microsoftazuread-sso.com


Kromě toho je nutné povolit zásadu zóny intranetu názvem **povolit aktualizace stavového řádku pomocí skriptu** prostřednictvím zásad skupiny. 

>[!NOTE]
> Podle následujících pokynů fungovat pouze pro Internet Explorer a Google Chrome v systému Windows (pokud ji sdílí sadu adres URL důvěryhodných serverů s aplikací Internet Explorer). Přečtěte si další části pokyny, jak nastavit Mozilla Firefox a Google Chrome na macu.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Proč je třeba změnit nastavení zóny intranetu uživatelů?

Ve výchozím prohlížeči automaticky vypočítá správné zóně, Internetu nebo intranetu, z konkrétní adresy URL. Například "http://contoso/" mapuje do zóny intranetu, zatímco "http://intranet.contoso.com/" mapuje zóně Internet (protože adresa URL obsahuje tečku). Prohlížeče nebude odesílají lístky protokolu Kerberos koncového bodu cloudu, jako je Azure AD adresu URL, pokud explicitně přidat adresu URL do zóny intranetu prohlížeče.

### <a name="detailed-steps"></a>Podrobné kroky

1. Otevřete nástroj Editor správy zásad skupiny.
2. Upravit zásady skupiny, které se použije k některým nebo všem uživatelům. Tento příklad používá **výchozí zásady domény**.
3. Přejděte do **konfigurace uživatele** > **šablony pro správu** > **součásti systému Windows**  >   **Internet Explorer** > **ovládací Panel Možnosti Internetu** > **stránka zabezpečení**. Potom vyberte **webu do zóny přiřazení seznamu**.
    ![Jednotné přihlašování](./media/active-directory-aadconnect-sso/sso6.png)
4. Je nutné povolit zásady a pak v dialogovém okně zadejte následující hodnoty:
   - **Název hodnoty**: Azure AD adresu URL, které jsou předávány lístky protokolu Kerberos.
   - **Hodnota** (Data): **1** označuje zóny intranetu.

    Výsledek vypadá takto:

    Hodnota: https://autologon.microsoftazuread-sso.com
  
    Data: 1

   >[!NOTE]
   > Pokud chcete zakázat některé uživatele pomocí bezproblémové jednotné přihlašování (například pokud tito uživatelé přihlásit na sdílené veřejné terminály), nastavte předchozí hodnoty na **4**. Tato akce přidá adresu URL pro Azure AD s omezeným přístupem zóny a selže vždy bezproblémové jednotné přihlašování.
   >

5. Vyberte **OK**a potom vyberte **OK** znovu.

    ![Jednotné přihlašování](./media/active-directory-aadconnect-sso/sso7.png)

6. Přejděte do **konfigurace uživatele** > **šablony pro správu** > **součásti systému Windows**  >   **Internet Explorer** > **ovládací Panel Možnosti Internetu** > **stránka zabezpečení** > **zóny intranetu**. Potom vyberte **povolit aktualizace stavového řádku pomocí skriptu**.

    ![Jednotné přihlašování](./media/active-directory-aadconnect-sso/sso11.png)

7. Povolit nastavení zásady a pak vyberte **OK**.

    ![Jednotné přihlašování](./media/active-directory-aadconnect-sso/sso12.png)

### <a name="browser-considerations"></a>Důležité informace o prohlížeči

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (všechny platformy)

Mozilla Firefox nebude automaticky používat ověřování protokolu Kerberos. Každý uživatel musí ručně přidat adresu URL pro Azure AD jejich nastavení Firefox pomocí následujících kroků:
1. Spustit Firefox a zadejte `about:config` na panelu Adresa. Zavřete všechny oznámení, které vidíte.
2. Vyhledejte **identifikátory URI network.negotiate auth.trusted** předvoleb. Tato předvolba obsahuje seznam důvěryhodných serverů na Firefox ověřování protokolem Kerberos.
3. Klikněte pravým tlačítkem a vyberte **upravit**.
4. Zadejte https://autologon.microsoftazuread-sso.com v poli.
5. Vyberte **OK** a znovu otevřete v prohlížeči.

#### <a name="safari-mac-os"></a>Safari (Mac OS)

Ujistěte se, že počítač se systémem Mac OS je připojený k Azure AD. Pokyny týkající se připojení Azure AD, najdete v části [osvědčené postupy pro OS X integraci se službou Active Directory](http://training.apple.com/pdf/Best_Practices_for_Integrating_OS_X_with_Active_Directory.pdf).

#### <a name="google-chrome-all-platforms"></a>Google Chrome (všechny platformy)

Pokud máte elementem [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) nebo [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) nastavení zásad ve vašem prostředí, ujistěte se, že přidáte adresu URL pro Azure AD (https://autologon.microsoftazuread-sso.com) k nim také.

#### <a name="google-chrome-mac-os-only"></a>Google Chrome (pouze Mac OS)

Google Chrome na ostatní platformy jiný systém než Windows a Mac OS, najdete v části [seznamu zásad projektu chromu](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) informace o tom, jak do seznamu povolených IP adres URL služby Azure AD pro integrované ověřování.

Použití zásad skupiny služby Active Directory rozšíření třetích stran, chcete-li vrátit na adresu URL pro Azure AD a Firefox, Google Chrome na uživatele počítačů Mac je mimo rámec tohoto článku.

#### <a name="known-browser-limitations"></a>Omezení známé prohlížeče

Bezproblémové SSO nefunguje v privátním režimu procházení na prohlížeče Firefox a okraj. Také nefunguje v Internet Exploreru Pokud prohlížeče běží v rozšířené chráněný režim.

## <a name="step-4-test-the-feature"></a>Krok 4: Testování funkce

Chcete-li otestovat funkci pro konkrétního uživatele, ujistěte se, že jsou splněné všechny následující podmínky:
  - Uživatel se přihlásí na firemní zařízení.
  - Zařízení je připojené k vaší doméně služby Active Directory.
  - Zařízení má přímé připojení k řadiči domény (DC) v podnikové síti pevné nebo bezdrátové sítě nebo prostřednictvím připojení vzdáleného přístupu, jako je například připojení k síti VPN.
  - Máte [nasazuje funkci](##step-3-roll-out-the-feature) s tímto uživatelem prostřednictvím zásad skupiny.

K otestování tohoto scénáře, kde uživatel zadá pouze uživatelské jméno, ale ne heslo:
   - Přihlaste se k https://myapps.microsoft.com/ v nové relaci privátní prohlížeče.

K otestování tohoto scénáře, kde uživatel nemusí zadávat uživatelské jméno nebo heslo, použijte jednu z těchto kroků: 
   - Přihlaste se k https://myapps.microsoft.com/contoso.onmicrosoft.com v nové relaci privátní prohlížeče. Nahraďte *contoso* s názvem vašeho klienta.
   - Přihlaste se k https://myapps.microsoft.com/contoso.com v nové relaci privátní prohlížeče. Nahraďte *contoso.com* s ověřenou doménu (nikoli federované domény) na klientovi.

## <a name="step-5-roll-over-keys"></a>Krok 5: Vrácení nad klíči

V kroku 2 vytvoří Azure AD Connect účty počítačů (představující Azure AD) ve všech struktur služby Active Directory, na kterých jste povolili bezproblémové jednotné přihlašování. Další informace najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování: podrobné technické informace](active-directory-aadconnect-sso-how-it-works.md). Pro lepší zabezpečení doporučujeme, aby pravidelně nezaregistrujete prostřednictvím protokolu Kerberos dešifrovací klíče tyto účty počítačů. Pokyny o tom, jak vrátit nad klíči najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování: Nejčastější dotazy](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account).

>[!IMPORTANT]
>Nemusíte proveďte tento krok _okamžitě_ poté, co povolíte funkci. Mění dešifrovací klíče protokolu Kerberos alespoň jednou za 30 dní.

## <a name="next-steps"></a>Další postup

- [Podrobné technické informace](active-directory-aadconnect-sso-how-it-works.md): pochopit, jak funkce bezproblémové jednotné přihlašování funguje.
- [Nejčastější dotazy](active-directory-aadconnect-sso-faq.md): získáte odpovědi na nejčastější dotazy o bezproblémové jednotné přihlašování.
- [Řešení potíží s](active-directory-aadconnect-troubleshoot-sso.md): Přečtěte si o řešení běžných problémů s funkcí bezproblémové jednotné přihlašování.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): použijte fóru Azure Active Directory do souboru žádosti o nové funkce.
