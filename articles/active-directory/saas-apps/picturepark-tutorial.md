---
title: 'Kurz: Azure Active Directory integrace s Picturepark | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Picturepark.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 8a00cf11edfea2e732a18a392d465525b38ea45f
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520838"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Kurz: Azure Active Directory integrace s Picturepark

V tomto kurzu se dozvíte, jak integrovat Picturepark s Azure Active Directory (Azure AD).
Integrace Picturepark s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Picturepark.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Picturepark (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s Picturepark potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povoleným Pictureparkm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Picturepark podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-picturepark-from-the-gallery"></a>Přidání Picturepark z Galerie

Pokud chcete nakonfigurovat integraci Picturepark do služby Azure AD, musíte přidat Picturepark z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Picturepark z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Picturepark**, vyberte **Picturepark** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Picturepark v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Picturepark na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Picturepark.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Picturepark, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Picturepark](#configure-picturepark-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření Picturepark Test User](#create-picturepark-test-user)** – pro Britta Simon v Picturepark, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Picturepark, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Picturepark** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně Picturepark a adresách URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.picturepark.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:

    ```http
        https://<companyname>.current-picturepark.com
        https://<companyname>.picturepark.com
        https://<companyname>.next-picturepark.com
    ```

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Picturepark](https://picturepark.com/company/picturepark-customer-support) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. V části **podpisový certifikát SAML** kliknutím na tlačítko **Upravit** otevřete dialogové okno **podpisový certifikát SAML** .

    ![Upravit podpisový certifikát SAML](common/edit-certificate.png)

6. V části **podpisový certifikát SAML** zkopírujte **kryptografický otisk** a uložte ho do svého počítače.

    ![Kopírovat hodnotu kryptografického otisku](common/copy-thumbprint.png)

7. V části **Nastavení Picturepark** zkopírujte příslušné adresy URL podle vašich požadavků. Pro **přihlašovací adresu URL**použijte hodnotu s následujícím vzorem: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ je ID tenanta předplatného služby Azure AD.

    ![Kopírovat adresy URL konfigurace](./media/picturepark-tutorial/configurls.png)

    a. Identifikátor Azure AD

    b. Odhlašovací adresa URL

### <a name="configure-picturepark-single-sign-on"></a>Nakonfigurovat Picturepark jednu Sign-On

1. V jiném okně webového prohlížeče se přihlaste k webu Picturepark společnosti jako správce.

2. Na panelu nástrojů v horní části klikněte na **Nástroje pro správu**a potom klikněte na **Konzola pro správu**.
   
    ![Konzola pro správu](./media/picturepark-tutorial/ic795062.png "Konzola pro správu")

3. Klikněte na **ověřování**a potom klikněte na **Zprostředkovatelé identity**.
   
    ![Authentication](./media/picturepark-tutorial/ic795063.png "Authentication")

4. V části **Konfigurace zprostředkovatele identity** proveďte následující kroky:
   
    ![Konfigurace zprostředkovatele identity](./media/picturepark-tutorial/ic795064.png "Konfigurace zprostředkovatele identity")
   
    a. Klikněte na **Přidat**.
  
    b. Zadejte název konfigurace.
   
    c. Vyberte **nastavit jako výchozí**.
   
    d. Do textového pole **identifikátor URI vystavitele** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.
   
    e. V poli **Miniatura důvěryhodného vystavitele** pole pro tisk vložte hodnotu **kryptografického otisku** , který jste zkopírovali z části **podpisový certifikát SAML** . 

5. Klikněte na **JoinDefaultUsersGroup**.

6. Pokud chcete nastavit atribut **EmailAddress** v textovém poli **deklarace identity** , zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` a klikněte na **Uložit**.

      ![Konfigurace](./media/picturepark-tutorial/ic795065.png "Konfigurace")

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension` . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Picturepark.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Picturepark**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Picturepark**.

    ![Odkaz Picturepark v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-picturepark-test-user"></a>Vytvořit testovacího uživatele Picturepark

Aby se uživatelé Azure AD mohli přihlašovat k Picturepark, musí se zřídit v Picturepark. V případě Picturepark je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se ke svému tenantovi **Picturepark** .

1. Na panelu nástrojů v horní části klikněte na **Nástroje pro správu**a potom klikněte na **Uživatelé**.
   
    ![Uživatelé](./media/picturepark-tutorial/ic795067.png "Uživatelé")

1. Na kartě **Přehled uživatelů** klikněte na **Nový**.
   
    ![Správa uživatelů](./media/picturepark-tutorial/ic795068.png "Správa uživatelů")

1. V dialogovém okně **vytvořit uživatele** proveďte následující kroky platného Azure Active Directoryho uživatele, který chcete zřídit:
   
    ![Vytvořit uživatele](./media/picturepark-tutorial/ic795069.png "Vytvořit uživatele")
   
    a. Do textového pole **e-mailová adresa** zadejte **e-mailovou adresu** uživatele `BrittaSimon@contoso.com` .  
   
    b. Do textových polí **heslo** a **potvrzení hesla** zadejte **heslo** BrittaSimon. 
   
    c. Do textového pole **jméno a příjmení** zadejte **křestní jméno** uživatele **Britta**. 
   
    d. Do textového pole **příjmení** zadejte **jméno** uživatele **Simon**.
   
    e. Do textového pole **Společnost** zadejte **název společnosti** uživatele. 
   
    f. V textovém poli **země** vyberte **zemi nebo oblast** uživatele.
  
    například Do textového pole **zip** **Zadejte PSČ města** .
   
    h. Do textového pole **město** zadejte **jméno města** uživatele.

    i. Vyberte **jazyk**.
   
    j. Klikněte na **Vytvořit**.

>[!NOTE]
>K zřizování uživatelských účtů Azure AD můžete použít jiné nástroje pro vytváření uživatelských účtů Picturepark nebo rozhraní API poskytovaná Picturepark.
>

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Picturepark, měli byste se automaticky přihlásit k Picturepark, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)