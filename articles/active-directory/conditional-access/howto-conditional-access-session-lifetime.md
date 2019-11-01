---
title: Konfigurace správy relace ověřování pomocí podmíněného přístupu Azure Active Directory
description: Upravte konfiguraci relace ověřování Azure AD, včetně četnosti přihlašovacích uživatelů a trvalosti relace prohlížeče.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70de9da44d52bd6e93377776a2dbfc1b802dd412
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241706"
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

Výchozí konfigurací Azure Active Directory (Azure AD) pro frekvenci přihlašování uživatelů je posuvné okno 90 dnů. Dotazování uživatelů na přihlašovací údaje se často zdá jako rozumné, ale může Backfire: uživatelé, kteří jsou vyškoleni k zadání přihlašovacích údajů, aniž by si je mohli omylem dodávat do výzvy ke škodlivým přihlašovacím údajům.

Může to vést ke zvukovému signalizaci, že nežádá uživatele, aby se k němu přihlásil. v důsledku toho jakékoli porušení zásad IT odvolá relaci. Mezi příklady patří (ale nejsou omezené na) změnu hesla, nekompatibilní zařízení nebo účet zakážete. Relace uživatelů taky můžete explicitně [odvolat pomocí prostředí PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). Výchozí konfigurace služby Azure AD se zobrazí v případě, že uživatel nepožaduje zadání přihlašovacích údajů, pokud se zabezpečení stav jejich relací nezměnilo.

Nastavení četnosti přihlašování funguje s aplikacemi, které implementovaly protokoly OAUTH2 nebo OIDC podle standardů. Většina nativních aplikací Microsoftu pro Windows, Mac a Mobile vyhovuje nastavením.

## <a name="persistence-of-browsing-sessions"></a>Trvalost relací procházení

Trvalá relace prohlížeče umožňuje uživatelům zůstat přihlášeni po zavření a opětovném otevření okna prohlížeče.

Výchozí nastavení Azure AD pro trvalost relace prohlížeče umožňuje uživatelům na osobních zařízeních vybrat, jestli se má relace zachovat, a to tak, že zobrazí "zůstat přihlášeni?". Po úspěšném ověření se zobrazí výzva. Pokud je trvalost prohlížeče nakonfigurované v AD FS pomocí pokynů v článku [AD FS nastavení jednotného přihlašování](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
), budeme tyto zásady dodržovat a zároveň trvale zachová relaci Azure AD. Můžete také nakonfigurovat, jestli uživatelé ve vašem tenantovi uvidí "zůstat přihlášeni?" pomocí pokynů v článku [přizpůsobení přihlašovací stránky služby Azure AD](../fundamentals/customize-branding.md)se zobrazí výzva, že změníte příslušné nastavení v podokně Branding společnosti v Azure Portal.

## <a name="configuring-authentication-session-controls"></a>Konfigurace ovládacích prvků relace ověřování

Podmíněný přístup je Azure AD Premium schopnost a vyžaduje licenci na prémii. Pokud se chcete dozvědět víc o podmíněném přístupu, přečtěte si téma [co je podmíněný přístup v Azure Active Directory?](overview.md#license-requirements)

> [!WARNING]
> Pokud používáte funkci [existence konfigurovatelného tokenu](../develop/active-directory-configurable-token-lifetimes.md) , která je aktuálně ve verzi Public Preview, pamatujte, že nepodporujeme vytváření dvou různých zásad pro stejnou kombinaci uživatelů nebo aplikací: jednu s touto funkcí a druhou s konfigurovatelným tokenem. funkce života. Microsoft plánuje vyřadit z provozu konfigurovatelnou funkci životnosti tokenu 1. května 2020 a nahradit ji funkcí správy relace ověřování pomocí podmíněného přístupu.  

### <a name="policy-1-sign-in-frequency-control"></a>Zásady 1: řízení četnosti přihlašování

1. Vytvořit novou zásadu
1. Vyberte všechny požadované podmínky pro prostředí zákazníka, včetně cílových cloudových aplikací.

   > [!NOTE]
   > Pro klíčové systém Microsoft Office aplikace, jako je Exchange Online a SharePoint Online, se doporučuje nastavit četnost výzvy pro ověření, která se bude používat pro nejlepší uživatelské prostředí.

1. Přejděte na **ovládací prvky přístupu** > **relaci** a klikněte na **četnost přihlášení** .
1. Zadejte požadovanou hodnotu dnů a hodin do prvního textového pole.
1. Vyberte v rozevíracím seznamu hodnotu **hodiny** nebo **dny** .
1. Uložit zásadu

![Zásada podmíněného přístupu konfigurovaná pro četnost přihlášení](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

V zaregistrovaných zařízeních s Windows Azure AD se k zařízení přihlásí za výzvu. Pokud jste například nakonfigurovali četnost přihlášení na 24 hodin pro aplikace Office, uživatelé na registrovaných zařízeních s Windows v Azure AD splní zásady četnosti přihlašování přihlašováním k zařízení a při otevírání aplikací Office se neobjeví znovu.

Pokud jste nakonfigurovali různou frekvenci přihlašování pro různé webové aplikace, které běží ve stejné relaci prohlížeče, použije se u obou aplikací nejpřísnější zásada, protože všechny aplikace spuštěné ve stejné relaci prohlížeče sdílejí jeden token relace.

### <a name="policy-2-persistent-browser-session"></a>Zásada 2: trvalá relace prohlížeče

1. Vytvořit novou zásadu
1. Vyberte všechny požadované podmínky.

   > [!NOTE]
   > Upozorňujeme, že tento ovládací prvek vyžaduje jako podmínku možnost "všechny cloudové aplikace". Trvalost relace prohlížeče se řídí tokenem relace ověřování. Všechny karty v relaci prohlížeče sdílejí jeden token relace, takže všichni musí sdílet stav trvalosti.

1. Přejděte na **ovládací prvky přístupu** > **relaci** a klikněte na **trvalá relace prohlížeče** .
1. Vybrat hodnotu z rozevíracího seznamu
1. Uložit zásady

![Zásada podmíněného přístupu konfigurovaná pro trvalý prohlížeč](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Trvalá konfigurace relace prohlížeče v rámci podmíněného přístupu Azure AD přepíše "zůstat přihlášeni?" nastavení v podokně Branding společnosti v Azure Portal pro stejného uživatele, pokud jste nakonfigurovali obě zásady.

## <a name="validation"></a>Ověření

Pomocí nástroje citlivosti pro simulaci přihlášení uživatele k cílové aplikaci a dalším podmínkám na základě toho, jak jste nakonfigurovali zásady. Ve výsledku nástroje se zobrazí ovládací prvky správy relace ověřování.

![Výsledky nástroje What If podmíněného přístupu](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Nasazení zásad

Abyste se ujistili, že vaše zásada funguje podle očekávání, doporučuje se před jejich vyzkoušením do produkčního prostředí otestovat. V ideálním případě použijte testovacího tenanta a ověřte, zda vaše nové zásady fungují tak, jak mají. Další informace najdete v článku [osvědčené postupy pro podmíněný přístup v Azure Active Directory](best-practices.md).

## <a name="next-steps"></a>Další kroky

* Pokud chcete zjistit, jak nakonfigurovat zásadu podmíněného přístupu, přečtěte si článek věnované použití [vícefaktorového ověřování pro konkrétní aplikace s Azure Active Directory podmíněný přístup](app-based-mfa.md).
* Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro vaše prostředí, přečtěte si článek [osvědčené postupy pro podmíněný přístup v Azure Active Directory](best-practices.md).
