---
title: 'Kurz: Integrace Azure Active Directory s Ziflow | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Ziflow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: d9745bdb1cb6de86a96946564865958433d49732
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086198"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Kurz: Integrace Azure Active Directory s Ziflow

V tomto kurzu se dozvíte, jak integrovat Ziflow s Azure Active Directory (Azure AD).
Ziflow integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Ziflow.
* Můžete povolit uživatelům být automaticky přihlášeni k Ziflow (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Ziflow, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Ziflow jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Ziflow **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-ziflow-from-the-gallery"></a>Přidání Ziflow z Galerie

Konfigurace integrace Ziflow do služby Azure AD, budete muset přidat Ziflow z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Ziflow z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Ziflow**vyberte **Ziflow** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Ziflow v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Ziflow podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Ziflow.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Ziflow, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Ziflow Single Sign-On](#configure-ziflow-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Ziflow](#create-ziflow-test-user)**  – Pokud chcete mít protějšek Britta Simon Ziflow, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Ziflow, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Ziflow** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Ziflow domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > Předchozí hodnoty nejsou skutečný. Skutečné hodnoty, který je vysvětlen později v tomto kurzu jste se aktualizuje jedinečnou hodnotu ID v identifikátoru a přihlášení na adrese URL.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení Ziflow** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-ziflow-single-sign-on"></a>Konfigurace Ziflow jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k Ziflow jako správce zabezpečení.

2. Klikněte na miniaturu v pravém horním rohu a pak klikněte na tlačítko **spravovat účet**.

    ![Správa konfigurace Ziflow](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

3. V levé horní části, klikněte na tlačítko **Single Sign-On**.

    ![Konfigurace přihlašování Ziflow](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

4. Na **Single Sign-On** stránce, proveďte následující kroky:

    ![Konfigurace jednoho Ziflow](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Vyberte **typ** jako **SAML2.0**.

    b. V **přihlašování v adrese URL** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    c. Nahrát certifikát kódováním base-64, který jste si stáhli z portálu Azure portal do **X509 podpisový certifikát**.

    d. V **adresy URL odhlašovací** textového pole vložte hodnotu **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    e. Z **nastavení konfigurace pro poskytovatele identifikátor** části, zkopírujte zvýrazněné jedinečnou hodnotu ID a připojit ho s identifikátorem a adresa URL v přihlašování **základní konfiguraci SAML** v Azure portál.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole brittasimon@yourcompanydomain.extension. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Ziflow použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Ziflow**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Ziflow**.

    ![Odkaz Ziflow v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-ziflow-test-user"></a>Vytvoření Ziflow testovacího uživatele

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k Ziflow, musí být poskytnuty do Ziflow. Zřizování v Ziflow, je ruční úlohy.

K poskytnutí uživatelského účtu, postupujte následovně:

1. Přihlaste se k Ziflow jako správce zabezpečení.

2. Přejděte do **lidé** nahoře.

    ![Konfigurace Ziflow osoby](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Klikněte na tlačítko **přidat** a potom klikněte na tlačítko **přidat uživatele**.

    ![Konfigurace Ziflow přidávání uživatele](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. Na **přidání uživatele** automaticky otevírané okno, proveďte následující kroky:

    ![Konfigurace Ziflow přidávání uživatele](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. V **e-mailu** textové pole, zadejte e-mailu uživatele, jako je brittasimon@contoso.com.

    b. V **křestní jméno** textové pole, zadejte jméno uživatele, jako je Britta.

    c. V **příjmení** textové pole, zadejte příjmení uživatele, jako je Simon.

    d. Vyberte svou roli Ziflow.

    e. Klikněte na tlačítko **přidat 1 uživatel**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mailu a řídí se odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Ziflow na přístupovém panelu, můžete by měl být automaticky přihlášeni k Ziflow, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

