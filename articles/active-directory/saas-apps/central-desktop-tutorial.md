---
title: 'Kurz: Azure Active Directory integrace s centrální plochou | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a centrální plochou.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.openlocfilehash: 321c6bf52f4dac636f6bebb3a79d3a206c2ea11b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92456428"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Kurz: Azure Active Directory integrace s centrální plochou

V tomto kurzu se dozvíte, jak integrovat centrální plochu pomocí Azure Active Directory (Azure AD).
Integrace centrální plochy s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k centrální ploše.
* Uživatelům můžete povolit, aby se automaticky přihlásili k nástroji Central Desktop (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s nástrojem Central Desktop potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné centrálního jednotného přihlašování s povoleným jednoduchým desktopem

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Centrální plocha podporuje jednotné přihlašování na webu **SP**

## <a name="adding-central-desktop-from-the-gallery"></a>Přidání centrální plochy z Galerie

Pokud chcete nakonfigurovat integraci centrální plochy do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat centrální plochu z galerie.

**Chcete-li přidat centrální plochu z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Central Desktop**, vyberte **centrální plocha** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Centrální plocha v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí centrální plochy na základě testovacího uživatele s názvem **Britta Simon**.
Aby bylo jednotné přihlašování fungovat, je třeba vytvořit odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v nástroji Central Desktop.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí centrální plochy, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurovat jednotné přihlašování pro centrální plochu](#configure-central-desktop-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte uživatele centrální plochy pro testování](#create-central-desktop-test-user)** , abyste měli protějšek Britta Simon v centrální ploše, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí centrální plochy, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **centrální plochy** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování domény a adresy URL centrální plochy](common/sp-identifier-reply.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.centraldesktop.com`

    b. Do pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:
    
    ```http
    https://<companyname>.centraldesktop.com/saml2-metadata.php
    https://<companyname>.imeetcentral.com/saml2-metadata.php
    ```

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.centraldesktop.com/saml2-assertion.php`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným Sign-On URL, identifikátor a adresu URL odpovědi. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory pro klienta centrální plochy](https://imeetcentral.com/contact-us) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na **Stáhnout** a Stáhněte si **certifikát (RAW)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificateraw.png)

6. V části **nastavit centrální plochu** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-central-desktop-single-sign-on"></a>Konfigurace jednoduchého Sign-On pro centrální Desktop

1. Přihlaste se ke svému klientovi **centrální plochy** .

2. Přejít na **Nastavení**. Vyberte **Upřesnit**a pak vyberte **jednotné přihlašování**.

    ![Nastavení – rozšířené](./media/central-desktop-tutorial/ic769563.png "Nastavení – rozšířené")

3. Na stránce **nastavení jednotného přihlašování** proveďte následující kroky:

    ![Nastavení jednotného přihlašování](./media/central-desktop-tutorial/ic769564.png "Nastavení jednotného přihlašování")

    a. Vyberte možnost **Povolit jednotné přihlašování SAML v2**.

    b. Do pole **Adresa URL jednotného přihlašování** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    c. Do pole **Adresa URL pro přihlášení SSO** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    d. Do pole **Adresa URL pro odhlášení jednotného přihlašování** vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

4. V části **metoda ověření podpisu zprávy** proveďte následující kroky:

    ![Metoda ověření podpisu zprávy](./media/central-desktop-tutorial/ic769565.png "Metoda ověření podpisu zprávy")
    
    a. Vyberte **Certifikát**.

    b. V seznamu **certifikát jednotného přihlašování** vyberte **rsh SHA256**.

    c. Otevřete stažený certifikát v programu Poznámkový blok. Pak zkopírujte obsah certifikátu a vložte ho do pole **certifikát jednotného přihlašování** .

    d. Vyberte **Zobrazit odkaz na přihlašovací stránku SAMLv2**.

    e. Vyberte **Aktualizovat**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k centrální ploše.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte **centrální plocha**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **centrální plocha**.

    ![Odkaz na centrální plochu v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-central-desktop-test-user"></a>Vytvořit testovacího uživatele pro centrální plochu

Aby se uživatelé Azure AD mohli přihlásit, musí se zřídit v aplikaci centrální plochy. Tato část popisuje, jak vytvořit uživatelské účty Azure AD v centrální ploše.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů nebo rozhraní API centrální plochy, které poskytuje centrální plocha.

**Postup při zřizování uživatelských účtů na Central Desktop:**

1. Přihlaste se ke svému klientovi centrální plochy.

2. Vyberte **lidé** a pak vyberte **Přidat interní členy**.

    ![People](./media/central-desktop-tutorial/ic781051.png "People")

3. Do pole **e-mailová adresa nového člena** zadejte účet služby Azure AD, který chcete zřídit, a pak vyberte **Další**.

    ![E-mailové adresy nových členů](./media/central-desktop-tutorial/ic781052.png "E-mailové adresy nových členů")

4. Vyberte **Přidat interní členy**.

    ![Přidat interního člena](./media/central-desktop-tutorial/ic781053.png "Přidat interního člena")
  
   > [!NOTE]
   > Uživatelé, které přidáte, obdrží e-mail, který obsahuje potvrzovací odkaz pro aktivaci jejich účtů.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici centrální plocha na přístupovém panelu, měli byste být automaticky přihlášeni k centrální ploše, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)