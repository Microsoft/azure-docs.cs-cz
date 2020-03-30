---
title: Kurz – vytváření uživatelských toků – Azure Active Directory B2C
description: Zjistěte, jak vytvořit toky uživatelů na webu Azure Portal, abyste povolili registraci, přihlášení a úpravy profilu uživatele pro vaše aplikace ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 51adbb74635f66ca86347b536dc2607566dcb725
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264242"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Kurz: Vytvoření toků uživatelů ve službě Azure Active Directory B2C

Ve vašich aplikacích můžete mít [toky uživatelů,](user-flow-overview.md) které uživatelům umožňují zaregistrovat se, přihlásit se nebo spravovat svůj profil. V tenantovi Služby Azure Active Directory B2C (Azure AD B2C) můžete vytvořit více uživatelských toků různých typů a podle potřeby je použít ve svých aplikacích. Toky uživatelů lze znovu použít napříč aplikacemi.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření toku registrace a přihlášení uživatele
> * Vytvoření toku uživatele pro úpravy profilu
> * Vytvoření toku uživatele pro obnovení hesla

Tento kurz ukazuje, jak vytvořit některé doporučené toky uživatelů pomocí portálu Azure. Pokud hledáte informace o tom, jak nastavit tok přihlašovacích údajů vlastníka prostředku (ROPC) ve vaší aplikaci, najdete v [tématu Konfigurace toku přihlašovacích údajů vlastníka prostředku v Azure AD B2C](configure-ropc.md).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

[Zaregistrujte své aplikace,](tutorial-register-applications.md) které jsou součástí toků uživatelů, které chcete vytvořit.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Vytvoření toku registrace a přihlášení uživatele

Tok registrace a přihlášení uživatele zpracovává možnosti registrace i přihlášení s jedinou konfigurací. Uživatelé vaší aplikace jsou vedeny správnou cestou v závislosti na kontextu.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Vyberte ikonu **Directory + Subscription** na panelu nástrojů portálu a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.

    ![Klient B2C, podokno Adresář a předplatné, portál Azure](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. Na webu Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. V části **Zásady**vyberte **Toky uživatelů (zásady)** a pak vyberte **Nový tok uživatelů**.

    ![Stránka toků uživatelů na portálu se zvýrazněným tlačítkem Tok nového uživatele](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Na kartě **Doporučeno** vyberte **tok registrace a přihlášení** uživatele.

    ![Výběr stránky toku uživatele se zvýrazněným tokem Registrace a přihlášení](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Zadejte **název** toku uživatele. Například *signupsignin1*.
1. V **případě zprostředkovatelů identity**vyberte **možnost Registrace e-mailu**.

    ![Vytvoření stránky toku uživatelů na webu Azure Portal se zvýrazněnými vlastnostmi](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. Pro **atributy a deklarace identity uživatele**zvolte deklarace identity a atributy, které chcete shromažďovat a odesílat od uživatele během registrace. Vyberte například **Zobrazit více**a pak zvolte atributy a deklarace pro **zemi nebo oblast**, **zobrazované jméno**a **PSČ**. Klikněte na tlačítko **OK**.

    ![Stránka výběru atributů a deklarací se třemi vybranými deklaracemi](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Kliknutím na **Vytvořit** přidejte tok uživatele. K názvu se automaticky připojí předpona *B2C_1.*

### <a name="test-the-user-flow"></a>Testování toku uživatele

1. Vyberte uživatelský tok, který jste vytvořili, otevřete stránku s přehledem a pak vyberte **Spustit tok uživatele**.
1. V **aplikaci**vyberte webovou aplikaci s názvem *webapp1,* kterou jste dříve zaregistrovali. Adresa **URL** odpovědi `https://jwt.ms`by se měla zobrazit .
1. Klikněte na **Spustit tok uživatele**a potom vyberte **Zaregistrovat se .**

    ![Spuštění stránky toku uživatelů na portálu se zvýrazněným tlačítkem Spustit tok uživatele](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Zadejte platnou e-mailovou adresu, klikněte na **Odeslat ověřovací kód**, zadejte ověřovací kód, který obdržíte, a pak vyberte **Ověřit kód**.
1. Zadejte nové heslo a potvrďte heslo.
1. Vyberte zemi a oblast, zadejte název, který chcete zobrazit, zadejte PSČ a klikněte na **Vytvořit**. Token je vrácena `https://jwt.ms` a měla by být zobrazena na vás.
1. Nyní můžete spustit tok uživatele znovu a měli byste být schopni se přihlásit pomocí účtu, který jste vytvořili. Vrácený token obsahuje deklarace identity, které jste vybrali pro zemi nebo oblast, název a PSČ.

## <a name="create-a-profile-editing-user-flow"></a>Vytvoření toku uživatele pro úpravy profilu

Pokud chcete uživatelům povolit úpravy jejich profilu v aplikaci, použijte tok uživatele pro úpravy profilu.

1. V nabídce stránky přehled klienta Azure AD B2C vyberte **Toky uživatelů (zásady)** a pak vyberte **Tok nového uživatele**.
1. Na kartě **Doporučené** vyberte tok uživatele **pro úpravy profilu.**
1. Zadejte **název** toku uživatele. Například *profileediting1*.
1. V **případě poskytovatelů identit vyberte**možnost Místní přihlášení k **účtu**.
1. V **atributech Uživatele**zvolte atributy, které má zákazník upravovat ve svém profilu. Vyberte například **Zobrazit více**a pak vyberte atributy i nároky pro **zobrazované jméno** a název **úlohy**. Klikněte na tlačítko **OK**.
1. Kliknutím na **Vytvořit** přidejte tok uživatele. K názvu se automaticky připojí předpona *B2C_1.*

### <a name="test-the-user-flow"></a>Testování toku uživatele

1. Vyberte uživatelský tok, který jste vytvořili, otevřete stránku s přehledem a pak vyberte **Spustit tok uživatele**.
1. V **aplikaci**vyberte webovou aplikaci s názvem *webapp1,* kterou jste dříve zaregistrovali. Adresa **URL** odpovědi `https://jwt.ms`by se měla zobrazit .
1. Klikněte na **Spustit tok uživatele**a přihlaste se pomocí účtu, který jste dříve vytvořili.
1. Nyní máte možnost změnit zobrazované jméno a pracovní pozici uživatele. Klikněte na **Pokračovat**. Token je vrácena `https://jwt.ms` a měla by být zobrazena na vás.

## <a name="create-a-password-reset-user-flow"></a>Vytvoření toku uživatele pro obnovení hesla

Chcete-li uživatelům aplikace povolit resetování hesla, použijte tok uživatelů pro obnovení hesla.

1. V nabídce přehled klienta Azure AD B2C vyberte **Toky uživatelů (zásady)** a pak vyberte **Tok nového uživatele**.
1. Na kartě **Doporučeno** vyberte tok uživatele **pro obnovení hesla.**
1. Zadejte **název** toku uživatele. Například *passwordreset1*.
1. U **zprostředkovatelů identit**povolte **resetování hesla pomocí e-mailové adresy**.
1. V části Deklarace aplikace klikněte na **Zobrazit další** a zvolte deklarace identity, které chcete vrátit v tokenech autorizace odeslaných zpět do vaší aplikace. Vyberte například **ID objektu uživatele**.
1. Klikněte na tlačítko **OK**.
1. Kliknutím na **Vytvořit** přidejte tok uživatele. K názvu se automaticky připojí předpona *B2C_1.*

### <a name="test-the-user-flow"></a>Testování toku uživatele

1. Vyberte uživatelský tok, který jste vytvořili, otevřete stránku s přehledem a pak vyberte **Spustit tok uživatele**.
1. V **aplikaci**vyberte webovou aplikaci s názvem *webapp1,* kterou jste dříve zaregistrovali. Adresa **URL** odpovědi `https://jwt.ms`by se měla zobrazit .
1. Klikněte na **Spustit tok uživatele**, ověřte e-mailovou adresu účtu, který jste dříve vytvořili, a vyberte **Pokračovat**.
1. Nyní máte možnost změnit heslo pro uživatele. Změňte heslo a vyberte **pokračovat**. Token je vrácena `https://jwt.ms` a měla by být zobrazena na vás.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili:

> [!div class="checklist"]
> * Vytvoření toku registrace a přihlášení uživatele
> * Vytvoření toku uživatele pro úpravy profilu
> * Vytvoření toku uživatele pro obnovení hesla

Dále se dozvíte o přidávání poskytovatelů identit do vašich aplikací, abyste mohli uživateli přihlašovat se pomocí poskytovatelů, jako jsou Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft nebo Twitter.

> [!div class="nextstepaction"]
> [Přidání zprostředkovatelů identit do aplikací >](tutorial-add-identity-providers.md)