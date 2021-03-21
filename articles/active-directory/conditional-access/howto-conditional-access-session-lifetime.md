---
title: Konfigurace správy relace ověřování – Azure Active Directory
description: Upravte konfiguraci relace ověřování Azure AD, včetně četnosti přihlašování uživatelů a trvalosti relace prohlížeče.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/23/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6116ab543d6dfc886e44206c2a60e4456b39fbc9
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558181"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Konfigurace správy relace ověřování pomocí podmíněného přístupu

Ve složitých nasazeních můžou organizace potřebovat omezit relace ověřování. Některé scénáře můžou zahrnovat:

* Přístup k prostředkům z nespravovaného nebo sdíleného zařízení
* Přístup k citlivým informacím z externí sítě
* Uživatelé s vysokým dopadem
* Důležité obchodní aplikace

Řízení podmíněného přístupu umožňuje vytvářet zásady, které cílí na konkrétní případy použití v rámci vaší organizace, aniž by to ovlivnilo všechny uživatele.

Než se začnete na podrobnosti o tom, jak nakonfigurovat zásady, Podívejme se na výchozí konfiguraci.

## <a name="user-sign-in-frequency"></a>Frekvence přihlašování uživatelů

Frekvence přihlášení definuje časový interval před tím, než se uživateli zobrazí výzva k opětovnému přihlášení při pokusu o přístup k prostředku.

Výchozí konfigurací Azure Active Directory (Azure AD) pro četnost přihlašování uživatelů je posuvné okno 90 dnů. Dotazování uživatelů na přihlašovací údaje se často zdá jako rozumné, ale může Backfire: uživatelé, kteří jsou vyškoleni k zadání přihlašovacích údajů, aniž by si je mohli omylem dodávat do výzvy ke škodlivým přihlašovacím údajům.

Může to vést ke zvukovému signalizaci, že nežádá uživatele, aby se k němu přihlásil. v důsledku toho jakékoli porušení zásad IT odvolá relaci. Mezi příklady patří (ale nejsou omezené na) změnu hesla, nekompatibilní zařízení nebo účet zakážete. Relace uživatelů taky můžete explicitně [odvolat pomocí prostředí PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken). Výchozí konfigurace služby Azure AD se zobrazí v případě, že uživatel nepožaduje zadání přihlašovacích údajů, pokud se zabezpečení stav jejich relací nezměnilo.

Nastavení četnosti přihlašování funguje s aplikacemi, které implementovaly protokoly OAUTH2 nebo OIDC podle standardů. Většina nativních aplikací Microsoftu pro Windows, Mac a mobilní zařízení, včetně těchto webových aplikací, dodržuje toto nastavení.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- Portál pro správu Microsoft 365
- Exchange Online
- SharePoint a OneDrive
- Webový klient pro týmy
- Dynamics CRM Online
- portál Azure

Nastavení četnosti přihlašování funguje i u aplikací SAML, pokud neobsahují vlastní soubory cookie a při pravidelném ověřování se přesměrují zpátky do služby Azure AD.

### <a name="user-sign-in-frequency-and-multi-factor-authentication"></a>Četnost přihlašování uživatelů a ověřování Multi-Factor Authentication

Četnost přihlašování se dřív používala jenom pro první ověření na zařízeních, která byla připojená k Azure AD, připojení k hybridní službě Azure AD a zaregistrovaná služba Azure AD. Pro naše zákazníky neexistuje snadný způsob, jak na těchto zařízeních znovu vymáhat vícefaktorové ověřování (MFA). Na základě zpětné vazby od zákazníků se pro vícefaktorové ověřování použijí i četnost přihlášení.

[![Frekvence přihlášení a MFA](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart-small.png)](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart.png#lightbox)

### <a name="user-sign-in-frequency-and-device-identities"></a>Četnost přihlašování uživatelů a identit zařízení

Pokud máte připojenou službu Azure AD, připojené k hybridní službě Azure AD nebo zařízení registrovaná v Azure AD, když uživatel odemkne své zařízení nebo přihlášení interaktivně, bude tato událost vyhovovat i zásadě četnosti přihlašování. V následujících dvou příkladech se četnost přihlášení uživatele nastaví na 1 hodinu:

Příklad 1:

- V 00:00 se uživatel přihlásí k zařízení připojenému ke službě Azure AD ve Windows 10 a spustí práci na dokumentu uloženém na SharePointu Online.
- Uživatel pokračuje v práci na stejném dokumentu na svém zařízení po celou hodinu.
- V 01:00 se uživateli zobrazí výzva k opětovnému přihlášení na základě požadavku na četnost přihlášení v zásadách podmíněného přístupu nakonfigurovaných správcem.

Příklad 2:

- V 00:00 se uživatel přihlásí k zařízení připojenému ke službě Azure AD ve Windows 10 a spustí práci na dokumentu uloženém na SharePointu Online.
- V 00:30 se uživatel zaregistruje a provede přerušení uzamykání zařízení.
- V 00:45 se uživatel vrátí z jeho přerušení a odemkne zařízení.
- V 01:45 se uživateli zobrazí výzva k opětovnému přihlášení na základě požadavku na četnost přihlášení v zásadách podmíněného přístupu nakonfigurovaných správcem od posledního přihlášení v 00:45.

## <a name="persistence-of-browsing-sessions"></a>Trvalost relací procházení

Trvalá relace prohlížeče umožňuje uživatelům zůstat přihlášeni po zavření a opětovném otevření okna prohlížeče.

Výchozí nastavení Azure AD pro trvalost relace prohlížeče umožňuje uživatelům na osobních zařízeních vybrat, jestli se má relace zachovat, a to tak, že zobrazí "zůstat přihlášeni?". Po úspěšném ověření se zobrazí výzva. Pokud je trvalost prohlížečů nakonfigurované v AD FS používáním pokynů v článku [AD FS nastavení jediného Sign-On](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
), budeme tyto zásady dodržovat a také zachovat relaci Azure AD. Můžete také nakonfigurovat, jestli uživatelé ve vašem tenantovi uvidí "zůstat přihlášeni?" pomocí pokynů v článku [přizpůsobení přihlašovací stránky služby Azure AD](../fundamentals/customize-branding.md)se zobrazí výzva, že změníte příslušné nastavení v podokně Branding společnosti v Azure Portal.

## <a name="configuring-authentication-session-controls"></a>Konfigurace ovládacích prvků relace ověřování

Podmíněný přístup je Azure AD Premium schopnost a vyžaduje licenci na prémii. Pokud se chcete dozvědět víc o podmíněném přístupu, přečtěte si téma [co je podmíněný přístup v Azure Active Directory?](overview.md#license-requirements)

> [!WARNING]
> Pokud používáte funkci [existence konfigurovatelného tokenu](../develop/active-directory-configurable-token-lifetimes.md) , která je aktuálně ve verzi Public Preview, pamatujte, že nepodporujeme vytváření dvou různých zásad pro stejnou kombinaci uživatelů nebo aplikací: jednu s touto funkcí a další s funkcí konfigurovatelný životností tokenu. Společnost Microsoft vyřadí konfigurovatelnou funkci životnosti tokenů pro dobu obnovení a životnosti tokenů relace od 30. ledna 2021 a nahradila ji funkcí správy relace ověřování pomocí podmíněného přístupu.  
>
> Než povolíte frekvenci přihlašování, ujistěte se, že je ve vašem tenantovi zakázané jiné nastavení pro opakované ověřování. Pokud je povolená možnost Zapamatovat MFA u důvěryhodných zařízení, nezapomeňte ji před použitím četnosti přihlášení zakázat, protože použití těchto dvou nastavení společně může vést k neočekávanému zobrazení výzvy uživatelů. Další informace o výzvách k opakovanému ověření a době platnosti relace najdete v článku věnovaném [optimalizaci výzev k opakovanému ověření a porozumění životnosti relace pro Azure AD Multi-Factor Authentication](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

### <a name="policy-1-sign-in-frequency-control"></a>Zásady 1: řízení četnosti přihlašování

1. Vytvořit novou zásadu
1. Vyberte všechny požadované podmínky pro prostředí zákazníka, včetně cílových cloudových aplikací.

   > [!NOTE]
   > Pro klíčové systém Microsoft Office aplikace, jako je Exchange Online a SharePoint Online, se doporučuje nastavit četnost výzvy pro ověření, která se bude používat pro nejlepší uživatelské prostředí.

1. Přejít do **relace řízení přístupu**  >   a kliknout na **četnost přihlášení**
1. Zadejte požadovanou hodnotu dnů a hodin do prvního textového pole.
1. Vyberte v rozevíracím seznamu hodnotu **hodiny** nebo **dny** .
1. Uložit zásadu

![Zásada podmíněného přístupu konfigurovaná pro frekvenci přihlašování](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

V zaregistrovaných zařízeních s Windows Azure AD se k zařízení přihlásí za výzvu. Pokud jste například nakonfigurovali četnost přihlášení na 24 hodin pro aplikace Office, uživatelé na registrovaných zařízeních s Windows v Azure AD splní zásady četnosti přihlašování přihlášením k zařízení a při otevírání aplikací Office se neobjeví znovu.

### <a name="policy-2-persistent-browser-session"></a>Zásada 2: trvalá relace prohlížeče

1. Vytvořit novou zásadu
1. Vyberte všechny požadované podmínky.

   > [!NOTE]
   > Upozorňujeme, že tento ovládací prvek vyžaduje jako podmínku možnost "všechny cloudové aplikace". Trvalost relace prohlížeče se řídí tokenem relace ověřování. Všechny karty v relaci prohlížeče sdílejí jeden token relace, takže všichni musí sdílet stav trvalosti.

1. Přejděte na **relaci řízení přístupu**  >   a klikněte na **trvalá relace prohlížeče** .
1. Vybrat hodnotu z rozevíracího seznamu
1. Uložit zásady

![Zásada podmíněného přístupu konfigurovaná pro trvalý prohlížeč](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Trvalá konfigurace relace prohlížeče v rámci podmíněného přístupu Azure AD přepíše "zůstat přihlášeni?" nastavení v podokně Branding společnosti v Azure Portal pro stejného uživatele, pokud jste nakonfigurovali obě zásady.

## <a name="validation"></a>Ověřování

Pomocí nástroje What-If Simulujte přihlášení uživatele k cílové aplikaci a dalším podmínkám na základě toho, jak jste zásady nakonfigurovali. Ve výsledku nástroje se zobrazí ovládací prvky správy relace ověřování.

![Výsledky nástroje What If podmíněného přístupu](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Nasazení zásad

Abyste se ujistili, že vaše zásada funguje podle očekávání, doporučuje se před jejich vyzkoušením do produkčního prostředí otestovat. V ideálním případě použijte testovacího tenanta a ověřte, zda vaše nové zásady fungují tak, jak mají. Další informace najdete v článku [Plánování nasazení podmíněného přístupu](plan-conditional-access.md).

## <a name="next-steps"></a>Další kroky

* Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro vaše prostředí, přečtěte si článek [Plánování nasazení podmíněného přístupu](plan-conditional-access.md).