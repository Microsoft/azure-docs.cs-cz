---
title: 'Azure AD Connect: Bezproblémové jednotné přihlašování – rychlý start | Dokumenty společnosti Microsoft'
description: Tento článek popisuje, jak začít s bezproblémovým jednotném přihlašování služby Azure Active Directory.
services: active-directory
keywords: co je Azure AD Connect, nainstalujte službu Active Directory, požadované součásti pro Azure AD, Jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ec56d37ca2c0a199968707b3d93f4797be2beca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261200"
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Bezproblémové jednotné přihlašování služby Azure Active Directory: Rychlý start

## <a name="deploy-seamless-single-sign-on"></a>Nasazení bezproblémového jednotného přihlašování

Azure Active Directory (Azure AD) Bezproblémové jednotné přihlašování (bezproblémové jednotné přihlašování) automaticky přihlásí uživatele, když jsou na svých podnikových desktopů, které jsou připojené k podnikové síti. Bezproblémové jednotné přihlašování poskytuje uživatelům snadný přístup k vašim cloudovým aplikacím bez nutnosti dalších místních komponent.

Chcete-li nasadit bezproblémové jednotné přihlašování, postupujte takto.

## <a name="step-1-check-the-prerequisites"></a>Krok 1: Kontrola předpokladů

Ujistěte se, že jsou na místě následující požadavky:

* **Nastavení serveru Azure AD Connect:** Pokud jako metodu přihlášení použijete [předávací ověřování,](how-to-connect-pta.md) není vyžadována žádná další kontrola předpokladů. Pokud jako metodu přihlášení používáte [synchronizaci hodnot hash hesla](how-to-connect-password-hash-synchronization.md) a mezi Službou Azure AD Connect a Službou Azure AD existuje brána firewall, ujistěte se, že:
   - Používáte verzi 1.1.644.0 nebo novější služby Azure AD Connect. 
   - Pokud brána firewall nebo proxy server umožňuje seznam povolených adres DNS, zadejte seznam povolených adres ** \*.msappproxy.net** adres URL přes port 443. Pokud ne, povolte přístup k [rozsahům IP adres datového centra Azure](https://www.microsoft.com/download/details.aspx?id=41653), které se aktualizují každý týden. Tento předpoklad je použitelný pouze v případě, že povolíte funkci. Není vyžadována pro skutečné přihlášení uživatele.

    >[!NOTE]
    >Azure AD Connect verze 1.1.557.0, 1.1.558.0, 1.1.561.0 a 1.1.614.0 mají problém související se synchronizací hash hesel. Pokud _nemáte_ v úmyslu používat synchronizaci hash hesla ve spojení s předávacím ověřováním, přečtěte si [poznámky k verzi Azure AD Connect,](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) kde se dozvíte další informace.

* **Použití podporované topologie Azure AD Connect**: Ujistěte se, že používáte některou z podporovaných topologie Azure AD Connect popsané [zde](plan-connect-topologies.md).

    >[!NOTE]
    >Bezproblémové jednotné přihlašované zabezpečení podporuje více doménových struktur ad, bez ohledu na to, zda mezi nimi existují vztahy důvěryhodnosti ad.

* **Nastavení pověření správce domény**: Pro každou doménovou strukturu služby Active Directory je třeba mít pověření správce domény, která:
    * Synchronizovat se službou Azure AD prostřednictvím služby Azure AD Connect.
    * Obsahuje uživatele, které chcete povolit pro bezproblémové jednotné přihlašovat.
    
* **Povolit moderní ověřování**: Chcete-li, aby tato funkce fungovala, musíte povolit [moderní ověřování](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) v tenantovi.

* **Použití nejnovějších verzí klientů Office 365**: Chcete-li získat tiché přihlašovací prostředí s klienty Office 365 (Outlook, Word, Excel a další), uživatelé musí používat verze 16.0.8730.xxxx nebo vyšší.

## <a name="step-2-enable-the-feature"></a>Krok 2: Povolení funkce

Povolit bezproblémové jednotné přihlašovací služby prostřednictvím [služby Azure AD Connect](whatis-hybrid-identity.md).

>[!NOTE]
> Můžete také [povolit bezproblémové jednotné přihlašování pomocí PowerShellu,](tshoot-connect-sso.md#manual-reset-of-the-feature) pokud Azure AD Connect nesplňuje vaše požadavky. Tuto možnost použijte, pokud máte více než jednu doménu v jedné doménové struktuře služby Active Directory a chcete být cílenější ohledně domény, pro kterou chcete povolit bezproblémové jednotné přihlašování.

Pokud provádíte novou instalaci služby Azure AD Connect, zvolte [vlastní cestu k instalaci](how-to-connect-install-custom.md). Na stránce **Přihlášení uživatele** vyberte možnost **Povolit jednotné přihlašování.**

>[!NOTE]
> Tato možnost bude k dispozici pro výběr pouze v případě, že metoda Přihlášení je **synchronizace hodnot hash hesla** nebo **předávací ověřování**.

![Azure AD Connect: Přihlášení uživatele](./media/how-to-connect-sso-quick-start/sso8.png)

Pokud už máte instalaci Azure AD Connect, vyberte změnit přihlašovací **stránku uživatele** v Azure AD Connect a pak vyberte **Další**. Pokud používáte Azure AD Connect verze 1.1.880.0 nebo vyšší, možnost **Povolit jednotné přihlašování** bude vybrána ve výchozím nastavení. Pokud používáte starší verze Azure AD Connect, vyberte možnost **Povolit jednotné přihlašování.**

![Azure AD Connect: Změna přihlášení uživatele](./media/how-to-connect-sso-quick-start/changeusersignin.png)

Pokračujte průvodcem, dokud se nedostanete na stránku **Povolit jednotné přihlášení.** Zadejte pověření správce domény pro každou doménovou strukturu služby Active Directory, která:

* Synchronizovat se službou Azure AD prostřednictvím služby Azure AD Connect.
* Obsahuje uživatele, které chcete povolit pro bezproblémové jednotné přihlašovat.

Po dokončení průvodce bezproblémové jednotné přihlašování je povoleno na vašem tenantovi.

>[!NOTE]
> Přihlašovací údaje správce domény se neukládají ve službě Azure AD Connect nebo ve službě Azure AD. Používají se pouze k povolení funkce.

Podle těchto pokynů ověřte, zda jste správně povolili bezproblémové jednotné přihlašování:

1. Přihlaste se do [centra správy Služby Azure Active Directory](https://aad.portal.azure.com) s přihlašovacími údaji globálního správce pro vašeho tenanta.
2. V levém podokně vyberte **Službu Azure Active Directory.**
3. Vyberte **Azure AD Connect**.
4. Ověřte, zda se funkce **bezešvého jednotného přihlašování** zobrazuje jako **povolená**.

![Portál Azure: Podokno Azure AD Connect](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> Bezproblémové jednotné přihlašování vytvoří účet počítače pojmenovaný `AZUREADSSOACC` v místní službě Active Directory (AD) v každé doménové struktuře služby AD. Účet `AZUREADSSOACC` počítače musí být z bezpečnostních důvodů silně chráněn. Účet počítače by měli spravovat pouze správci domény. Ujistěte se, že delegování protokolu Kerberos v účtu počítače je `AZUREADSSOACC` zakázáno a že žádný jiný účet ve službě Active Directory nemá oprávnění k delegování účtu počítače. Uložte účet počítače do organizační jednotky (OU), kde jsou v bezpečí před náhodným odstraněním a kde mají přístup pouze správci domény.

>[!NOTE]
> Pokud používáte architektury Pass-the-Hash a Credential Theft Mitigation v místním prostředí, proveďte příslušné změny, abyste `AZUREADSSOACC` zajistili, že účet počítače neskončí v kontejneru karantény. 

## <a name="step-3-roll-out-the-feature"></a>Krok 3: Zavedení funkce

Pomocí níže uvedených pokynů můžete uživatelům postupně zavést bezproblémové jednotné přihlašování. Začnete přidáním následující adresy URL Azure AD do nastavení zóny intranetu všech nebo vybraných uživatelů pomocí zásad skupiny ve službě Active Directory:

- `https://autologon.microsoftazuread-sso.com`

Kromě toho je třeba povolit nastavení zásad zóny intranetu s názvem **Povolit aktualizace stavového řádku prostřednictvím skriptu** prostřednictvím zásad skupiny. 

>[!NOTE]
> Následující pokyny fungují pouze pro aplikaci Internet Explorer a Google Chrome v systému Windows (pokud sdílí sadu důvěryhodných adres URL webu s aplikací Internet Explorer). Přečtěte si další část, kde najdete pokyny, jak nastavit Mozilla Firefox a Google Chrome v systému macOS.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Proč je třeba upravit nastavení intranetové zóny uživatelů?

Ve výchozím nastavení prohlížeč automaticky vypočítá správnou zónu, internet nebo intranet, z určité adresy URL. Například `http://contoso/` mapuje na intranetovou `http://intranet.contoso.com/` zónu, zatímco mapuje se na zónu Internet (protože adresa URL obsahuje tečku). Prohlížeče nebudou odesílat lístky protokolu Kerberos do koncového bodu cloudu, jako je adresa URL Azure AD, pokud explicitně nepřidáte adresu URL do zóny intranetu prohlížeče.

Nastavení intranetové zóny uživatelů lze upravit dvěma způsoby:

| Možnost | Admin úvahy | Uživatelské prostředí |
| --- | --- | --- |
| Zásady skupiny | Správce uzamkne úpravy nastavení zóny Intranet | Uživatelé nemohou měnit vlastní nastavení. |
| Předvolba zásad skupiny |  Admin umožňuje úpravy v nastavení zóny Intranet | Uživatelé mohou změnit své vlastní nastavení |

### <a name="group-policy-option---detailed-steps"></a>Možnost Zásady skupiny – podrobné kroky

1. Otevřete nástroj Editor správy zásad skupiny.
2. Upravte zásady skupiny, které se vztahují na některé nebo všechny uživatele. Tento příklad používá **výchozí zásady domény**.
3. Přejděte na > **stránku zabezpečení uživatelských** >  **konfiguračních** > **zásad** > **pro správu šablony****součásti aplikace Windows Components** > **Internet Explorer** > **.** Potom vyberte **možnost Web do seznamu přiřazení zóny**.
    ![Jednotné přihlašování](./media/how-to-connect-sso-quick-start/sso6.png)
4. Povolte zásadu a do dialogového okna zadejte následující hodnoty:
   - **Název hodnoty**: Adresa URL Azure AD, kde jsou předávány lístky protokolu Kerberos.
   - **Hodnota** (data): **1** označuje zónu Intranet.

     Výsledek vypadá takto:

     Název hodnoty:`https://autologon.microsoftazuread-sso.com`
  
     Hodnota (data): 1

   >[!NOTE]
   > Pokud chcete zakázat některým uživatelům používat bezproblémové jednotné přihlašování (například pokud se tito uživatelé přihlašují ke sdíleným kioskům), nastavte předchozí hodnoty na **4**. Tato akce přidá adresu URL Azure AD do zóny s omezeným přístupem a nezdaří bezproblémové jednotné přihlašování po celou dobu.
   >

5. Vyberte **OK** a potom znovu vyberte **OK**.

    ![Jednotné přihlašování](./media/how-to-connect-sso-quick-start/sso7.png)

6. Přejděte do části**Policy** > **Intranetové stránky** > **intranetové**stránky**intranet** > ové stránky Zásady **konfigurace** > **uživatele Windows Components** > **Internet Explorer** > **Internet Control Panel** > . Pak vyberte **Povolit aktualizace stavového řádku pomocí skriptu**.

    ![Jednotné přihlašování](./media/how-to-connect-sso-quick-start/sso11.png)

7. Povolte nastavení zásad a pak vyberte **OK**.

    ![Jednotné přihlašování](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>Možnost "Předvolba zásad skupiny" – podrobné kroky

1. Otevřete nástroj Editor správy zásad skupiny.
2. Upravte zásady skupiny, které se vztahují na některé nebo všechny uživatele. Tento příklad používá **výchozí zásady domény**.
3. Přejděte do**části Předvolby** >  **konfigurace** > uživatele**Windows Settings** > **Registry** > **New** > **Registry Item .**

    ![Jednotné přihlašování](./media/how-to-connect-sso-quick-start/sso15.png)

4. Do příslušných polí zadejte následující hodnoty a klepněte na **tlačítko OK**.
   - **Cesta klávesy**: ***Software\Microsoft\Windows\CurrentVersion\Nastavení Internetu\ZoneMap\Domains\microsoftazuread-sso.com\autologon***
   - **Název hodnoty**: ***https***.
   - **Typ hodnoty**: ***REG_DWORD***.
   - **Hodnotové údaje**: ***00000001***.
 
     ![Jednotné přihlašování](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![Jednotné přihlašování](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>Důležité informace o prohlížeči

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (všechny platformy)

Mozilla Firefox nepoužívá automaticky ověřování protokolem Kerberos. Každý uživatel musí ručně přidat adresu URL Azure AD do nastavení Firefoxu pomocí následujících kroků:
1. Spusťte `about:config` Firefox a zadejte do adresního řádku. Zavřete všechna oznámení, která se zobrazí.
2. Vyhledejte předvolbu **network.negotiate-auth.trusted-uris.** Tato předvolba uvádí důvěryhodné weby Firefoxu pro ověřování protokolem Kerberos.
3. Klepněte pravým tlačítkem myši a vyberte **příkaz Změnit**.
4. Zadejte `https://autologon.microsoftazuread-sso.com` do pole.
5. Vyberte **OK** a pak znovu otevřete prohlížeč.

#### <a name="safari-macos"></a>Safari (macOS)

Ujistěte se, že je počítač s macOS připojen ke společnosti AD. Pokyny pro připojení ad zařízení macOS je mimo rozsah tohoto článku.

#### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge založený na chromu (všechny platformy)

Pokud jste přepsali [AuthNegotiateDelegateAllowlist](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authnegotiatedelegateallowlist) nebo [AuthServerAllowlist](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authserverallowlist) nastavení zásad ve vašem prostředí, ujistěte`https://autologon.microsoftazuread-sso.com`se, že přidáte adresu URL Azure AD ( ) k nim také.

#### <a name="microsoft-edge-based-on-chromium-macos-and-other-non-windows-platforms"></a>Microsoft Edge založený na chromu (macOS a další platformy, které nejsou windows)

Pro Microsoft Edge na bázi Chromium na Mac OS a jiné platformy než Windows, naleznete [v Microsoft Edge na základě seznamu zásad chromium](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authserverallowlist) informace o tom, jak přidat adresu URL Azure AD pro integrované ověřování do seznamu povolených.

#### <a name="google-chrome-all-platforms"></a>Google Chrome (všechny platformy)

Pokud jste přepsali [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) nebo [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) nastavení zásad ve vašem prostředí, ujistěte`https://autologon.microsoftazuread-sso.com`se, že přidáte adresu URL Azure AD ( ) k nim také.

#### <a name="google-chrome-macos-and-other-non-windows-platforms"></a>Google Chrome (macOS a jiné platformy, které nejsou windows)

V případě prohlížeče Google Chrome v systému Mac OS a dalších platformách, které nejsou součástí windows, najdete v [seznamu zásad projektu Chromium,](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) kde najdete informace o tom, jak zařadit adresu URL Azure AD pro integrované ověřování.

Použití rozšíření zásad skupiny služby Active Directory třetích stran k zavedení adresy URL Azure AD pro uživatele Firefoxu a Google Chrome na Macu je mimo rozsah tohoto článku.

#### <a name="known-browser-limitations"></a>Známá omezení prohlížeče

Bezproblémové jednotné přihlašování nefunguje v režimu anonymního prohlížení v prohlížečích Firefox a Microsoft Edge. V aplikaci Internet Explorer také nefunguje, pokud je prohlížeč spuštěn v rozšířeném chráněném režimu. Pro další verzi Microsoft Edge založené na Chromium, nebude fungovat v inprivate a host režimu podle návrhu.

## <a name="step-4-test-the-feature"></a>Krok 4: Testování funkce

Chcete-li otestovat funkci pro konkrétního uživatele, ujistěte se, že jsou na místě všechny následující podmínky:
  - Uživatel se přihlásí na podnikovém zařízení.
  - Zařízení je připojeno k doméně služby Active Directory. Zařízení _nemusí_ být azure [ad připojen .](../active-directory-azureadjoin-overview.md)
  - Zařízení má přímé připojení k řadiči domény (DC), buď v podnikové kabelové nebo bezdrátové síti, nebo prostřednictvím připojení vzdáleného přístupu, například připojení VPN.
  - Tuto funkci jste pro tohoto uživatele [zavedli](#step-3-roll-out-the-feature) prostřednictvím zásad skupiny.

Chcete-li otestovat scénář, kdy uživatel zadá pouze uživatelské jméno, ale ne heslo:
   - Přihlaste `https://myapps.microsoft.com/` se k nové relaci soukromého prohlížeče.

Chcete-li otestovat scénář, kdy uživatel nemusí zadávat uživatelské jméno nebo heslo, použijte jeden z těchto kroků: 
   - Přihlaste `https://myapps.microsoft.com/contoso.onmicrosoft.com` se k nové relaci soukromého prohlížeče. Nahraďte *contoso* jménem svého tenanta.
   - Přihlaste `https://myapps.microsoft.com/contoso.com` se k nové relaci soukromého prohlížeče. Nahraďte *contoso.com* ověřenou doménou (ne federovovo) v tenantovi.

## <a name="step-5-roll-over-keys"></a>Krok 5: Převrácení kláves

V kroku 2 Azure AD Connect vytvoří účty počítačů (představující Azure AD) ve všech doménových strukturách služby Active Directory, na kterých jste povolili bezproblémové jednotné přihlašování. Další informace najdete [v tématu Bezproblémové jednotné přihlašování služby Azure Active Directory: Podrobné technické informace](how-to-connect-sso-how-it-works.md).

>[!IMPORTANT]
>Dešifrovací klíč kerberos v účtu počítače, pokud unikly, lze použít ke generování lístků protokolu Kerberos pro libovolného uživatele v doménové struktuře služby AD. Škodlivé objekty actor pak můžete zosobnit přihlášení Azure AD pro ohrožené uživatele. Důrazně doporučujeme, abyste tyto dešifrovací klíče kerberos pravidelně přejíždí – alespoň jednou za 30 dní.

Pokyny k převrácení klíčů najdete v [tématu Bezproblémové jednotné přihlašování služby Azure Active Directory: Nejčastější dotazy](how-to-connect-sso-faq.md). Pracujeme na schopnosti zavést automatické převrácení klíčů.

>[!IMPORTANT]
>Tento krok není nutné provést _ihned_ po povolení funkce. Převrácení dešifrovacích klíčů protokolu Kerberos alespoň jednou za 30 dní.

## <a name="next-steps"></a>Další kroky

- [Technické podrobné informace](how-to-connect-sso-how-it-works.md): Zjistěte, jak funguje funkce bezproblémového jednotného přihlašování.
- [Nejčastější dotazy](how-to-connect-sso-faq.md): Získejte odpovědi na nejčastější dotazy týkající se bezproblémového jednotného přihlašování.
- [Poradce při potížích](tshoot-connect-sso.md): Přečtěte si, jak vyřešit běžné problémy s funkcí Bezproblémové jednotné přihlašování.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Fórum Azure Active Directory můžete zasít nové požadavky na funkce.
