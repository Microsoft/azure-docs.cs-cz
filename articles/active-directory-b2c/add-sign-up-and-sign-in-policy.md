---
title: Nastavení toku registrace a přihlašování
titleSuffix: Azure AD B2C
description: Naučte se, jak v Azure Active Directory B2C nastavit postup registrace a přihlašování.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 047b295d68e872b4dc334e53b5b9da0ec07d5826
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257071"
---
# <a name="set-up-a-sign-up-and-sign-in-flow-in-azure-active-directory-b2c"></a>Nastavení toku registrace a přihlašování v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-up-and-sign-in-flow"></a>Registrace a postup přihlášení

Zásady registrace a přihlašování umožňují uživatelům: 

* Zaregistrujte se pomocí místního účtu.
* Přihlaste se pomocí místního účtu.
* Registrace nebo přihlášení pomocí účtu sociální sítě
* Resetování hesla

![Tok úprav profilu](./media/add-sign-up-and-sign-in-policy/add-sign-up-and-sign-in-flow.png)

## <a name="prerequisites"></a>Požadavky

Pokud jste to ještě neudělali, [Zaregistrujte webovou aplikaci v Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Vytvoření uživatelského toku pro registraci a přihlašování

Tok uživatelů registrace a přihlašování zpracovává jak registraci, tak i přihlašovací prostředí s jedinou konfigurací. Uživatelé vaší aplikace jsou ve správném umístění v závislosti na kontextu.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. V části **zásady** vyberte **toky uživatelů** a pak vyberte **Nový tok uživatele**.

    ![Stránka toky uživatelů na portálu s zvýrazněným tlačítkem nový uživatelský tok](./media/add-sign-up-and-sign-in-policy/signup-signin-user-flow.png)

1. Na stránce **vytvořit tok uživatele** vyberte možnost **registrace a přihlášení** uživatele.

    ![Výběr stránky toku uživatelů pomocí registrace a zvýrazněného toku pro přihlášení](./media/add-sign-up-and-sign-in-policy/select-user-flow-type.png)

1. V části **vybrat verzi** vyberte **doporučeno** a pak vyberte **vytvořit**. (Další[informace](user-flow-versions.md) o verzích toku uživatele)

    ![Stránka vytvoření toku uživatele v Azure Portal se zvýrazněnými vlastnostmi](./media/add-sign-up-and-sign-in-policy/select-version.png)

1. Zadejte **název** toku uživatele. Například *signupsignin1*.
1. V případě **zprostředkovatelů identity** vyberte možnost **registrace e-mailu**.
1. V případě **atributů a deklarací uživatelů** vyberte deklarace identity a atributy, které chcete shromáždit a odeslat uživateli během registrace. Vyberte například možnost **Zobrazit více** a pak zvolte možnost atributy a deklarace pro **zemi/oblast**, **zobrazované jméno** a **poštovní směrovací číslo**. Klikněte na **OK**.

    ![Stránka pro výběr atributů a deklarací se třemi vybranými deklaracemi](./media/add-sign-up-and-sign-in-policy/signup-signin-attributes.png)

1. Kliknutím na **vytvořit** přidejte tok uživatele. Předpona *B2C_1* je automaticky Předpona názvu.
2. Postupujte podle kroků pro [zpracování toku pro "Zapomenuté heslo?"](add-password-reset-policy.md?pivots=b2c-user-flow.md#self-service-password-reset-recommended) v rámci zásady registrace nebo přihlašování.

### <a name="test-the-user-flow"></a>Testování toku uživatele

1. Vyberte tok uživatele, který jste vytvořili, a otevřete jeho stránku Přehled a pak vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *WebApp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Klikněte na **Spustit tok uživatele** a pak vyberte **zaregistrovat se hned**.

    ![Stránka spustit tok uživatele na portálu s zvýrazněným tlačítkem Spustit uživatele toku](./media/add-sign-up-and-sign-in-policy/signup-signin-run-now.png)

1. Zadejte platnou e-mailovou adresu, klikněte na **Odeslat ověřovací kód**, zadejte ověřovací kód, který jste obdrželi, a pak vyberte **ověřit kód**.
1. Zadejte nové heslo a potvrďte ho.
1. Vyberte zemi a oblast, zadejte název, který chcete zobrazit, zadejte poštovní kód a potom klikněte na **vytvořit**. Token se vrátí do `https://jwt.ms` a měl by se vám zobrazovat.
1. Nyní můžete tok uživatele spustit znovu a měli byste být schopni se přihlásit pomocí účtu, který jste vytvořili. Vrácený token obsahuje deklarace identity, které jste vybrali v poli Země/oblast, jméno a PSČ.

> [!NOTE]
> Prostředí "spuštění toku uživatele" není aktuálně kompatibilní s typem adresy URL odpovědi zabezpečeného hesla pomocí toku autorizačního kódu. Pokud chcete pro tyto typy aplikací používat zkušenosti s uživatelským tokem, zaregistrujte adresu URL odpovědi typu web a povolte implicitní tok, jak je popsáno [zde](tutorial-register-spa.md).

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-sign-up-and-sign-in-policy"></a>Vytvoření zásady registrace a přihlašování

Vlastní zásady jsou sada souborů XML, které nahrajete do svého tenanta Azure AD B2C, abyste mohli definovat cesty uživatelů. Nabízíme počáteční balíčky s několika předem sestavenými zásadami, včetně registrace a přihlášení, resetování hesla a zásad úprav profilů. Další informace najdete v tématu [Začínáme s vlastními zásadami v Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy).

::: zone-end

## <a name="next-steps"></a>Další kroky

* Přidejte [přihlášení pomocí zprostředkovatele sociální identity](add-identity-provider.md).
* Nastavte [tok resetování hesla](add-password-reset-policy.md).
