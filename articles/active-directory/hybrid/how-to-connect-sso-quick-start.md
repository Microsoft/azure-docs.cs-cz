---
title: 'Azure AD Connect: Bezproblémové jednotné přihlašování – rychlý Start | Microsoft Docs'
description: Tento článek popisuje, jak začít s Azure Active Directory bezproblémové jednotné přihlašování.
services: active-directory
keywords: Co je Azure AD Connect, instalace služby Active Directory, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování
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
ms.openlocfilehash: 29f94d6ff8045b7cae64957eeae00d2460ca3e37
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2019
ms.locfileid: "71176836"
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Azure Active Directory bezproblémové jednotné přihlašování: Rychlý start

## <a name="deploy-seamless-single-sign-on"></a>Nasazení bezproblémového jednotného přihlašování

Azure Active Directory (Azure AD) bezproblémové jednotné přihlašování (bezproblémové jednotné přihlašování) se automaticky přihlásí k podnikovým plochám, které jsou připojené k podnikové síti. Bezproblémové jednotné přihlašování poskytuje uživatelům snadný přístup k vašim cloudovým aplikacím bez nutnosti dalších místních komponent.

Chcete-li nasadit bezproblémové jednotné přihlašování, postupujte podle těchto kroků.

## <a name="step-1-check-the-prerequisites"></a>Krok 1: Kontrola požadavků

Ujistěte se, že jsou splněné následující požadavky:

* **Nastavení serveru Azure AD Connect**: Pokud jako metodu přihlašování použijete [předávací ověřování](how-to-connect-pta.md) , nevyžaduje se žádná další Kontrola požadovaných součástí. Pokud jako metodu přihlašování použijete [synchronizaci hodnot hash hesel](how-to-connect-password-hash-synchronization.md) , a pokud je mezi Azure AD Connect a službou Azure AD brána firewall, zajistěte, aby:
   - Použijete 1.1.644.0 nebo novější verzi Azure AD Connect. 
   - Pokud brána firewall nebo proxy umožňuje přidávání do seznamu povolených serverů DNS, seznam  **\*** povolených připojení k adresám URL. msappproxy.NET přes port 443. V takovém případě povolte přístup k [rozsahům IP adres datacentra Azure](https://www.microsoft.com/download/details.aspx?id=41653), které se aktualizují týdně. Tato požadovaná součást platí pouze v případě, že funkci povolíte. Nepožaduje se pro vlastní přihlášení uživatelů.

    >[!NOTE]
    >Azure AD Connect verze 1.1.557.0, 1.1.558.0, 1.1.561.0 a 1.1.614.0 mají problém týkající se synchronizace hodnot hash hesel. Pokud nehodláte použít synchronizaci hodnot hash hesel ve spojení s předávacím ověřováním, přečtěte si [poznámky k verzi Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) , _kde najdete další_ informace.

* **Použít podporované topologie Azure AD Connect**: Ujistěte se, že používáte jednu z podporovaných topologií, které jsou [zde](plan-connect-topologies.md)popsané Azure AD Connect.

    >[!NOTE]
    >Bezproblémové jednotné přihlašování podporuje několik doménových struktur AD, ať už mezi nimi existují vztahy důvěryhodnosti AD.

* **Nastavení přihlašovacích údajů správce domény**: Musíte mít přihlašovací údaje správce domény pro každou doménovou strukturu služby Active Directory, která:
    * Synchronizujete se do Azure AD prostřednictvím Azure AD Connect.
    * Obsahuje uživatele, které chcete povolit pro bezproblémové přihlašování.
    
* **Povolit moderní ověřování**: Aby tato funkce fungovala, musíte ve svém tenantovi povolit [moderní ověřování](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) .

* **Použijte nejnovější verze klientů Office 365**: Chcete-li získat tiché přihlašování pomocí klientů Office 365 (Outlook, Word, Excel a další), musí uživatelé používat verze 16.0.8730. xxxx nebo vyšší.

## <a name="step-2-enable-the-feature"></a>Krok 2: Povolit funkci

Povolit bezproblémové přihlašování prostřednictvím [Azure AD Connect](whatis-hybrid-identity.md).

>[!NOTE]
> Pokud Azure AD Connect nesplňuje vaše požadavky, můžete také [Povolit bezproblémové přihlašování pomocí prostředí PowerShell](tshoot-connect-sso.md#manual-reset-of-the-feature) . Tuto možnost použijte, pokud máte více než jednu doménu na doménovou strukturu služby Active Directory a chcete být cíleny na doménu, pro kterou chcete povolit bezproblémové přihlašování.

Pokud provádíte novou instalaci Azure AD Connect, vyberte [vlastní cestu instalace](how-to-connect-install-custom.md). Na **přihlašovací stránce uživatele** vyberte možnost **Povolit jednotné přihlašování** .

>[!NOTE]
> Možnost bude k dispozici pro výběr pouze v případě, že metoda Signing je **synchronizace hodnot hash hesel** nebo **předávací ověřování**.

![Azure AD Connect: Přihlášení uživatele](./media/how-to-connect-sso-quick-start/sso8.png)

Pokud už máte instalaci Azure AD Connect, vyberte **přihlašovací stránku změnit uživatele** v Azure AD Connect a pak vyberte **Další**. Pokud používáte Azure AD Connect verze 1.1.880.0 nebo vyšší, bude ve výchozím nastavení vybrána možnost **Povolit jednotné přihlašování** . Pokud používáte starší verze Azure AD Connect, vyberte možnost **Povolit jednotné přihlašování** .

![Azure AD Connect: Změna přihlášení uživatele](./media/how-to-connect-sso-quick-start/changeusersignin.png)

Pokračujte v průvodci, dokud se nedostanete na stránku **Povolit jednotné přihlašování** . Zadejte přihlašovací údaje správce domény pro každou doménovou strukturu služby Active Directory, která:

* Synchronizujete se do Azure AD prostřednictvím Azure AD Connect.
* Obsahuje uživatele, které chcete povolit pro bezproblémové přihlašování.

Po dokončení průvodce se ve vašem tenantovi povolí bezproblémové jednotné přihlašování.

>[!NOTE]
> Přihlašovací údaje správce domény se neukládají v Azure AD Connect ani ve službě Azure AD. Používají se pouze k povolení této funkce.

Postupujte podle těchto pokynů a ověřte, zda jste správně povolili bezproblémové přihlašování:

1. Přihlaste se do [centra pro správu Azure Active Directory](https://aad.portal.azure.com) s přihlašovacími údaji globálního správce vašeho tenanta.
2. V levém podokně vyberte **Azure Active Directory** .
3. Vyberte **Azure AD Connect**.
4. Ověřte, že se funkce **bezproblémového jednotného přihlašování** zobrazuje jako **povolená**.

![Azure Portal: Azure AD Connect podokno](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> Bezproblémové jednotné přihlašování vytvoří účet počítače `AZUREADSSOACC` s názvem v místní službě Active Directory (AD) v každé doménové struktuře služby AD. Účet `AZUREADSSOACC` počítače musí být z bezpečnostních důvodů důrazně chráněn. Pouze správci domény by měli být schopni spravovat účet počítače. Zajistěte, aby delegování protokolu Kerberos na účtu počítače bylo zakázané a aby žádný jiný účet ve službě Active Directory nemá `AZUREADSSOACC` oprávnění k delegování na účtu počítače. Uložte účet počítače do organizační jednotky (OU), kde jsou bezpečné před náhodným odstraněním a k nimž mají přístup pouze správci domény.

>[!NOTE]
> Pokud používáte architekturu pass-the-hash a krádeže přihlašovacích údajů v místním prostředí, proveďte příslušné změny, abyste zajistili, že `AZUREADSSOACC` účet počítače nekončí v kontejneru karantény. 

## <a name="step-3-roll-out-the-feature"></a>Krok 3: Zavedení funkce

Bezproblémovou přihlašování pro uživatele můžete postupně zavádět pomocí níže uvedených pokynů. Začnete přidáním následující adresy URL služby Azure AD do nastavení zóny intranetu pro všechny nebo vybrané uživatele pomocí Zásady skupiny ve službě Active Directory:

- `https://autologon.microsoftazuread-sso.com`

Kromě toho musíte povolit nastavení zásad zóny intranetu **s názvem povolit aktualizace stavového řádku prostřednictvím skriptu** prostřednictvím zásady skupiny. 

>[!NOTE]
> Následující pokyny fungují pouze pro Internet Explorer a Google Chrome ve Windows (Pokud sdílí sadu adres URL důvěryhodných webů s Internet Explorerem). V další části najdete pokyny k nastavení Mozilla Firefox a Google Chrome v macOS.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Proč je potřeba upravit nastavení intranetové zóny uživatelů?

Prohlížeč standardně automaticky vypočítá správnou zónu, buď Internet, nebo intranet, z konkrétní adresy URL. Například `http://contoso/` se mapuje na zónu intranetu, zatímco `http://intranet.contoso.com/` je mapována k zóně Internet (protože adresa URL obsahuje tečku). Prohlížeče neodesílají lístky protokolu Kerberos do koncového bodu cloudu, jako je například adresa URL služby Azure AD, pokud explicitně nepřidáte adresu URL do intranetové zóny prohlížeče.

Existují dva způsoby, jak upravit nastavení zóny intranetu uživatele:

| Možnost | Aspekt správce | Činnost koncového uživatele |
| --- | --- | --- |
| Zásady skupiny | Správce uzamkne úpravy nastavení zóny intranetu. | Uživatelé nemůžou upravovat svoje vlastní nastavení. |
| Preference zásad skupiny |  Správce umožňuje úpravy nastavení zóny intranetu. | Uživatelé můžou změnit svoje vlastní nastavení. |

### <a name="group-policy-option---detailed-steps"></a>Možnost Zásady skupiny – podrobný postup

1. Otevřete nástroj Editor pro správu zásad skupiny.
2. Upravte zásady skupiny, které se aplikují na některé nebo všechny uživatele. V tomto příkladu se používá **výchozí zásada domény**.
3. Přejděte do části > **zásady** > konfigurace uživatele**šablony pro správu** > **součásti systému Windows**Internet**Explorer**. >  >  > **Stránka zabezpečení**ovládacích panelů. Pak vyberte **seznam přiřazení lokality k zóně**.
    ![Jednotné přihlašování](./media/how-to-connect-sso-quick-start/sso6.png)
4. Povolte tuto zásadu a v dialogovém okně zadejte následující hodnoty:
   - **Název hodnoty**: Adresa URL služby Azure AD, na které se předávají lístky protokolu Kerberos.
   - **Hodnota** (Data): **1** označuje zónu intranetu.

     Výsledek bude vypadat takto:

     Název hodnoty:`https://autologon.microsoftazuread-sso.com`
  
     Hodnota (data): 1

   >[!NOTE]
   > Pokud chcete, aby někteří uživatelé nepoužívali bezproblémové přihlašování (například pokud se tito uživatelé přihlásí ke sdíleným terminálům), nastavte předchozí hodnoty na **4**. Tato akce přidá adresu URL služby Azure AD do zóny s omezeným přístupem a pokaždé neumožní bezproblémové jednotné přihlašování.
   >

5. Vyberte **OK**a pak znovu vyberte **OK** .

    ![Jednotné přihlašování](./media/how-to-connect-sso-quick-start/sso7.png)

6. Přejděte do části > **zásady** > konfigurace uživatele**šablony pro správu** > **součásti systému Windows**Internet**Explorer**. >  >  > **Stránka**zabezpečení ovládacího panelu – zóna intranetu. >  Pak vyberte možnost **Povolení aktualizací stavového řádku prostřednictvím skriptu**.

    ![Jednotné přihlašování](./media/how-to-connect-sso-quick-start/sso11.png)

7. Povolte nastavení zásad a pak vyberte **OK**.

    ![Jednotné přihlašování](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>Možnost "Předvolby zásad skupiny" – podrobný postup

1. Otevřete nástroj Editor pro správu zásad skupiny.
2. Upravte zásady skupiny, které se aplikují na některé nebo všechny uživatele. V tomto příkladu se používá **výchozí zásada domény**.
3.  > Přejděte do okna**Předvolby** > Konfiguraceuživatele > **registr**nastavenísystémuWindows > Novápoložkaregistru. > 

    ![Jednotné přihlašování](./media/how-to-connect-sso-quick-start/sso15.png)

4. Do příslušných polí zadejte následující hodnoty a klikněte na **OK**.
   - **Cesta ke klíči**: ***Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-sso.com\autologon***
   - **Název hodnoty**: ***https***.
   - **Typ hodnoty**: ***REG_DWORD***.
   - **Data hodnoty**: ***00000001***.
 
     ![Jednotné přihlašování](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![Jednotné přihlašování](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>Prohlížeč – požadavky

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (všechny platformy)

Mozilla Firefox nepoužívá automatické ověřování pomocí protokolu Kerberos. Každý uživatel musí do svého nastavení Firefox ručně přidat adresu URL služby Azure AD pomocí následujících kroků:
1. Spusťte Firefox a do `about:config` adresního řádku zadejte. Zavřete všechna oznámení, která vidíte.
2. Vyhledejte **síť. vyjednat-auth. Trusted-URI** preference. Tato předvolby obsahuje seznam důvěryhodných lokalit prohlížeče Firefox pro ověřování protokolem Kerberos.
3. Klikněte pravým tlačítkem a vyberte **Upravit**.
4. Do `https://autologon.microsoftazuread-sso.com` pole zadejte.
5. Vyberte **OK** a pak znovu otevřete prohlížeč.

#### <a name="safari-macos"></a>Safari (macOS)

Ujistěte se, že počítač, na kterém běží macOS, je připojený ke službě AD. Pokyny pro AD – spojování zařízení macOS je mimo rámec tohoto článku.

#### <a name="google-chrome-all-platforms"></a>Google Chrome (všechny platformy)

Pokud jste ve svém prostředí přepsali nastavení zásad [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) nebo [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) , ujistěte se, že jste do nich přidali taky adresu`https://autologon.microsoftazuread-sso.com`URL služby Azure AD.

#### <a name="google-chrome-macos-and-other-non-windows-platforms"></a>Google Chrome (macOS a jiné platformy než Windows)

V případě Google Chrome na Mac OS a dalších platformách jiných než Windows najdete v [seznamu zásad projektu Chromu](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) informace o tom, jak povolit adresu URL služby Azure AD pro integrované ověřování.

Použití rozšíření služby Active Directory Zásady skupiny třetích stran k uvedení adresy URL služby Azure AD na Firefox a Google Chrome na uživatele Mac je mimo rámec tohoto článku.

#### <a name="known-browser-limitations"></a>Známá omezení prohlížeče

Bezproblémové jednotné přihlašování nefunguje v privátním režimu procházení v prohlížeči Firefox a v prohlížečích Microsoft Edge. Nefunguje také v aplikaci Internet Explorer, pokud prohlížeč běží v rozšířeném chráněném režimu.

## <a name="step-4-test-the-feature"></a>Krok 4: Testování funkce

Chcete-li otestovat funkci pro konkrétního uživatele, ujistěte se, že jsou splněny všechny následující podmínky:
  - Uživatel se přihlásí v podnikovém zařízení.
  - Zařízení je připojené k doméně služby Active Directory. Zařízení _není_ nutné [připojit k Azure AD](../active-directory-azureadjoin-overview.md).
  - Zařízení má přímé připojení k řadiči domény (DC) v podnikové kabelové nebo bezdrátové síti nebo prostřednictvím připojení vzdáleného přístupu, jako je třeba připojení k síti VPN.
  - Tuto [funkci](##step-3-roll-out-the-feature) jste do tohoto uživatele zavedli prostřednictvím zásady skupiny.

Chcete-li otestovat scénář, ve kterém uživatel zadá pouze uživatelské jméno, ale ne heslo:
   - Přihlaste `https://myapps.microsoft.com/` se k nové privátní relaci prohlížeče.

Chcete-li otestovat situaci, kdy uživatel nemusí zadávat uživatelské jméno nebo heslo, použijte jeden z následujících kroků: 
   - Přihlaste `https://myapps.microsoft.com/contoso.onmicrosoft.com` se k nové privátní relaci prohlížeče. Nahraďte *Contoso* názvem vašeho tenanta.
   - Přihlaste `https://myapps.microsoft.com/contoso.com` se k nové privátní relaci prohlížeče. Nahraďte *contoso.com* ověřenou doménou (ne federované domény) ve vašem tenantovi.

## <a name="step-5-roll-over-keys"></a>Krok 5: Převrácení klíčů

V kroku 2 Azure AD Connect vytvoří účty počítačů (reprezentující službu Azure AD) ve všech doménových strukturách služby Active Directory, na kterých jste povolili bezproblémové přihlašování. Další informace najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování: Technická hluboká](how-to-connect-sso-how-it-works.md)podrobně

>[!IMPORTANT]
>Dešifrovací klíč protokolu Kerberos v účtu počítače, pokud se nevrací, lze použít ke generování lístků protokolu Kerberos pro libovolného uživatele v doménové struktuře služby AD. Škodlivé objekty actor potom můžou zosobnit přihlášení Azure AD pro ohrožené uživatele. Důrazně doporučujeme, abyste tyto dešifrovací klíče protokolu Kerberos pravidelně přepsali nejméně jednou za 30 dní.

Pokyny k postupu při převzetí klíčů najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování: Nejčastější dotazy](how-to-connect-sso-faq.md) Pracujeme na schopnostech zavést automatizované převrácení klíčů.

>[!IMPORTANT]
>Tento krok nemusíte provádět _hned_ po povolení této funkce. Převeďte šifrovací klíče protokolu Kerberos aspoň jednou za 30 dní.

## <a name="next-steps"></a>Další kroky

- [Technická hluboká podrobně](how-to-connect-sso-how-it-works.md): Pochopte, jak funguje funkce bezproblémového jednotného přihlašování.
- [Nejčastější dotazy](how-to-connect-sso-faq.md): Získejte odpovědi na nejčastější dotazy týkající se bezproblémového jednotného přihlašování.
- [Řešení potíží](tshoot-connect-sso.md): Naučte se řešit běžné problémy s funkcí bezproblémového jednotného přihlašování.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Použijte Fórum Azure Active Directory k započetí nových požadavků na funkce.
