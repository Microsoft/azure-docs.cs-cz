---
title: 'Kurz: Integrace Azure Active Directory s IdeaScale | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a IdeaScale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55dafb074375cc310daa747c19503f23eff6ec18
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56879216"
---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Kurz: Integrace Azure Active Directory s IdeaScale

V tomto kurzu se dozvíte, jak integrovat IdeaScale s Azure Active Directory (Azure AD).
IdeaScale integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k IdeaScale.
* Můžete povolit uživatelům být automaticky přihlášeni k IdeaScale (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s IdeaScale, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* IdeaScale jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje IdeaScale **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-ideascale-from-the-gallery"></a>Přidání IdeaScale z Galerie

Konfigurace integrace IdeaScale do služby Azure AD, budete muset přidat IdeaScale z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat IdeaScale z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **IdeaScale**vyberte **IdeaScale** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![IdeaScale v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí IdeaScale podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v IdeaScale.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s IdeaScale, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace IdeaScale Single Sign-On](#configure-ideascale-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele IdeaScale](#create-ideascale-test-user)**  – Pokud chcete mít protějšek Britta Simon IdeaScale, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s IdeaScale, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **IdeaScale** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![IdeaScale domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.ideascale.com`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce:
    | |
    |--|
    | `http://<companyname>.ideascale.com`  |
    | `https://<companyname>.ideascale.com` |

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory IdeaScale klienta](https://support.ideascale.com/) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavení IdeaScale** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-ideascale-single-sign-on"></a>Konfigurace IdeaScale jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti IdeaScale jako správce.

2. Přejděte na **komunity nastavení**.

    ![Nastavení komunity](./media/ideascale-tutorial/ic790847.png "nastavení komunity")

3. Přejděte na **zabezpečení \> jednotného přihlášení nastavení**.

    ![Jednotné přihlášení nastavení](./media/ideascale-tutorial/ic790848.png "jednotného přihlášení nastavení")

4. Jako **typ jednotného přihlašování**vyberte **SAML 2.0**.

    ![Jednotné přihlášení typu](./media/ideascale-tutorial/ic790849.png "jeden typ přihlášení")

5. Na **nastavení jednotného přihlášení** dialogového okna, proveďte následující kroky:

    ![Jednotné přihlášení nastavení](./media/ideascale-tutorial/ic790850.png "jednotného přihlášení nastavení")

    a. V **ID Entity SAML zprostředkovatele identity** textového pole vložte hodnotu **Azure Ad identifikátor** zkopírovanou z webu Azure portal.

    b. Otevřete soubor metadat stažené z webu Azure portal do poznámkového bloku a zkopírujte jeho obsah a vložte do **metadat SAML zprostředkovatele identity** textového pole.

    c. V **úspěch odhlašovací adresa URL** textového pole vložte hodnotu **odhlašovací adresa URL** zkopírovanou z webu Azure portal.

    d. Klikněte na tlačítko **uložit změny**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k IdeaScale použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **IdeaScale**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **IdeaScale**.

    ![Odkaz IdeaScale v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-ideascale-test-user"></a>Vytvoření IdeaScale testovacího uživatele

Umožňuje uživatelům Azure AD k přihlášení do IdeaScale, musí být poskytnuty v k IdeaScale. V případě IdeaScale zřizování se ruční úlohy.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **IdeaScale** společnosti serveru jako správce.

2. Přejděte na **komunity nastavení**.

    ![Nastavení komunity](./media/ideascale-tutorial/ic790847.png "nastavení komunity")

3. Přejděte na **základní nastavení \> člen správu**.

4. Klikněte na tlačítko **přidat člena**.

    ![Správa členů](./media/ideascale-tutorial/ic790852.png "člen správy")

5. V části přidat nového člena proveďte následující kroky:

    ![Přidání nového člena](./media/ideascale-tutorial/ic790853.png "přidat nového člena")

    a. V **e-mailové adresy** textové pole, zadejte e-mailovou adresu platnou Azure AD účtu, který jste ke zřízení.

    b. Klikněte na tlačítko **uložit změny**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail s odkazem pro potvrzení účtu, pak se změní na aktivní.

> [!NOTE]
> Můžete použít jakékoli jiné IdeaScale uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných IdeaScale uživatelským účtům, zřídit AAD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici IdeaScale na přístupovém panelu, můžete by měl být automaticky přihlášeni k IdeaScale, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

