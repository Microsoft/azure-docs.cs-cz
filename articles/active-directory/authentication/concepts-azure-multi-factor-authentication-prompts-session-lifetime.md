---
title: Výzvy k Azure Multi-Factor Authentication a doba platnosti relace
description: Přečtěte si o Doporučené konfiguraci pro výzvy k opakovanému ověření pomocí Azure Multi-Factor Authentication a o tom, jak se používá doba života relace.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab36eb9d5c66f61fbb320c91087659cc811eca85
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91595547"
---
# <a name="optimize-reauthentication-prompts-and-understand-session-lifetime-for-azure-multi-factor-authentication"></a>Vylepší výzvy k opakovanému ověření a pochopení životnosti relace pro Azure Multi-Factor Authentication

Azure Active Directory (Azure AD) má několik nastavení, která určují, jak často se uživatelé potřebují znovu ověřit. Toto opakované ověřování může být u prvního faktoru, jako je heslo, FIDO nebo Microsoft Authenticator bez hesla nebo provádění vícefaktorového ověřování (MFA). Tato nastavení opakovaného ověřování můžete nakonfigurovat podle potřeby vlastního prostředí a uživatelského prostředí, které chcete.

Výchozí konfigurace Azure AD pro četnost přihlašování uživatelů je posuvné okno 90 dnů. Dotazování uživatelů na přihlašovací údaje často vypadá jako rozumné, ale může Backfire. Pokud jsou uživatelé vyškoleni, aby mohli zadat své přihlašovací údaje, aniž by se museli překládat, můžou je omylem zadat na výzvu ke škodlivé přihlašovací údaje.

Může se jednat o zvukové upozornění, které nežádá uživatele, aby se přihlásil zpátky, i když jakékoli porušení zásad IT odvolá relaci. Mezi příklady patří změna hesla, zařízení, které nedodržuje předpisy, nebo operace zakázání účtu. Relace uživatelů taky můžete explicitně [odvolat pomocí prostředí PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken).

Tento článek podrobně popisuje doporučené konfigurace a způsob práce s různými nastaveními a jejich vzájemné interakce.

## <a name="recommended-settings"></a>Doporučené nastavení

Pokud chcete vašim uživatelům poskytnout správné rovnováhu zabezpečení a snadného použití tím, že si je vyžádáte, abyste se přihlásili ke správné četnosti, doporučujeme následující konfigurace:

* Pokud máte Azure AD Premium:
    * Povolte jednotné přihlašování (SSO) napříč aplikacemi pomocí [spravovaných zařízení](../devices/overview.md) nebo [bezproblémového přihlašování](../hybrid/how-to-connect-sso.md).
    * Pokud se vyžaduje opětovné ověření, použijte [zásady četnosti přihlašování](../conditional-access/howto-conditional-access-session-lifetime.md)pro podmíněný přístup.
    * Pro uživatele, kteří se přihlásí z nespravovaných zařízení nebo scénářů pro mobilní zařízení, použijte podmíněný přístup a povolte tak trvalé relace prohlížeče a zásady četnosti přihlašování.
* Pokud máte licence Microsoft 365 apps nebo volná úroveň služby Azure AD:
    * Povolte jednotné přihlašování (SSO) napříč aplikacemi pomocí [spravovaných zařízení](../devices/overview.md) nebo [bezproblémového přihlašování](../hybrid/how-to-connect-sso.md).
    * Nechte zapnutou možnost *zůstat přihlášená* a uživatelé ji přijměte.
* V případě scénářů pro mobilní zařízení se ujistěte, že uživatelé používají aplikaci Microsoft Authenticator. Tato aplikace se používá jako zprostředkovatel pro jiné federované aplikace Azure AD a na zařízení se omezuje výzvy k ověření.

Náš výzkum ukazuje, že toto nastavení je pro většinu tenantů nejvhodnější. Některé kombinace těchto nastavení, jako je *třeba zapamatovat MFA* a *zůstat v jednom*, můžou vést k tomu, že se uživatelům zobrazí výzva, aby ověřili příliš často. Pravidelné výzvy k opakovanému ověření jsou pro produktivitu uživatelů špatné a můžou být zranitelnější vůči útokům.

## <a name="azure-ad-session-lifetime-configuration-settings"></a>Nastavení konfigurace životnosti relace Azure AD

Pro optimalizaci četnosti výzev k ověřování pro vaše uživatele můžete nakonfigurovat možnosti životnosti relace Azure AD. Seznamte se s požadavky na firmu a uživatele a nakonfigurujte nastavení, která poskytují nejlepší rovnováhu pro vaše prostředí.

### <a name="evaluate-session-lifetime-policies"></a>Vyhodnotit zásady životnosti relace

Bez nastavení doby života relace nejsou v relaci prohlížeče žádné trvalé soubory cookie. Pokaždé, když uživatel zavře a otevře prohlížeč, zobrazí výzvu k opakovanému ověření. V klientech Office je výchozím časovým obdobím posuvné okno 90 dnů. V případě této výchozí konfigurace Office, pokud uživatel obnovil heslo, nebo došlo k nečinnosti po dobu více než 90 dní, musí se uživatel znovu ověřit se všemi požadovanými faktory (první a druhý faktor).

Uživatel se může na zařízení, které nemá identitu ve službě Azure AD, zobrazit výzvy s více MFA. Více výzev má za následek, že každá aplikace má vlastní obnovovací token OAuth, který není sdílen s ostatními klientskými aplikacemi. V tomto scénáři MFA dotazuje víckrát, protože každá aplikace vyžaduje, aby byl obnovovací token OAuth ověřený pomocí vícefaktorového ověřování.

Nejvíce omezující zásada pro dobu života relace ve službě Azure AD určuje, kdy se uživatel musí znovu ověřit. Představte si následující scénář:

* Povolení *zůstane přihlášené*, které používá trvalý soubor cookie prohlížeče.
* Zapamatujte si taky možnost *pamatovat MFA na 14 dní* .

V tomto ukázkovém scénáři se uživatel musí znovu ověřit každých 14 dní. Toto chování se řídí nejpřísnějšími zásadami, i když by se služba *Keep jsem přihlásila* sama o sobě nevyžadovala uživatele k opakovanému ověření v prohlížeči.

### <a name="managed-devices"></a>Spravovaná zařízení

Zařízení připojená k Azure AD pomocí služby Azure AD JOIN nebo hybridního připojení služby Azure AD obdrží [primární obnovovací tokeny (PRT)](../devices/concept-primary-refresh-token.md) pro použití jednotného přihlašování (SSO) napříč aplikacemi. Tento PRT umožňuje uživateli přihlásit se na zařízení jednou a umožňuje pracovníkům IT zajistit, aby byly splněny standardy zabezpečení a dodržování předpisů. Pokud je potřeba, aby si uživatel na připojeném zařízení pro některé aplikace nebo scénáře přihlásil častěji, můžete to dosáhnout pomocí [frekvence přihlašování podmíněného přístupu](../conditional-access/howto-conditional-access-session-lifetime.md).

### <a name="show-option-to-remain-signed-in"></a>Zobrazit možnost zůstat přihlášeni

Když uživatel vybere možnost **Ano** na stránce *zůstat přihlášeni?* během přihlašování, je v prohlížeči nastaven trvalý soubor cookie. Tento trvalý soubor cookie se pamatuje jako první i druhý faktor a vztahuje se pouze na požadavky na ověřování v prohlížeči.

![Snímek obrazovky s ukázkovou výzvou pro zůstat přihlášeni](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/stay-signed-in-prompt.png)

Pokud máte licenci Azure AD Premium 1, doporučujeme pro *trvalou relaci prohlížeče*použít zásady podmíněného přístupu. Tato zásada přepíše nastavení *zůstat přihlášená* a poskytuje vylepšené uživatelské prostředí. Pokud nemáte licenci Azure AD Premium 1, doporučujeme, abyste povolili nastavení zůstat přihlášeni pro vaše uživatele.

Další informace o konfiguraci možnosti, která umožní uživatelům zůstat přihlášeni, najdete v tématu [přizpůsobení přihlašovací stránky Azure AD](../fundamentals/customize-branding.md#customize-your-azure-ad-sign-in-page).

### <a name="remember-multi-factor-authentication"></a>Zapamatovat Multi-Factor Authentication  

Toto nastavení umožňuje nakonfigurovat hodnoty mezi 1-365 dny a nastavit trvalý soubor cookie v prohlížeči, když uživatel vybere možnost po X dnech při přihlašování **znovu nedotazovat** .

![Snímek obrazovky s ukázkovou výzvou ke schválení žádosti o přihlášení](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/approve-sign-in-request.png)

I když toto nastavení snižuje počet ověřování ve webových aplikacích, zvyšuje počet ověřování pro klienty moderních ověřování, jako jsou například klienti Office. Tito klienti obvykle vyzvou až po resetování hesla nebo nečinnosti po 90 dnech. Nastavení této hodnoty na méně než 90 dní ale zkrátí výchozí výzvy MFA pro klienty Office a zvýší frekvenci opakovaného ověřování. Pokud se používá v kombinaci s **zůstatem přihlášeným** nebo zásadami podmíněného přístupu, může se zvýšit počet požadavků na ověření.

Pokud používáte *zapamatování MFA* a máte licence Azure AD Premium 1, zvažte možnost migrace těchto nastavení do frekvence přihlašování pomocí podmíněného přístupu. V opačném případě zvažte použití možnost *zůstat přihlášeni?* místo toho.

Další informace najdete v tématu [zapamatování Multi-Factor Authentication](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

### <a name="authentication-session-management-with-conditional-access"></a>Správa relace ověřování pomocí podmíněného přístupu

**Četnost přihlášení** umožňuje správci zvolit četnost přihlášení, která platí pro první i druhý faktor v obou klientech i v prohlížeči. Tato nastavení doporučujeme používat společně s používáním spravovaných zařízení ve scénářích, kdy potřebujete omezit relaci ověřování, například pro důležité podnikové aplikace.

**Trvalá relace prohlížeče** umožňuje uživatelům zůstat přihlášení po zavření a opětovném otevření okna prohlížeče. Podobně jako u nastavení *zůstat přihlášeno* se nastaví trvalý soubor cookie v prohlížeči. Protože je však správce nakonfiguroval, nepožaduje, aby uživatel v případě, že je to v *přihlášeném zůstatku* , vybral možnost **Ano** , takže nabízí lepší uživatelské prostředí. Pokud používáte možnost *zůstat přihlášeni?* , doporučujeme místo toho povolit zásady **relace trvalého prohlížeče** .

Další informace. Další informace najdete v tématu [Konfigurace správy relací ověřování pomocí podmíněného přístupu](../conditional-access/howto-conditional-access-session-lifetime.md).

### <a name="configurable-token-lifetimes"></a>Konfigurovatelné životnosti tokenů

Toto nastavení umožňuje konfiguraci životnosti pro token vydaný Azure Active Directory. Tato zásada je nahrazena *správou relace ověřování s podmíněným přístupem*. Pokud dnes používáte *konfigurovatelné životnosti tokenů* , doporučujeme spustit migraci do zásad podmíněného přístupu.

## <a name="review-your-tenant-configuration"></a>Kontrola konfigurace tenanta  

Když teď víte, jak různá nastavení fungují, a doporučená konfigurace, je čas zkontrolovat konfiguraci klientů a provést změny odpovídajícím způsobem:

Pokud chcete nakonfigurovat nebo zkontrolovat možnost *zůstat přihlášeni* , proveďte následující kroky:

1. Na portálu Azure AD vyhledejte a vyberte *Azure Active Directory*.
1. Vyberte **Branding společnosti**a pak pro každé národní prostředí zvolte **Zobrazit možnost pro zůstat přihlášení**.
1. Zvolte *Ano*a pak vyberte **Uložit**.

Pokud chcete pamatovat nastavení vícefaktorového ověřování, proveďte následující kroky:

1. Na portálu Azure AD vyhledejte a vyberte *Azure Active Directory*.
1. Vyberte **zabezpečení**a pak **MFA**.
1. V části **Konfigurovat**vyberte **Další cloudová nastavení MFA**.
1. Na stránce *nastavení služby Multi-Factor Authentication Service* přejděte na možnost **Zapamatovat si nastavení vícefaktorového ověřování**. Toto nastavení zakažte zrušením zaškrtnutí políčka.

Pokud chcete nakonfigurovat zásady podmíněného přístupu pro četnost přihlášení a trvalou relaci prohlížeče, proveďte následující kroky:

1. Na portálu Azure AD vyhledejte a vyberte *Azure Active Directory*.
1. Vyberte **zabezpečení**a pak **podmíněný přístup**.
1. Nakonfigurujte zásady pomocí doporučených možností správy relací popsaných v tomto článku.

Pokud chcete zkontrolovat životnost tokenů, [použijte Azure AD PowerShell k dotazování všech zásad Azure AD](../develop/configure-token-lifetimes.md#prerequisites). Zakažte všechny zásady, které jste na svém místě.

Pokud je ve vašem tenantovi povolené víc nastavení, doporučujeme, abyste aktualizovali nastavení na základě licencování, které máte k dispozici. Pokud máte například licence Azure AD Premium, měli byste použít jenom zásady podmíněného přístupu pro *četnost přihlášení* a *trvalou relaci prohlížeče*. Pokud máte Microsoft 365 aplikace nebo licence Azure AD Free, měli byste použít konfiguraci *zůstat přihlášeni?*

Pokud jste povolili konfigurovatelné životnosti tokenů, tato funkce bude brzy odebrána. Naplánujte migraci do zásad podmíněného přístupu.

Následující tabulka shrnuje doporučení založená na licencích:

|              | Aplikace Azure AD Free a Microsoft 365 | Azure AD Premium |
|------------------------------|-----------------------------------|------------------|
| **Jednotné přihlašování**                      | [Připojení k Azure AD](../devices/concept-azure-ad-join.md) nebo [hybridní připojení ke službě Azure AD](../devices/concept-azure-ad-join-hybrid.md)nebo [bezproblémové jednotné přihlašování](../hybrid/how-to-connect-sso.md) pro nespravovaná zařízení. | Připojení k Azure AD<br />Hybridní připojení k Azure AD |
| **Nastavení opakovaného ověřování** | Zůstat přihlášeni                  | Použití zásad podmíněného přístupu pro četnost přihlášení a trvalou relaci prohlížeče |

## <a name="next-steps"></a>Další kroky

Pokud chcete začít, dokončete tento kurz a [Zabezpečte události přihlašování uživatelů pomocí Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md) nebo [použijte detekci rizik pro přihlášení uživatelů, aby mohli aktivovat Azure Multi-Factor Authentication](tutorial-risk-based-sspr-mfa.md).
