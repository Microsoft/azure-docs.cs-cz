---
title: Kurz – vytvoření toky uživatelů v Azure Active Directory B2C | Dokumentace Microsoftu
description: Naučíte se vytvářet toky uživatelů pro aplikace v Azure Active Directory B2C na webu Azure portal.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/11/2019
ms.author: davidmu
ms.openlocfilehash: 6e651d23e3b5cced78088d59979507eb09723165
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845588"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Kurz: Vytvořit toky uživatelů v Azure Active Directory B2C

Ve svých aplikacích můžete mít [toky uživatelů](active-directory-b2c-reference-policies.md) umožňující uživatelům zaregistrovat, přihlaste se nebo spravovat svůj profil. Můžete vytvořit několik toků uživatelů z různých typů ve vašem tenantovi Azure Active Directory (Azure AD) B2C a podle potřeby využít ve svých aplikacích. Toky uživatelů můžete použít opakovaně napříč aplikacemi.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořit tok registrace a přihlášení uživatele
> * Vytvořit tok uživatele upravování profilu
> * Vytvořit tok uživatele resetování hesla

V tomto kurzu se dozvíte, jak vytvořit některé doporučené uživatele toky s využitím webu Azure portal. Pokud hledáte informace o tom, jak nastavit přihlašovacího hesla vlastníka prostředku (ROPC) flow ve vaší aplikaci, přečtěte si téma [konfigurovat tok přihlašovacího hesla vlastníka prostředku v Azure AD B2C](configure-ropc.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

[Registrace aplikace](tutorial-register-applications.md) , které jsou součástí toky uživatelů, kterou chcete vytvořit. 

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Vytvořit tok registrace a přihlášení uživatele

Tok uživatele. Zaregistrujte se a přihlaste se zpracovává prostředí jak registrace a přihlášení s konfigurací jedné. Uživatelům vaší aplikace jsou vedla dolů správné cestě, v závislosti na kontextu.

1. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.

    ![Přepnout na adresář předplatného](./media/tutorial-create-user-flows/switch-directories.png)

2. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
3. V nabídce vlevo vyberte **toky uživatelů**a pak vyberte **nový tok uživatele**.

    ![Vyberte nový tok uživatele](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

4. Vyberte **registrace a přihlášení** tok uživatele na kartě doporučené.

    ![Vyberte tok registrace a přihlášení uživatele](./media/tutorial-create-user-flows/signup-signin-type.png)

5. Zadejte **název** pro tok uživatele. Například *signupsignin1*.
6. Pro **zprostředkovatelé Identity**vyberte **e-mailová registrace**.

    ![Nastavit vlastnosti toku](./media/tutorial-create-user-flows/signup-signin-properties.png)

7. Pro **atributy uživatele a deklarace identity**, zvolte deklarace identity a atributy, které chcete shromažďovat a odesílat od uživatele během registrace. Vyberte například **zobrazit více**a klikněte na tlačítko **země/oblast**, **zobrazovaný název**, a **PSČ**. Klikněte na **OK**.

    ![Vyberte atributy a deklarace identity](./media/tutorial-create-user-flows/signup-signin-attributes.png)

8. Klikněte na tlačítko **vytvořit** přidat tok uživatele. Předpona *B2C_1* se automaticky připojí k názvu.

### <a name="test-the-user-flow"></a>Testování tohoto toku uživatele

1. Na stránce Přehled toku uživatele, který jste vytvořili, vyberte **spustit tok uživatele**.
2. Pro **aplikace**, vyberte webovou aplikaci s názvem *webapp1* , které jste dříve zaregistrovali. **Adresy URL odpovědi** by se zobrazit `https://jwt.ms`.
3. Klikněte na tlačítko **spustit tok uživatele**a pak vyberte **zaregistrujte se hned teď**.

    ![Spuštění toku uživatele](./media/tutorial-create-user-flows/signup-signin-run-now.png)

4. Zadejte platnou e-mailovou adresu, klikněte na tlačítko **poslat ověřovací kód**a pak zadejte ověřovací kód, který se zobrazí.
5. Zadejte nové heslo a potvrzení hesla.
6. Zadejte název, který chcete zobrazit, vyberte svou zemi a oblast, zadejte poštovní směrovací číslo a potom klikněte na tlačítko **vytvořit**. Token, který je vrácen do `https://jwt.ms` a má být zobrazena na vás.
7. Nyní můžete spustit tok uživatele znovu a by měl být schopní se přihlásit pomocí účtu, který jste vytvořili. Vrácený token obsahuje deklarace, které jste vybrali název země, PSČ.

## <a name="create-a-profile-editing-user-flow"></a>Vytvořit tok uživatele upravování profilu

Pokud chcete povolit uživatelům upravovat svůj profil ve vaší aplikaci, použijte tok uživatele upravování profilu.

1. V nabídce vlevo vyberte **toky uživatelů**a pak vyberte **nový tok uživatele**.
2. Vyberte **upravování profilu** tok uživatele na kartě doporučené.
3. Zadejte **název** pro tok uživatele. Například *profileediting1*.
4. Pro **zprostředkovatelé Identity**vyberte **přihlášení místním účtem**.
5. Pro **atributy uživatele**, zvolte atributy, které chcete, aby zákazníkům mít možnost upravovat ve svém profilu. Vyberte například **zobrazit více**a klikněte na tlačítko **zobrazovaný název** a **pracovní pozice**. Klikněte na **OK**.
6. Klikněte na tlačítko **vytvořit** přidat tok uživatele. Předpona *B2C_1* se automaticky připojí k názvu.

### <a name="test-the-user-flow"></a>Testování tohoto toku uživatele

1. Na stránce Přehled toku uživatele, který jste vytvořili, vyberte **spustit tok uživatele**.
2. Pro **aplikace**, vyberte webovou aplikaci s názvem *webapp1* , které jste dříve zaregistrovali. **Adresy URL odpovědi** by se zobrazit `https://jwt.ms`.
3. Klikněte na tlačítko **spustit tok uživatele**a pak se přihlaste pomocí účtu, který jste předtím vytvořili.
4. Teď máte příležitost změnit zobrazované jméno nebo funkce pro daného uživatele. Klikněte na **Pokračovat**. Token, který je vrácen do `https://jwt.ms` a má být zobrazena na vás.

## <a name="create-a-password-reset-user-flow"></a>Vytvořit tok uživatele resetování hesla

Je možné, že vám umožňují uživatelům vaší aplikace pro resetování hesla v případě potřeby. Pokud chcete povolit resetování hesla, použijte tok uživatele resetování hesla.

1. V nabídce vlevo vyberte **toky uživatelů**a pak vyberte **nový tok uživatele**.
2. Vyberte **resetování hesla** tok uživatele na kartě doporučené.
3. Zadejte **název** pro tok uživatele. Například *passwordreset1*.
4. Pro **zprostředkovatelé Identity**, povolte **resetovat heslo pomocí e-mailovou adresu**.
5. V části deklarace identit aplikace, klikněte na **zobrazit více** a zvolte deklarace identit, které se mají vracet v autorizačních tokenech odesílaných zpět do aplikace. Vyberte například **ID objektu uživatele**.
6. Klikněte na **OK**.
7. Klikněte na tlačítko **vytvořit** přidat tok uživatele. Předpona *B2C_1* se automaticky připojí k názvu.

### <a name="test-the-user-flow"></a>Testování tohoto toku uživatele

1. Na stránce Přehled toku uživatele, který jste vytvořili, vyberte **spustit tok uživatele**.
2. Pro **aplikace**, vyberte webovou aplikaci s názvem *webapp1* , které jste dříve zaregistrovali. **Adresy URL odpovědi** by se zobrazit `https://jwt.ms`.
3. Klikněte na tlačítko **spustit tok uživatele**a pak se přihlaste pomocí účtu, který jste předtím vytvořili.
4. Teď máte příležitost změnit heslo pro uživatele. Klikněte na **Pokračovat**. Token, který je vrácen do `https://jwt.ms` a má být zobrazena na vás.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytvořit tok registrace a přihlášení uživatele
> * Vytvořit tok uživatele upravování profilu
> * Vytvořit tok uživatele resetování hesla

> [!div class="nextstepaction"]
> [Přizpůsobení uživatelského rozhraní svých aplikací v Azure Active Directory B2C](tutorial-customize-ui.md)