---
title: Kurz – vytvoření toky uživatelů – Azure Active Directory B2C
description: Naučíte se vytvářet toky uživatelů na webu Azure portal povolit přihlašování nahoru, přihlaste se a upravování profilu uživatele pro vaše aplikace v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 111196388d0e17ecde8a2055959f2f573e43ade8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056340"
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

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.

    ![Přepnout na adresář předplatného](./media/tutorial-create-user-flows/switch-directories.PNG)

1. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
1. V nabídce vlevo v části **zásady**vyberte **toky uživatelů (zásady)** a pak vyberte **nový tok uživatele**.

    ![Vyberte nový tok uživatele](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Na **doporučená** kartu, vyberte **podepsat a přihlašování** tok uživatele.

    ![Vyberte tok registrace a přihlášení uživatele](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Zadejte **název** pro tok uživatele. Například *signupsignin1*.
1. Pro **zprostředkovatelé Identity**vyberte **e-mailová registrace**.

    ![Nastavit vlastnosti toku](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. Pro **atributy uživatele a deklarace identity**, zvolte deklarace identity a atributy, které chcete shromažďovat a odesílat od uživatele během registrace. Vyberte například **zobrazit více**a klikněte na tlačítko atributech a deklaracích identit pro **země/oblast**, **zobrazovaný název**, a **PSČ**. Klikněte na **OK**.

    ![Vyberte atributy a deklarace identity](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Klikněte na tlačítko **vytvořit** přidat tok uživatele. Předpona *B2C_1* se automaticky připojí k názvu.

### <a name="test-the-user-flow"></a>Testování tohoto toku uživatele

1. Vyberte tok uživatele, který jste vytvořili, otevřete jeho stránku přehledu a pak vyberte **spustit tok uživatele**.
1. Pro **aplikace**, vyberte webovou aplikaci s názvem *webapp1* , které jste dříve zaregistrovali. **Adresy URL odpovědi** by se zobrazit `https://jwt.ms`.
1. Klikněte na tlačítko **spustit tok uživatele**a pak vyberte **zaregistrujte se hned teď**.

    ![Spuštění toku uživatele](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Zadejte platnou e-mailovou adresu, klikněte na tlačítko **poslat ověřovací kód**, zadejte ověřovací kód, který zobrazí a pak vyberte **ověřit kód**.
1. Zadejte nové heslo a potvrzení hesla.
1. Vyberte svou zemi a oblast, zadejte název, který chcete zobrazit, zadejte poštovní směrovací číslo a potom klikněte na tlačítko **vytvořit**. Token, který je vrácen do `https://jwt.ms` a má být zobrazena na vás.
1. Nyní můžete spustit tok uživatele znovu a by měl být schopní se přihlásit pomocí účtu, který jste vytvořili. Vrácený token obsahuje deklarace, které jste vybrali země/oblast, jméno a PSČ.

## <a name="create-a-profile-editing-user-flow"></a>Vytvořit tok uživatele upravování profilu

Pokud chcete povolit uživatelům upravovat svůj profil ve vaší aplikaci, použijte tok uživatele upravování profilu.

1. V levé nabídce na stránce s přehledem tenanta Azure AD B2C vyberte **toky uživatelů (zásady)** a pak vyberte **nový tok uživatele**.
1. Vyberte **upravování profilu** tok uživatele na kartě doporučené.
1. Zadejte **název** pro tok uživatele. Například *profileediting1*.
1. Pro **zprostředkovatelé Identity**vyberte **přihlášení místním účtem**.
1. Pro **atributy uživatele**, zvolte atributy, které chcete, aby zákazníkům mít možnost upravovat ve svém profilu. Vyberte například **zobrazit více**a potom zvolte atributy a deklarací identity pro **zobrazovaný název** a **pracovní pozice**. Klikněte na **OK**.
1. Klikněte na tlačítko **vytvořit** přidat tok uživatele. Předpona *B2C_1* se automaticky připojí k názvu.

### <a name="test-the-user-flow"></a>Testování tohoto toku uživatele

1. Vyberte tok uživatele, který jste vytvořili, otevřete jeho stránku přehledu a pak vyberte **spustit tok uživatele**.
1. Pro **aplikace**, vyberte webovou aplikaci s názvem *webapp1* , které jste dříve zaregistrovali. **Adresy URL odpovědi** by se zobrazit `https://jwt.ms`.
1. Klikněte na tlačítko **spustit tok uživatele**a pak se přihlaste pomocí účtu, který jste předtím vytvořili.
1. Teď máte příležitost změnit zobrazované jméno nebo funkce pro daného uživatele. Klikněte na **Pokračovat**. Token, který je vrácen do `https://jwt.ms` a má být zobrazena na vás.

## <a name="create-a-password-reset-user-flow"></a>Vytvořit tok uživatele resetování hesla

Povolit uživatelům vaší aplikace k resetování hesla, použijte tok uživatele resetování hesla.

1. V nabídce vlevo vyberte **toky uživatelů (zásady)** a pak vyberte **nový tok uživatele**.
1. Vyberte **resetování hesla** tok uživatele na kartě doporučené.
1. Zadejte **název** pro tok uživatele. Například *passwordreset1*.
1. Pro **zprostředkovatelé Identity**, povolte **resetovat heslo pomocí e-mailovou adresu**.
1. V části deklarace identit aplikace, klikněte na **zobrazit více** a zvolte deklarace identit, které se mají vracet v autorizačních tokenech odesílaných zpět do aplikace. Vyberte například **ID objektu uživatele**.
1. Klikněte na **OK**.
1. Klikněte na tlačítko **vytvořit** přidat tok uživatele. Předpona *B2C_1* se automaticky připojí k názvu.

### <a name="test-the-user-flow"></a>Testování tohoto toku uživatele

1. Vyberte tok uživatele, který jste vytvořili, otevřete jeho stránku přehledu a pak vyberte **spustit tok uživatele**.
1. Pro **aplikace**, vyberte webovou aplikaci s názvem *webapp1* , které jste dříve zaregistrovali. **Adresy URL odpovědi** by se zobrazit `https://jwt.ms`.
1. Klikněte na tlačítko **spustit tok uživatele**, ověřte e-mailovou adresu účtu, který jste dříve vytvořili a vyberte **pokračovat**.
1. Teď máte příležitost změnit heslo pro uživatele. Změnit heslo a vyberte **pokračovat**. Token, který je vrácen do `https://jwt.ms` a má být zobrazena na vás.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytvořit tok registrace a přihlášení uživatele
> * Vytvořit tok uživatele upravování profilu
> * Vytvořit tok uživatele resetování hesla

V dalším kroku zjistěte, jak přidat zprostředkovatelů identity pro vaše aplikace umožňuje přihlášení uživatele pomocí poskytovatelů, jako je Azure AD, Amazon, Facebook, Githubu, LinkedIn, Microsoft nebo Twitter.

> [!div class="nextstepaction"]
> [Přidat zprostředkovatele identity pro vaše aplikace >](tutorial-add-identity-providers.md)