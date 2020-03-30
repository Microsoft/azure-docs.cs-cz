---
title: Konfigurace správy relací ověřování – služba Azure Active Directory
description: Přizpůsobte konfiguraci relace ověřování Azure AD včetně četnosti přihlášení uživatele a trvalosti relace prohlížeče.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e9c0c88064c00c97de7dc58a500910e81c04eef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263280"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Konfigurace správy ověřovacích relací pomocí podmíněného přístupu

Ve složitých nasazeních mohou mít organizace potřebu omezit relace ověřování. Některé scénáře mohou zahrnovat:

* Přístup k prostředkům z nespravovaného nebo sdíleného zařízení
* Přístup k citlivým informacím z externí sítě
* Uživatelé s vysokým dopadem
* Kritické podnikové aplikace

Ovládací prvky podmíněného přístupu umožňují vytvářet zásady, které cílí na konkrétní případy použití ve vaší organizaci, aniž by to ovlivnilo všechny uživatele.

Než se ponoříte do podrobností o tom, jak zásadu nakonfigurovat, podívejme se na výchozí konfiguraci.

## <a name="user-sign-in-frequency"></a>Frekvence přihlašování uživatele

Frekvence přihlášení definuje časové období, než je uživatel vyzván k novému přihlášení při pokusu o přístup k prostředku.

Výchozí konfigurace služby Azure Active Directory (Azure AD) pro frekvenci přihlášení uživatele je postupné okno 90 dní. Žádat uživatele o pověření se často jeví jako rozumná věc, ale může se vymstít: uživatelé, kteří jsou vyškoleni k zadání svých přihlašovacích údajů bez přemýšlení, je mohou neúmyslně poskytnout na výzvu škodlivého pověření.

Může to znít alarmující, nežádat uživatele, aby se přihlásil zpět, ve skutečnosti jakékoli porušení zásad IT zruší relaci. Některé příklady zahrnují (ale nejsou omezeny na) změnu hesla, nekompatibilní zařízení nebo účet zakázat. Můžete také explicitně [odvolat relace uživatelů pomocí prostředí PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Výchozí konfigurace Azure AD přijde na "neptejte se uživatelů, aby poskytli svá pověření, pokud se nezměnil stav zabezpečení jejich relací".

Nastavení frekvence přihlášení funguje s aplikacemi, které implementovaly protokoly OAUTH2 nebo OIDC podle standardů. S tímto nastavením je v souladu většina nativních aplikací Microsoftu pro Windows, Mac a Mobile, včetně následujících webových aplikací.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- Portál pro správu O365
- Exchange Online
- SharePoint a OneDrive
- Webový klient Teams
- Dynamics CRM Online
- portál Azure

### <a name="user-sign-in-frequency-and-device-identities"></a>Frekvence přihlášení uživatelů a identity zařízení

Pokud máte Azure AD připojen, hybridní Azure AD připojen, nebo Azure AD registrovaná zařízení, když uživatel odemkne své zařízení nebo přihlášení interně, tato událost uspokojí zásady frekvence přihlášení také. V následujících 2 příkladech je frekvence přihlášení uživatele nastavena na 1 hodinu:

Příklad 1:

- V 00:00 se uživatel přihlásí ke svému zařízení s Windows 10 Azure AD a začne pracovat na dokumentu uloženém na SharePointu Online.
- Uživatel pokračuje v práci na stejném dokumentu na svém zařízení po dobu jedné hodiny.
- V 01:00 je uživatel vyzván k novému přihlášení na základě požadavku na frekvenci přihlášení v zásadách podmíněného přístupu nakonfigurovaných jeho správcem.

Příklad 2:

- V 00:00 se uživatel přihlásí ke svému zařízení s Windows 10 Azure AD a začne pracovat na dokumentu uloženém na SharePointu Online.
- V 00:30 uživatel vstane a provede přestávku a zamkne své zařízení.
- V 00:45 se uživatel vrátí z přestávky a odemkne zařízení.
- V 01:45 je uživatel vyzván k přihlášení znovu na základě požadavku frekvence přihlášení v zásadách podmíněného přístupu nakonfigurovaných jejich správcem od posledního přihlášení v 00:45.

## <a name="persistence-of-browsing-sessions"></a>Trvalá procházení relací

Trvalá relace prohlížeče umožňuje uživatelům zůstat přihlášeni po zavření a opětovném otevření okna prohlížeče.

Výchozí nastavení azure ad pro trvalost relace prohlížeče umožňuje uživatelům na osobních zařízeních zvolit, zda chcete zachovat relaci zobrazením "Zůstat přihlášeni?" po úspěšném ověření. Pokud je trvalost prohlížeče nakonfigurována ve službě AD FS pomocí pokynů v článku [Nastavení jednotného přihlášení služby AD FS](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
), budeme dodržovat tyto zásady a také zachováme relaci Azure AD. Můžete také nakonfigurovat, zda se uživatelům ve vašem tenantovi zobrazí možnost Zůstat přihlášeni? výzvou změnou příslušného nastavení v podokně značky společnosti na webu Azure Portal pomocí pokynů v článku [Přizpůsobení přihlašovací stránky Azure AD](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Konfigurace ovládacích prvků relace ověřování

Podmíněný přístup je funkce Azure AD Premium a vyžaduje prémiovou licenci. Pokud se chcete dozvědět víc o podmíněném přístupu, přečtěte si informace [o podmíněném přístupu ve službě Azure Active Directory?](overview.md#license-requirements)

> [!WARNING]
> Pokud používáte [konfigurovatelnou](../develop/active-directory-configurable-token-lifetimes.md) funkci životnosti tokenu, která je aktuálně ve verzi Public Preview, upozorňujeme, že nepodporujeme vytváření dvou různých zásad pro stejnou kombinaci uživatelů nebo aplikací: jednu s touto funkcí a druhou s konfigurovatelnou funkcí životnosti tokenu. Společnost Microsoft plánuje vyřadit konfigurovatelnou funkci životnosti tokenu 1.  

### <a name="policy-1-sign-in-frequency-control"></a>Zásady 1: Řízení frekvence přihlášení

1. Vytvořit novou zásadu
1. Vyberte všechny požadované podmínky pro prostředí zákazníka, včetně cílových cloudových aplikací.

   > [!NOTE]
   > Doporučujeme nastavit stejnou četnost ověřování pro klíčové aplikace Microsoft Office, jako je Exchange Online a SharePoint Online, abyste se co nejlépe domátli uživatelského prostředí.

1. Přejděte na Relaci **ovládacích prvků** > **přístupu** a klikněte na **Četnost přihlášení.**
1. Zadejte požadovanou hodnotu dnů a hodin do prvního textového pole.
1. Výběr hodnoty **hodin** nebo **dnů** z rozevíracího pole
1. Uložení zásad

![Zásady podmíněného přístupu nakonfigurované pro frekvenci přihlášení](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Na azure ad registrovaných zařízení s Windows přihlášení k zařízení se považuje za výzvu. Pokud jste například nakonfigurovali frekvenci přihlášení na 24 hodin pro aplikace Office, uživatelé na zařízeních s Windows registrovanými službou Azure AD uspokojí zásady četnosti přihlášení přihlášením přihlášením se k zařízení a při otevírání aplikací Office se znovu nezobrazí výzva.

Pokud jste nakonfigurovali různé frekvence přihlášení pro různé webové aplikace, které běží ve stejné relaci prohlížeče, budou nejpřísnější zásady použity pro obě aplikace, protože všechny aplikace spuštěné ve stejné relaci prohlížeče sdílejí jeden token relace.

### <a name="policy-2-persistent-browser-session"></a>Zásady 2: Trvalá relace prohlížeče

1. Vytvořit novou zásadu
1. Vyberte všechny požadované podmínky.

   > [!NOTE]
   > Vezměte prosím na vědomí, že tento ovládací prvek vyžaduje zvolit "Všechny cloudové aplikace" jako podmínku. Trvalost relace prohlížeče je řízena tokenem relace ověřování. Všechny karty v relaci prohlížeče sdílejí jeden token relace, a proto musí všechny sdílet stav trvalosti.

1. Přejděte na**relaci** **ovládacích prvků** > aplikace Access a klikněte na **trvalá relace prohlížeče.**
1. Výběr hodnoty z rozevíracího pole
1. Uložit zásady

![Zásady podmíněného přístupu nakonfigurované pro trvalý prohlížeč](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Trvalá konfigurace relace prohlížeče v podmíněném přístupu Azure AD přepíše možnost Zůstat přihlášená? nastavení v podokně značky společnosti na portálu Azure pro stejného uživatele, pokud jste nakonfigurovali obě zásady.

## <a name="validation"></a>Ověřování

Pomocí nástroje What-If simulujte přihlášení uživatele k cílové aplikaci a další podmínky na základě toho, jak jste nakonfigurovali zásady. Ovládací prvky správy relace ověřování se zobrazí ve výsledku nástroje.

![Výsledky nástroje Co-li podmíněného přístupu](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Nasazení zásad

Chcete-li se ujistit, že vaše zásady fungují podle očekávání, doporučujeme osvědčeným postupem otestovat před jejich zavedením do produkčního prostředí. V ideálním případě použijte testovacího klienta k ověření, zda vaše nové zásady fungují tak, jak bylo zamýšleno. Další informace naleznete v článku [Doporučené postupy pro podmíněný přístup ve službě Azure Active Directory](best-practices.md).

## <a name="next-steps"></a>Další kroky

* Pokud chcete vědět, jak nakonfigurovat zásady podmíněného přístupu, přečtěte si článek [Vyžadovat vícefaktorové povolení pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](app-based-mfa.md).
* Pokud jste připraveni nakonfigurovat zásady podmíněného přístupu pro vaše prostředí, přečtěte si článek [Doporučené postupy pro podmíněný přístup ve službě Azure Active Directory](best-practices.md).
