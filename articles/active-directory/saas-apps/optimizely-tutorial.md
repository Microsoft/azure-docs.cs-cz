---
title: 'Kurz: Integrace Azure Active Directory s optimalizací | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a optimalizuje.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 2e25c615e040dd4359e278b95045fbc71ca60ef1
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68943956"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Kurz: Integrace Azure Active Directory s optimalizací

V tomto kurzu se naučíte integrovat optimalizaci s Azure Active Directory (Azure AD).
Integrace s Azure AD nabízí následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají k optimalizaci přístup.
* Uživatelům můžete povolit, aby se automaticky přihlásili k optimalizaci (jednotné přihlašování) se svými účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s optimalizací potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Optimalizuje odběr s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Optimalizace podporuje jednotné přihlašování spouštěné přes **SP**

## <a name="adding-optimizely-from-the-gallery"></a>Optimalizace se optimalizuje z galerie.

Ke konfiguraci integrace optimalizuje do Azure AD je potřeba přidat optimalizaci z Galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat optimalizaci z Galerie, proveďte následující kroky:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **optimalizované**, vyberte **optimalizuje** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Optimalizace v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s optimalizací na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, je potřeba vytvořit odkazový vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci optimalizace.

Ke konfiguraci a testování jednotného přihlašování Azure AD s optimalizací je potřeba, abyste dokončili tyto stavební bloky:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Nakonfigurujte optimalizované jednotné přihlašování](#configure-optimizely-single-sign-on)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořte optimalizuje testovacího uživatele](#create-optimizely-test-user)** , abyste měli protějšek Britta Simon v optimalizuje, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete jednotné přihlašování Azure AD nakonfigurovat s optimalizací, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **optimalizace** integrace aplikací vyberte **jednotné přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Optimalizuje informace jednotného přihlašování v doménových a adresách URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://app.optimizely.net/<instance name>`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`urn:auth0:optimizely:contoso`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Tuto hodnotu aktualizujete skutečnou přihlašovací adresou URL a identifikátorem, který je vysvětlen později v tomto kurzu. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Vaše optimalizovaná aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno **atributy uživatele** .

    ![image](common/edit-attribute.png)

6. Kromě toho aplikace optimalizované pro optimalizaci očekává, že se v odpovědi SAML zpátky vrátí několik atributů. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulce:

    | Name | Zdrojový atribut |
    | ---------------| --------------- |
    | email | user.mail |
    
    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

4. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **nastavit** s optimalizací zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-optimizely-single-sign-on"></a>Konfigurovat optimalizaci jednotného přihlašování

1. Pokud chcete jednotné přihlašování nakonfigurovat na straně **optimalizace** , obraťte se na svého správce optimalizovaného účtu a poskytněte stažený **certifikát (Base64)** a příslušné zkopírované adresy URL.

2. V reakci na váš e-mail vám optimalizuje i adresu URL pro přihlášení (SSO iniciované nástrojem SP) a identifikátor (ID entity poskytovatele služeb).

    a. Zkopírujte **adresu URL jednotného přihlašování iniciované nástrojem SP** a vložte ji do textového pole **Signing URL** v **základní části Konfigurace SAML** na Azure Portal.

    b. Zkopírujte **ID entity poskytovatele služby** poskytnuté optimalizací a vložte je do textového pole identifikátoru v **základní části konfigurace SAML** na Azure Portal.

3. V jiném okně prohlížeče se přihlaste k optimalizované aplikaci.

4. Klikněte na název účtu v pravém horním rohu a pak na **Nastavení účtu**.

    ![Jednotné přihlašování Azure AD](./media/optimizely-tutorial/tutorial_optimizely_09.png)

5. Na kartě účet zaškrtněte políčko **Povolit jednotné přihlašování** v části jednotné přihlašování v části **Přehled** .
  
    ![Jednotné přihlašování Azure AD](./media/optimizely-tutorial/tutorial_optimizely_10.png)

6. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole typ **uživatelského jména** **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k optimalizaci.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **optimalizovat**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **optimalizovat**.

    ![Odkaz optimalizovaného v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-optimizely-test-user"></a>Vytvořit optimalizuje testovacího uživatele

V této části vytvoříte v optimalizuje uživatele s názvem Britta Simon.

1. Na domovské stránce vyberte kartu spolupracovníci.

2. Chcete-li do projektu přidat nového spolupracovníka, klikněte na tlačítko **Nový**spolupracovníka.
   
    ![Vytváří se testovací uživatele služby Azure AD](./media/optimizely-tutorial/create_aaduser_10.png)

3. Zadejte e-mailovou adresu a přiřaďte jim roli. Klikněte na **pozvat**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/optimizely-tutorial/create_aaduser_11.png)

4. Obdrží pozvání k odeslání e-mailu. Pomocí e-mailové adresy se musí přihlásit k optimalizaci.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici optimalizované k optimalizaci, měli byste být automaticky přihlášeni k optimalizaci, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

