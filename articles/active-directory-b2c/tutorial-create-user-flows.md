---
title: Kurz – vytvoření uživatelských toků – Azure Active Directory B2C
description: V tomto kurzu se naučíte vytvářet toky uživatelů v Azure Portal, abyste mohli povolit registraci, přihlašování a úpravy uživatelských profilů pro vaše aplikace v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c42c6465af8e895d833332be847c134b97ee8ddc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781292"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Kurz: vytvoření toků uživatelů v Azure Active Directory B2C

Ve vašich aplikacích můžete mít [uživatelské toky](user-flow-overview.md) , které uživatelům umožňují registraci, přihlašování a správu svého profilu. V tenantovi Azure Active Directory B2C (Azure AD B2C) můžete vytvořit více uživatelských toků různých typů a podle potřeby je ve svých aplikacích používat. Toky uživatelů se dají znovu použít napříč aplikacemi.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření uživatelského toku pro registraci a přihlašování
> * Povolení samoobslužného resetování hesel
> * Vytvoření toku uživatele upravujícího profil


V tomto kurzu se dozvíte, jak vytvořit několik doporučených uživatelských toků pomocí Azure Portal. Pokud hledáte informace o tom, jak ve své aplikaci nastavit tok přihlašovacích údajů vlastníka prostředku (ROPC), přečtěte si téma [Konfigurace toku přihlašovacích údajů pro heslo vlastníka prostředku v Azure AD B2C](add-ropc-policy.md).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!IMPORTANT]
> Změnili jsme způsob, jakým označujeme verze toků uživatelů. Dříve jsme nabízeli verze v1 (připravené pro produkční prostředí) a verze v1.1 a v2 (Preview). Nyní jsme konsolidované toky uživatelů **doporučili doporučeným** (ve verzi Preview nové generace) a **standardní** (všeobecně dostupné) verze. Od **1. srpna 2021** se toky všech uživatelských toků verze Preview a v2 starší verze nacházejí v cestě k vyřazení. Podrobnosti najdete v tématu [verze toku uživatele v Azure AD B2C](user-flow-versions.md).

## <a name="prerequisites"></a>Požadavky

[Zaregistrujte své aplikace](tutorial-register-applications.md) , které jsou součástí uživatelských toků, které chcete vytvořit.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Vytvoření uživatelského toku pro registraci a přihlašování

Tok uživatelů registrace a přihlašování zpracovává jak registraci, tak i přihlašovací prostředí s jedinou konfigurací. Uživatelé vaší aplikace jsou ve správném umístění v závislosti na kontextu.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.

    ![B2C, okno klienta, adresáře a předplatného, Azure Portal](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. V části **zásady** vyberte **toky uživatelů** a pak vyberte **Nový tok uživatele**.

    ![Stránka toky uživatelů na portálu s zvýrazněným tlačítkem nový uživatelský tok](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Na stránce **vytvořit tok uživatele** vyberte možnost **registrace a přihlášení** uživatele.

    ![Výběr stránky toku uživatelů pomocí registrace a zvýrazněného toku pro přihlášení](./media/tutorial-create-user-flows/select-user-flow-type.png)

1. V části **vybrat verzi** vyberte **doporučeno** a pak vyberte **vytvořit**. (Další[informace](user-flow-versions.md) o verzích toku uživatele)

    ![Stránka vytvoření toku uživatele v Azure Portal se zvýrazněnými vlastnostmi](./media/tutorial-create-user-flows/select-version.png)

1. Zadejte **název** toku uživatele. Například *signupsignin1*.
1. V případě **zprostředkovatelů identity** vyberte **registrace e-mailu**.
1. V případě **atributů a deklarací uživatelů** vyberte deklarace identity a atributy, které chcete shromáždit a odeslat uživateli během registrace. Vyberte například možnost **Zobrazit více** a pak zvolte možnost atributy a deklarace pro **zemi/oblast**, **zobrazované jméno** a **poštovní směrovací číslo**. Klikněte na **OK**.

    ![Stránka pro výběr atributů a deklarací se třemi vybranými deklaracemi](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Kliknutím na **vytvořit** přidejte tok uživatele. Předpona *B2C_1* je automaticky Předpona názvu.

### <a name="test-the-user-flow"></a>Testování toku uživatele

1. Vyberte tok uživatele, který jste vytvořili, a otevřete jeho stránku Přehled a pak vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *WebApp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Klikněte na **Spustit tok uživatele** a pak vyberte **zaregistrovat se hned**.

    ![Stránka spustit tok uživatele na portálu s zvýrazněným tlačítkem Spustit uživatele toku](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Zadejte platnou e-mailovou adresu, klikněte na **Odeslat ověřovací kód**, zadejte ověřovací kód, který jste obdrželi, a pak vyberte **ověřit kód**.
1. Zadejte nové heslo a potvrďte ho.
1. Vyberte zemi a oblast, zadejte název, který chcete zobrazit, zadejte poštovní kód a potom klikněte na **vytvořit**. Token se vrátí do `https://jwt.ms` a měl by se vám zobrazovat.
1. Nyní můžete tok uživatele spustit znovu a měli byste být schopni se přihlásit pomocí účtu, který jste vytvořili. Vrácený token obsahuje deklarace identity, které jste vybrali v poli Země/oblast, jméno a PSČ.

> [!NOTE]
> Prostředí "spuštění toku uživatele" není aktuálně kompatibilní s typem adresy URL odpovědi zabezpečeného hesla pomocí toku autorizačního kódu. Pokud chcete pro tyto typy aplikací používat zkušenosti s uživatelským tokem, zaregistrujte adresu URL odpovědi typu web a povolte implicitní tok, jak je popsáno [zde](tutorial-register-spa.md).

## <a name="enable-self-service-password-reset"></a>Povolení samoobslužného resetování hesel

Povolení [samoobslužného resetování hesla](add-password-reset-policy.md) pro uživatelský tok registrace nebo přihlašování:

1. Vyberte uživatelský tok registrace nebo přihlašování, který jste vytvořili.
1. V části **Nastavení** v levé nabídce vyberte **vlastnosti**.
1. V části **složitost hesla** vyberte **Samoobslužné resetování hesla**.
1. Vyberte **Uložit**.

### <a name="test-the-user-flow"></a>Testování toku uživatele

1. Vyberte tok uživatele, který jste vytvořili, a otevřete jeho stránku Přehled a pak vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *WebApp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Vyberte **Spustit tok uživatele**.
1. Na stránce registrace nebo přihlášení vyberte **zapomenuté heslo?**.
1. Ověřte e-mailovou adresu účtu, který jste vytvořili dříve, a pak vyberte **pokračovat**.
1. Teď máte možnost změnit heslo pro uživatele. Změňte heslo a vyberte **pokračovat**. Token se vrátí do `https://jwt.ms` a měl by se vám zobrazovat.

## <a name="create-a-profile-editing-user-flow"></a>Vytvoření toku uživatele upravujícího profil

Pokud chcete uživatelům umožnit úpravy svého profilu v aplikaci, použijte uživatelský tok upravující profil.

1. V nabídce na stránce Přehled klienta Azure AD B2C vyberte **toky uživatelů** a pak vyberte **Nový tok uživatele**.
1. Na stránce **vytvořit tok uživatele** vyberte uživatelský tok **upravující profil** . 
1. V části **vybrat verzi** vyberte **doporučeno** a pak vyberte **vytvořit**.
1. Zadejte **název** toku uživatele. Například *profileediting1*.
1. V případě **zprostředkovatelů identity** vyberte **přihlášení k místnímu účtu**.
2. V případě **atributů uživatele** vyberte atributy, které má zákazník ve svém profilu upravovat. Vyberte například **Zobrazit více** a potom zvolte atributy i deklarace identity pro **zobrazované jméno** a **název úlohy**. Klikněte na **OK**.
3. Kliknutím na **vytvořit** přidejte tok uživatele. K názvu se automaticky připojí předpona *B2C_1* .

### <a name="test-the-user-flow"></a>Testování toku uživatele

1. Vyberte tok uživatele, který jste vytvořili, a otevřete jeho stránku Přehled a pak vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *WebApp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Klikněte na **Spustit tok uživatele** a pak se přihlaste pomocí účtu, který jste vytvořili dříve.
1. Teď máte příležitost změnit zobrazované jméno a název úlohy pro uživatele. Klikněte na **Pokračovat**. Token se vrátí do `https://jwt.ms` a měl by se vám zobrazovat.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytvoření uživatelského toku pro registraci a přihlašování
> * Vytvoření toku uživatele upravujícího profil
> * Vytvoření toku uživatele pro resetování hesla

V dalším kroku se dozvíte, jak používat Azure AD B2C k přihlášení a registraci uživatelů v aplikaci. Postupujte podle webové aplikace ASP.NET, která je propojena níže, nebo v obsahu v části **ověřování uživatelů** přejděte do jiné aplikace.

> [!div class="nextstepaction"]
> [Kurz: povolení ověřování ve webové aplikaci pomocí Azure AD B2C >](tutorial-web-app-dotnet.md)
