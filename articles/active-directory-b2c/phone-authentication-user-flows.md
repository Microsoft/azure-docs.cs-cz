---
title: Nastavení zprostředkovatele identity místního účtu
titleSuffix: Azure AD B2C
description: Definujte typy identity, které můžete použít (e-mail, uživatelské jméno, telefonní číslo) pro ověřování místního účtu při nastavení toků uživatelů ve vašem tenantovi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dd21c1dca0dd54331780ba98f9c53d5b99d6b4e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100557234"
---
# <a name="set-up-phone-sign-up-and-sign-in-for-user-flows-preview"></a>Nastavení registrace a přihlášení k telefonu pro toky uživatelů (Preview)

> [!NOTE]
> Funkce pro zápis a přihlášení k telefonu a e-mailové funkce pro uživatelské toky jsou ve verzi Public Preview.

Kromě e-mailu a uživatelského jména můžete povolit telefonní číslo jako možnosti registrace na úrovni tenanta tím, že přidáte registraci a přihlásíte se k místnímu poskytovateli identity účtu. Když zapnete registraci a přihlášení telefonování pro místní účty, můžete do svých uživatelských toků přidat registraci telefonu.

Nastavení registrace a přihlášení k telefonu v toku uživatele zahrnuje následující kroky:

- V rámci zprostředkovatele identity místního účtu [nakonfigurujte registraci a přihlašování telefonem na úrovni tenanta a](#configure-phone-sign-up-and-sign-in-tenant-wide) přijměte telefonní číslo jako identitu uživatele. 

- [Přidejte si do svého uživatelského toku registraci telefonem](#add-phone-sign-up-to-a-user-flow) a Umožněte uživatelům, aby si mohli zaregistrovat svoji aplikaci pomocí jejich telefonního čísla.

- [Povolením výzvy k zadání e-mailu pro obnovení (Preview)](#enable-the-recovery-email-prompt-preview) umožníte uživatelům zadat e-mail, který se dá použít k obnovení účtu, když nemají svůj telefon.

Služba Multi-Factor Authentication (MFA) je ve výchozím nastavení zakázána, když nakonfigurujete tok uživatele s přihlášením k telefonu. VÍCEFAKTOROVÉ ověřování můžete v tokůch uživatelů povolit pomocí registrace telefonem, ale pokud se jako primární identifikátor používá telefonní číslo, bude pro druhý faktor ověřování k dispozici jenom možnost e-mailové heslo pro jeden čas.

## <a name="configure-phone-sign-up-and-sign-in-tenant-wide"></a>Konfigurace registrace a přihlašování telefonem na úrovni klienta

Registrace e-mailu je ve výchozím nastavení povolená v nastavení zprostředkovatele identity místního účtu. Typy identit, které ve svém tenantovi podporujete, můžete změnit tak, že vyberete nebo zrušíte výběr možnosti přihlášení k e-mailu, uživatelské jméno nebo telefonní číslo.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a zvolíte adresář, který obsahuje vašeho TENANTA Azure AD.

3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.

4. V části **Spravovat** vyberte **Zprostředkovatelé identity**.

5. V seznamu Zprostředkovatel identity vyberte **místní účet**.

   ![Zprostředkovatelé identity – výběr místního účtu](media/phone-authentication-user-flows/identity-provider-local-account.png)

1. Na stránce **Konfigurovat místní IDP** se ujistěte, že je **telefon** vybraný jako jeden z přípustných typů identity, které můžou uživatelé použít k vytvoření svých místních účtů ve vašem tenantovi Azure AD B2C.

   ![Vyberte povolené typy identity.](media/phone-authentication-user-flows/configure-local-idp.png)

1. Vyberte **Uložit**.

## <a name="add-phone-sign-up-to-a-user-flow"></a>Přidání telefonického přihlášení do uživatelského toku

Po přidání registrace telefonu jako možnosti identity pro místní účty ho můžete přidat do toků uživatelů, pokud se jedná o nejnovější verze **doporučeného** toku uživatele. Následuje příklad, který ukazuje, jak přidat registraci telefonu k novým tokům uživatelů. Můžete ale také přidat registraci do stávajících uživatelských toků s doporučenou verzí (vyberte **toky uživatelů**  >  *uživatelských toků název*  >  **poskytovatelé identity poskytovatelé** přihlášení k  >  **místnímu účtu**). 

Tady je příklad, který ukazuje, jak přidat registraci telefonu do nového toku uživatele.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.

    ![B2C, okno klienta, adresáře a předplatného, Azure Portal](./media/phone-authentication-user-flows/directory-subscription-pane.png)

3. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
4. V části **zásady** vyberte **toky uživatelů** a pak vyberte **Nový tok uživatele**.

    ![Stránka toky uživatelů na portálu s zvýrazněným tlačítkem nový uživatelský tok](./media/phone-authentication-user-flows/signup-signin-user-flow.png)

5. Na stránce **vytvořit tok uživatele** vyberte možnost **registrace a přihlášení** uživatele.

    ![Vyberte stránku toku uživatele se zvýrazněným registrací a tokem přihlášení.](./media/phone-authentication-user-flows/select-user-flow-type.png)

6. V části **vybrat verzi** vyberte **doporučeno** a pak vyberte **vytvořit**. (Další[informace](user-flow-versions.md) o verzích toku uživatele)

    ![Tlačítko vytvořit uživatelský tok](./media/phone-authentication-user-flows/select-version.png)

7. Zadejte **název** toku uživatele. Například *signupsignin1*.
8. V části **Zprostředkovatelé identity** v části **místní účty** vyberte **Registrace pro telefon**.

   ![Vybraná možnost telefonního zápisu uživatelského toku](media/phone-authentication-user-flows/user-flow-phone-signup.png)

9. V části **Zprostředkovatelé sociální identity** vyberte všechny ostatní zprostředkovatele identity, které chcete pro tento tok uživatelů použít.

   > [!NOTE]
   > Služba Multi-Factor Authentication (MFA) je ve výchozím nastavení zakázána pro uživatelské toky registrace. VÍCEFAKTOROVÉ ověřování můžete povolit pro uživatelský tok, který se zaregistruje, ale pokud se jako primární identifikátor používá telefonní číslo, pro druhý faktor ověření je k dispozici jenom možnost e-mailového hesla pro jeden čas.

1. V části **atributy uživatele a deklarace identity tokenů** vyberte deklarace identity a atributy, které chcete shromáždit a odeslat uživateli během registrace. Vyberte například možnost **Zobrazit více** a pak zvolte možnost atributy a deklarace pro **zemi/oblast**, **zobrazované jméno** a **poštovní směrovací číslo**. Vyberte **OK**.

1. Vyberte **vytvořit** a přidejte tok uživatele. Předpona *B2C_1* je automaticky Předpona názvu.

## <a name="enable-the-recovery-email-prompt-preview"></a>Povolit výzvu k zadání e-mailu pro obnovení (Preview)

Když pro toky uživatelů povolíte registraci a přihlášení telefonování, je také vhodné povolit funkci e-mailu pro obnovení. Pomocí této funkce může uživatel zadat e-mailovou adresu, kterou můžete použít k obnovení účtu, když nemají svůj telefon. Tato e-mailová adresa se používá jenom pro obnovení účtu. Nedá se použít pro přihlášení.

- Když se zobrazí výzva k zadání e-mailu **pro obnovení, uživatel** se při prvním přihlášení vyzve k ověření záložního e-mailu. Uživatel, který před dalším přihlášením nezadal e-mail pro obnovení, se vyzve k ověření záložního e-mailu.

- Když je e-mail pro obnovení **vypnutý**, uživatel se k registraci nebo přihlašování nezobrazí výzva k zadání e-mailu pro obnovení.
 
Můžete povolit výzvu k zadání e-mailu pro obnovení ve vlastnostech toku uživatele.

> [!NOTE]
> Než začnete, ujistěte se, že jste [přidali telefon do svého uživatelského toku](#add-phone-sign-up-to-a-user-flow) , jak je popsáno výše.

### <a name="to-enable-the-recovery-email-prompt"></a>Povolení výzvy k zadání e-mailu pro obnovení

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
3. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
4. V Azure AD B2C v části **zásady** vyberte **toky uživatelů**.
5. V seznamu vyberte tok uživatele.
6. V části **Nastavení** vyberte **Vlastnosti**.
7. Vedle **pole povolit výzvu k zadání e-mailu pro obnovení pro přihlášení k telefonnímu číslu a přihlášení (Preview)** vyberte:

   - **Zapnuto** zobrazí se výzva k zadání e-mailu pro obnovení při registraci i přihlášení.
   - **Off** pro skrytí výzvy k zadání e-mailu pro obnovení.

    ![Vlastnosti toků uživatelů s povoleným e-mailem povolení pro obnovení](./media/phone-authentication-user-flows/recovery-email-settings.png)

8. Vyberte **Uložit**.

### <a name="to-test-the-recovery-email-prompt"></a>Otestování výzvy k zadání e-mailu pro obnovení

Po povolení registrace a přihlášení telefonování a zobrazování výzvy k e-mailu pro obnovení v toku uživatele můžete pomocí možnosti **Spustit tok uživatelů** otestovat činnost koncového uživatele.

1. Vyberte **zásady**  >  **uživatelské toky** a pak vyberte tok uživatele, který jste vytvořili. Na stránce Přehled toku uživatele vyberte **Spustit tok uživatele**.

2. V části **aplikace** vyberte webovou aplikaci, kterou jste zaregistrovali v kroku 1. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .

3. Vyberte **Spustit tok uživatele** a ověřte následující chování:

   - Uživateli, který se poprvé zaregistruje, se zobrazí výzva k zadání e-mailu pro obnovení. 
   - Uživatel, který se už zaregistroval, ale neposkytl e-mailu pro obnovení, se vyzve, aby po přihlášení zadal jednu z nich.

4. Zadejte e-mailovou adresu a pak vyberte **Odeslat ověřovací kód**. Ověřte, že se do e-mailové schránky, kterou jste zadali, pošle kód. Načtěte kód a zadejte ho do pole **ověřovací kód** . Pak vyberte **ověřit kód**.

## <a name="next-steps"></a>Další kroky

- [Přidat externí zprostředkovatele identity](add-identity-provider.md)
- [Vytvoření toku uživatele](tutorial-create-user-flows.md)