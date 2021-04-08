---
title: Nastavení Azure AD B2C zprostředkovatele identity místního účtu
titleSuffix: Azure AD B2C
description: Definujte typy identit, které můžou používat k registraci nebo přihlášení (e-mail, uživatelské jméno, telefonní číslo) ve vašem tenantovi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b2baff33d9e91e1b5259d79eca0a22535c00f419
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555246"
---
# <a name="set-up-the-local-account-identity-provider"></a>Nastavení zprostředkovatele identity místního účtu

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C poskytuje různé způsoby, jak uživatelé můžou ověřit uživatele. Uživatelé se mohou přihlásit k místnímu účtu pomocí uživatelského jména a hesla, ověřování telefonem (také označovaného jako méně ověřování hesla) nebo poskytovatelů sociálních identit. Registrace e-mailu je ve výchozím nastavení povolená v nastavení zprostředkovatele identity místního účtu. 

Tento článek popisuje, jak uživatelé vytváří své účty místně k tomuto Azure AD B2Cmu tenantovi. V případě identit v sociálních sítích nebo podnicích, kde identity uživatele spravuje federovaný zprostředkovatel identity, jako je Facebook, a Google, přečtěte si téma [Přidání poskytovatele identity](add-identity-provider.md).

## <a name="email-sign-in"></a>Přihlášení k e-mailu

Pomocí možnosti e-mail se uživatelé můžou přihlásit k e-mailové adrese a heslo:

- **Přihlaste** se, zobrazí se uživatelům výzva k zadání e-mailu a hesla.
- Při **registraci** se uživatelům zobrazí výzva k zadání e-mailové adresy, která se ověří při registraci (volitelné) a stane se jejich přihlašovacím ID. Uživatel pak zadá další informace požadované na přihlašovací stránce, například zobrazované jméno, křestní jméno a příjmení. Pak vyberte pokračovat a vytvořte účet.
- **Resetování hesla**, uživatelé musí zadat a ověřit svůj e-mail. uživatel pak může heslo resetovat.

![Přihlášení k e-mailu nebo přihlašovací prostředí](./media/identity-provider-local/local-account-email-experience.png)

## <a name="username-sign-in"></a>Přihlášení k uživatelskému jménu

S možností uživatele se uživatelé můžou přihlásit k uživatelskému jménu a heslu:

- **Přihlášení**: uživatelům se zobrazí výzva k zadání uživatelského jména a hesla.
- **Registrace**: uživatelům se zobrazí výzva k zadání uživatelského jména, které se stane přihlašovacím ID. Uživatelům se zobrazí také výzva k zadání e-mailové adresy, která bude ověřena při registraci. E-mailová adresa se použije během toku resetování hesla. Uživatel zadá další informace požadované na stránce pro registraci, například zobrazované jméno, křestní jméno a příjmení. Uživatel pak vybere pokračovat a vytvoří účet.
- **Resetování hesla**: uživatelé musí zadat své uživatelské jméno a přidruženou e-mailovou adresu. E-mailová adresa musí být ověřena, uživatel může heslo resetovat.

![Přihlašovací uživatelské jméno nebo přihlašovací prostředí](./media/identity-provider-local/local-account-username-experience.png)

## <a name="phone-sign-in-preview"></a>Přihlášení telefonem (Preview)

Ověřování bez hesla je typ ověřování, kdy se uživatel nemusí přihlašovat pomocí hesla. Když se přihlásíte a přihlásíte telefon, uživatel se může přihlásit k aplikaci pomocí telefonního čísla jako jeho primárního přihlašovacího identifikátoru. Uživatel bude mít během registrace a přihlašování následující možnosti:

- **Přihlášení**: Pokud má uživatel existující účet s telefonním číslem jako svůj identifikátor, uživatel zadá své telefonní číslo a vybere *Přihlásit* se. Po výběru možnosti *pokračovat* potvrdí zemi a telefonní číslo a na telefonu se pošle jednorázový ověřovací kód. Uživatel zadá ověřovací kód a vybere *pokračovat* pro přihlášení.
- **Registrace**: Pokud uživatel ještě nemá účet pro vaši aplikaci, může ho vytvořit kliknutím na odkaz Zaregistrovat se k *registraci* . 
    1. Zobrazí se stránka pro registraci, kde uživatel vybere svou *zemi*, zadá jejich telefonní číslo a vybere *Odeslat kód*. 
    1. Do telefonního čísla uživatele se pošle jednorázový ověřovací kód. Uživatel zadá *ověřovací kód* na stránce pro registraci a pak vybere příkaz *ověřit kód*. (Pokud uživatel nemůže získat kód, může vybrat *Odeslat nový kód*). 
    1. Uživatel zadá další informace požadované na stránce pro registraci, například zobrazované jméno, křestní jméno a příjmení. Potom vyberte Pokračovat.
    1. V dalším kroku se uživateli zobrazí výzva k zadání **e-mailu pro obnovení**. Uživatel zadá svou e-mailovou adresu a pak vybere *Odeslat ověřovací kód*. Do e-mailové schránky uživatele se pošle kód, který se může načíst a zadat do pole ověřovací kód. Pak uživatel vybere ověřit kód.
    1. Po ověření kódu uživatel vybere *vytvořit* a vytvoří svůj účet. 

![Přihlášení nebo přihlašovací prostředí pro telefon](./media/identity-provider-local/local-account-phone-experience.png)

### <a name="pricing"></a>Ceny

Jednorázová hesla se uživatelům odesílají pomocí textových zpráv SMS. V závislosti na operátoru mobilní sítě se vám může účtovat Každá odeslaná zpráva. Informace o cenách naleznete v části **samostatné poplatky** [Azure Active Directory B2C ceny](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

> [!NOTE]
> Služba Multi-Factor Authentication (MFA) je ve výchozím nastavení zakázána, když nakonfigurujete tok uživatele s přihlášením k telefonu. VÍCEFAKTOROVÉ ověřování můžete v tokůch uživatelů povolit pomocí registrace telefonem, ale pokud se jako primární identifikátor používá telefonní číslo, bude pro druhý faktor ověřování k dispozici jenom možnost e-mailové heslo pro jeden čas.

### <a name="phone-recovery"></a>Obnovení pro telefony

Když pro toky uživatelů povolíte registraci a přihlášení telefonování, je také vhodné povolit funkci e-mailu pro obnovení. Pomocí této funkce může uživatel zadat e-mailovou adresu, kterou můžete použít k obnovení účtu, když nemají svůj telefon. Tato e-mailová adresa se používá jenom pro obnovení účtu. Nedá se použít pro přihlášení.

- Když se zobrazí výzva k zadání e-mailu **pro obnovení, uživatel** se při prvním přihlášení vyzve k ověření záložního e-mailu. Uživatel, který před dalším přihlášením nezadal e-mail pro obnovení, se vyzve k ověření záložního e-mailu.

- Když je e-mail pro obnovení **vypnutý**, uživatel se k registraci nebo přihlašování nezobrazí výzva k zadání e-mailu pro obnovení.
 
Následující snímky obrazovky ukazují tok obnovy pro telefony:

![Tok uživatele pro obnovení telefonů](./media/identity-provider-local/local-account-change-phone-flow.png)


## <a name="phone-or-email-sign-in-preview"></a>Telefonické nebo e-mailové přihlášení (Preview)

Můžete si vybrat, že se má [přihlašování telefonem](#phone-sign-in-preview)zkombinovat, a [e-mailové přihlášení](#email-sign-in). Na přihlašovací stránce nebo na přihlašovací stránce může uživatel zadat telefonní číslo nebo e-mailovou adresu. V závislosti na vstupu uživatele Azure AD B2C převezme uživatele k odpovídajícímu toku. 

![Telefonické nebo e-mailové přihlášení nebo přihlášení k e-mailu](./media/identity-provider-local/local-account-phone-and-email-experience.png)

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>Konfigurace nastavení zprostředkovatele identity místního účtu

Můžete nakonfigurovat místní zprostředkovatele identity, který je k dispozici pro použití v rámci toku uživatele, povolením nebo zakázáním zprostředkovatelů (e-mail, uživatelské jméno nebo telefonní číslo).  Na úrovni tenanta můžete mít povoleného více než jednoho místního zprostředkovatele identity.

Tok uživatele může být v jednom okamžiku nakonfigurovaný jenom na použití jednoho z poskytovatelů identity místních účtů. Každý tok uživatele může mít nastavený jiný zprostředkovatel identity místního účtu, pokud je na úrovni tenanta povolený víc než jeden.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a zvolíte adresář, který obsahuje vašeho TENANTA Azure AD.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. V části **Spravovat** vyberte **Zprostředkovatelé identity**.
1. V seznamu Zprostředkovatel identity vyberte **místní účet**.
1. Na stránce **Konfigurovat místní IDP** vyberte alespoň jeden z přípustných typů identit, které můžou uživatelé použít k vytvoření svých místních účtů ve vašem tenantovi Azure AD B2C.
1. Vyberte **Uložit**.

## <a name="configure-your-user-flow"></a>Konfigurace toku uživatele

1. V levé nabídce Azure Portal vyberte **Azure AD B2C**.
1. V části **zásady** vyberte **toky uživatelů (zásady)**.
1. Vyberte tok uživatele, pro který chcete nakonfigurovat prostředí pro registraci a přihlašování.
1. Vybrat **zprostředkovatele identity**
1. V části **místní účty** vyberte jednu z následujících možností: **registrace e-mailu**,  **registrace ID uživatele**, registrace **telefonování**, **telefonování a registrace e-mailu** nebo **žádné**.

### <a name="enable-the-recovery-email-prompt"></a>Povolit výzvu k zadání e-mailu pro obnovení

Pokud se rozhodnete pro **registraci telefonu**, **telefon nebo e-mailovou možnost registrace** , povolte výzvu k obnovení e-mailu.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. V Azure AD B2C v části **zásady** vyberte **toky uživatelů**.
1. V seznamu vyberte tok uživatele.
1. V části **Nastavení** vyberte **Vlastnosti**.
1. Vedle **pole povolit výzvu k zadání e-mailu pro obnovení pro přihlášení k telefonnímu číslu a přihlášení (Preview)** vyberte:
   - **Zapnuto** zobrazí se výzva k zadání e-mailu pro obnovení při registraci i přihlášení.
   - **Off** pro skrytí výzvy k zadání e-mailu pro obnovení.
1. Vyberte **Uložit**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="get-the-starter-pack"></a>Získat Startovní sadu

Vlastní zásady jsou sada souborů XML, které nahrajete do svého tenanta Azure AD B2C, abyste mohli definovat cesty uživatelů. Poskytujeme počáteční balíčky s několika předem sestavenými zásadami. Stáhněte si příslušnou úvodní sadu: 

- [Přihlášení k e-mailu](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)
- [Přihlášení k uživatelskému jménu](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin)
- [Přihlášení telefonem](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). Vyberte [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhone.xml) zásady předávající strany. 
- [Telefonické nebo e-mailové přihlášení](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). Vyberte [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhoneOrEmail.xml) zásady předávající strany.

Po stažení počátečního balíčku.

1. V každém souboru nahraďte řetězec `yourtenant` názvem vašeho tenanta Azure AD B2C. Například pokud je název vašeho tenanta B2C *contosob2c*, všechny instance `yourtenant.onmicrosoft.com` se stanou `contosob2c.onmicrosoft.com` .

1. Dokončete kroky v části [Přidání ID aplikací do vlastní zásady](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) [v tématu Začínáme s vlastními zásadami v Azure Active Directory B2C](custom-policy-get-started.md). Například aktualizujte `/phone-number-passwordless/` **`Phone_Email_Base.xml`** **ID aplikace (klienta)** dvou aplikací, které jste zaregistrovali při dokončování požadavků, *IdentityExperienceFramework* a *ProxyIdentityExperienceFramework*.
1. Nahrání souborů zásad

::: zone-end

## <a name="next-steps"></a>Další kroky

- [Přidat externí zprostředkovatele identity](add-identity-provider.md)
- [Vytvoření toku uživatele](tutorial-create-user-flows.md)
