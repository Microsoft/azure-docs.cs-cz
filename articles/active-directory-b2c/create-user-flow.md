---
title: Vytvoření toku uživatele – Azure Active Directory B2C
description: Naučte se vytvářet toky uživatelů v Azure Portal, abyste mohli povolit registraci, přihlašování a úpravy uživatelských profilů pro vaše aplikace v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9bd436b972dfb1549232831b1f07c3726ff459dd
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556496"
---
# <a name="create-a-user-flow-in-azure-active-directory-b2c"></a>Vytvoření toku uživatele v Azure Active Directory B2C

V tenantovi Azure Active Directory B2C (Azure AD B2C) můžete vytvářet [uživatelské toky](user-flow-overview.md) různých typů a podle potřeby je používat ve svých aplikacích. Toky uživatelů se dají znovu použít napříč aplikacemi.

> [!IMPORTANT]
> Změnili jsme způsob, jakým označujeme verze toků uživatelů. Dříve jsme nabízeli verze v1 (připravené pro produkční prostředí) a verze v1.1 a v2 (Preview). Nyní jsme konsolidované toky uživatelů **doporučili doporučeným** (ve verzi Preview nové generace) a **standardní** (všeobecně dostupné) verze. Od **1. srpna 2021** se toky všech uživatelských toků verze Preview a v2 starší verze nacházejí v cestě k vyřazení. Podrobnosti najdete v tématu [verze toku uživatele v Azure AD B2C](user-flow-versions.md).

## <a name="before-you-begin"></a>Než začnete

- **Zaregistrujte aplikaci** , kterou chcete použít k testování toku nového uživatele. Příklad najdete v [kurzu: registrace webové aplikace v Azure AD B2C](tutorial-register-applications.md).
- Pokud chcete povolit přihlašování uživatelů pomocí poskytovatelů, jako je Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft nebo Twitter, **přidejte externí zprostředkovatele identity** . Další informace najdete [v tématu Přidání zprostředkovatelů identity k vašim aplikacím v Azure AD B2C](add-identity-provider.md).
- **Nakonfigurujte poskytovatele identity místního účtu** a určete typy identit (e-mail, uživatelské jméno, telefonní číslo), které chcete podporovat pro místní účty ve vašem tenantovi. Pak můžete vybírat z těchto podporovaných typů identity při vytváření toků jednotlivých uživatelů. Když uživatel dokončí tok uživatele, vytvoří se ve vašem adresáři Azure AD B2C místní účet a poskytovatel identity **místního účtu** ověří informace o uživateli. Nakonfigurujte zprostředkovatele identity místního účtu vašeho tenanta pomocí těchto kroků:

   1. Přihlaste se na [Azure Portal](https://portal.azure.com/). 
   2. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta Azure AD B2C.
   3. Na panelu hledání v horní části Azure Portal vyhledejte a vyberte **Azure AD B2C**.
   4. V části **Spravovat** vyberte **Zprostředkovatelé identity**.
   5. V seznamu Zprostředkovatel identity vyberte **místní účet**.
   6. Na stránce **Konfigurovat místní IDP** vyberte všechny typy identit, které chcete podporovat. Výběrem možností, které jsou k dispozici, budou tyto možnosti k dispozici pro uživatelské toky, které vytvoříte později:
      - **Telefon** (Preview): umožňuje uživateli zadat telefonní číslo, které se ověří při registraci, a bude se mu zobrazovat jeho ID.
      - **E-mail** (výchozí): umožňuje uživateli zadat e-mailovou adresu, která se ověří při registraci a znovu se zobrazí jejich ID uživatele.
      - **Uživatelské jméno**: umožňuje uživateli vytvořit vlastní jedinečné ID uživatele. Od uživatele se shromáždí e-mailová adresa a ověří se.
    7. Vyberte **Uložit**.

## <a name="create-a-user-flow"></a>Vytvoření toku uživatele

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.

    ![B2C, okno klienta, adresáře a předplatného, Azure Portal](./media/create-user-flow/directory-subscription-pane.png)

3. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
4. V části **zásady** vyberte **toky uživatelů** a pak vyberte **Nový tok uživatele**.

    ![Stránka toky uživatelů na portálu s zvýrazněným tlačítkem nový uživatelský tok](./media/create-user-flow/signup-signin-user-flow.png)

5. Na stránce **vytvořit tok uživatele** vyberte typ toku uživatele, který chcete vytvořit (Další informace najdete [v tématu toky uživatelů v Azure AD B2C](user-flow-overview.md) ).

    ![Vyberte stránku toku uživatele se zvýrazněným registrací a tokem přihlášení.](./media/create-user-flow/select-user-flow-type.png)

6. V části **vybrat verzi** vyberte **doporučeno** a pak vyberte **vytvořit**. (Další[informace](user-flow-versions.md) o verzích toku uživatele)

    ![Stránka vytvoření toku uživatele v Azure Portal se zvýrazněnými vlastnostmi](./media/create-user-flow/select-version.png)

7. Zadejte **název** toku uživatele (například *signupsignin1*, *profileediting1*, *passwordreset1*).
8. V části **Zprostředkovatelé identity** vyberte možnosti v závislosti na typu toku uživatele, který vytváříte:

   - **Místní účet**. Pokud chcete uživatelům dovolit, aby ve svém tenantovi Azure AD B2C vytvářet místní účty, vyberte typ identifikátoru, který chcete použít (například e-mail, ID uživatele nebo telefon). V seznamu jsou uvedené jenom typy identit, které jsou nakonfigurované v nastavení [zprostředkovatele identity místního účtu](#before-you-begin) .

   - **Zprostředkovatelé sociálních identit**. Pokud chcete uživatelům přihlašovat pomocí [poskytovatelů sociálních identit, které jste přidali](add-identity-provider.md), jako je Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft nebo Twitter, vyberte poskytovatele ze seznamu.

9. V případě **atributů a deklarací uživatelů** vyberte deklarace identity a atributy, které chcete shromáždit a odeslat uživateli během registrace. Vyberte **Zobrazit další**. Vyberte atributy a deklarace identity a pak vyberte **OK**.

    ![Stránka pro výběr atributů a deklarací se třemi vybranými deklaracemi](./media/create-user-flow/signup-signin-attributes.png)

10. Vyberte **vytvořit** a přidejte tok uživatele. Předpona *B2C_1* je automaticky Předpona názvu.

### <a name="test-the-user-flow"></a>Testování toku uživatele

1. Vyberte **zásady**  >  **uživatelské toky** a pak vyberte tok uživatele, který jste vytvořili. Na stránce Přehled toku uživatele vyberte **Spustit tok uživatele**.
1. V části **aplikace** vyberte webovou aplikaci, kterou jste zaregistrovali v kroku 1. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Vyberte **Spustit tok uživatele**.
2. V závislosti na typu toku uživatele, který testujete, se buď Zaregistrujte pomocí platné e-mailové adresy, nebo použijte postup registrace, nebo se přihlaste pomocí účtu, který jste vytvořili dříve.

    ![Stránka spustit tok uživatele na portálu s zvýrazněným tlačítkem Spustit uživatele toku](./media/create-user-flow/sign-up-sign-in-run-now.png)

1. Postupujte podle pokynů pro tok uživatele. Po dokončení toku uživatele se token vrátí do `https://jwt.ms` a měl by se vám zobrazovat.

> [!NOTE]
> Prostředí "spuštění toku uživatele" není aktuálně kompatibilní s typem adresy URL odpovědi zabezpečeného hesla pomocí toku autorizačního kódu. Pokud chcete pro tyto typy aplikací používat zkušenosti s uživatelským tokem, zaregistrujte adresu URL odpovědi typu web a povolte implicitní tok, jak je popsáno [zde](tutorial-register-spa.md).

## <a name="next-steps"></a>Další kroky

- [Přidání podmíněného přístupu k Azure AD B2C toků uživatelů](conditional-access-user-flow.md)
- [Přizpůsobení uživatelského rozhraní v Azure AD B2C toku uživatele](customize-ui-with-html.md)
