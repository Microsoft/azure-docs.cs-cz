---
title: 'Kurz: Integrace Azure Active Directory s Appraisd | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Appraisd.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 44d4988ff68d5ccca575fc29ec3f0b7dce0bf948
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55765428"
---
# <a name="tutorial-azure-active-directory-integration-with-appraisd"></a>Kurz: Integrace Azure Active Directory s Appraisd

V tomto kurzu se dozvíte, jak integrovat Appraisd s Azure Active Directory (Azure AD).
Appraisd integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Appraisd.
* Můžete povolit uživatelům být automaticky přihlášeni k Appraisd (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Appraisd, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Appraisd jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Appraisd **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí

## <a name="adding-appraisd-from-the-gallery"></a>Přidání Appraisd z Galerie

Konfigurace integrace Appraisd do služby Azure AD, budete muset přidat Appraisd z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Appraisd z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Appraisd**vyberte **Appraisd** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Appraisd v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Appraisd podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Appraisd.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Appraisd, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Appraisd Single Sign-On](#configure-appraisd-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Appraisd](#create-appraisd-test-user)**  – Pokud chcete mít protějšek Britta Simon Appraisd, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Appraisd, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Appraisd** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavení jednotného přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Appraisd domény a adresy URL jednotného přihlašování – informace](common/both-preintegrated-advanced-urls.png)

    a. Klikněte na tlačítko **nastavit další adresy URL**.

    b. V **stav přenosu** textové pole, zadejte adresu URL: `<TENANTCODE>`

    c. Pokud chcete nakonfigurovat aplikace v **SP** zahájeno v režimu **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Na stránce Konfigurace jednotného přihlašování Appraisd, který je vysvětlen později v tomto kurzu získáte skutečnou hodnotu přihlašovací adresu URL a stav přenosu.

5. Appraisd aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

6. V **deklarace identity uživatelů** části na **atributy uživatele** dialogovém okně Upravit deklarace identity pomocí **ikonu pro úpravu** nebo přidání deklarace identity pomocí **přidat novou deklaraci**ke konfiguraci atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    | Název |  Zdrojový atribut|
    | ---------------| --------------- |
    | NameIdentifier | user.mail |
    | | |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

8. Na **nastavení Appraisd** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-appraisd-single-sign-on"></a>Konfigurace Appraisd jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k Appraisd jako správce zabezpečení.

2. V horní části stránky, klikněte na **nastavení** ikonu, přejděte na **konfigurace**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

3. V levé nabídce klikněte na **SAML jednotného přihlašování**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

4. Na **konfigurace SAML 2.0 Single Sign-On** stránce, proveďte následující kroky:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Kopírovat **výchozí stav přenosu** hodnotu a vložte ji **stav přenosu** textového pole v **základní konfiguraci SAML** na portálu Azure portal.

    b. Kopírovat **spouštěných službou přihlašovací adresa URL** hodnotu a vložte ji **přihlašovací adresa URL** textového pole v **základní konfiguraci SAML** na portálu Azure portal.

5. Přejděte dolů na stejné stránce v části **identifikace uživatelů**, proveďte následující kroky:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. V **jednotné přihlašování – adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z webu Azure portal a klikněte na tlačítko **Uložit**.

    b. V **adresa URL Vystavitel zprostředkovatele Identity** textového pole vložte hodnotu **Azure Ad identifikátor**, který jste zkopírovali z webu Azure portal a klikněte na tlačítko **Uložit**.

    c. V poznámkovém bloku otevřete base-64 kódovaných certifikát, který jste si stáhli z webu Azure portal, zkopírujte jeho obsah a vložte jej do **certifikát X.509** pole a klikněte na tlačítko **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Appraisd použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Appraisd**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Appraisd**.

    ![Odkaz Appraisd v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-appraisd-test-user"></a>Vytvoření Appraisd testovacího uživatele

Povolení služby Azure AD uživatelé přihlašují k Appraisd, se musí být poskytnuty do Appraisd. Zřizování v Appraisd, je ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k Appraisd jako správce zabezpečení.

2. V horní části stránky, klikněte na **nastavení** ikonu, přejděte na **Centrum správy**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Na panelu nástrojů v horní části stránky klikněte na tlačítko **lidé**, přejděte na **přidat nového uživatele**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Na **přidat nového uživatele** stránce, proveďte následující kroky:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. V **křestní jméno** textové pole, zadejte jméno uživatele, jako je **Britta**.

    b. V **příjmení** textové pole, zadejte příjmení uživatele, jako je **simon**.

    c. V **e-mailu** textové pole, zadejte e-mailu uživatele, jako je **Brittasimon@contoso.com**.

    d. Klikněte na **Přidat uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Appraisd na přístupovém panelu, můžete by měl být automaticky přihlášeni k Appraisd, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
