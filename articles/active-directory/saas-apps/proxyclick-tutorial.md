---
title: 'Kurz: Azure Active Directory integrace s Proxyclick | Microsoft Docs'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Proxyclick.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 23ae1a2c1371cda9435ea76f02cebc79c141c904
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522236"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Kurz: Azure Active Directory integrace s Proxyclick

V tomto kurzu se dozvíte, jak integrovat Proxyclick s Azure Active Directory (Azure AD).
Tato integrace poskytuje tyto výhody:

* Pomocí Azure AD můžete řídit, kdo má přístup k Proxyclick.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Proxyclick (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Pokud chcete nakonfigurovat integraci Azure AD s Proxyclick, musíte mít:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete se zaregistrovat k měsíční [zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Předplatné Proxyclick s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Proxyclick podporuje jednotné přihlašování iniciované v SP a IdP.

## <a name="add-proxyclick-from-the-gallery"></a>Přidání Proxyclick z Galerie

K nastavení integrace Proxyclick do služby Azure AD je nutné přidat Proxyclick z Galerie do seznamu spravovaných aplikací SaaS.

1. V [Azure Portal](https://portal.azure.com)v levém podokně vyberte **Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejít k **podnikovým aplikacím**  >  **všechny aplikace**:

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte v horní části okna možnost **Nová aplikace** :

    ![Vybrat novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Proxyclick**. Ve výsledcích hledání vyberte **Proxyclick** a pak vyberte **Přidat**.

     ![Výsledky hledání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Proxyclick pomocí testovacího uživatele s názvem Britta Simon.
Pokud chcete povolit jednotné přihlašování, musíte vytvořit relaci mezi uživatelem služby Azure AD a odpovídajícím uživatelem v Proxyclick.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Proxyclick, musíte provést tyto kroky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** , abyste funkci povolili uživatelům.
2. **[Konfigurace jednotného přihlašování Proxyclick](#configure-proxyclick-single-sign-on)** na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** povolení jednotného přihlašování Azure AD pro uživatele.
5. **[Vytvořte testovacího uživatele Proxyclick](#create-a-proxyclick-test-user)** , který je propojený s Předprezentací Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Proxyclick, proveďte tyto kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací Proxyclick vyberte **jednotné přihlašování**:

    ![Vyberte jednotné přihlašování.](common/select-sso.png)

2. V dialogovém okně **Vybrat metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** pro povolení jednotného přihlašování:

    ![Vyberte metodu jednotného přihlašování.](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** vyberte ikonu **Upravit** a otevřete DIALOGOVÉ okno **základní konfigurace SAML** :

    ![Ikona úprav](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované IdP, proveďte v dialogovém okně **základní konfiguraci SAML** následující kroky.

    ![Základní dialogové okno Konfigurace SAML](common/idp-intiated.png)

    1. Do pole **identifikátor** zadejte adresu URL v tomto vzoru:
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. Do pole **Adresa URL odpovědi** zadejte adresu URL v tomto vzoru:

       `https://saml.proxyclick.com/consume/<companyId>`

5. Chcete-li nakonfigurovat aplikaci v režimu iniciované SP, vyberte možnost **nastavit další adresy URL**. Do pole **přihlašovací adresa URL** zadejte adresu URL v tomto vzoru:
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Informace o jednotném přihlašování v doméně Proxyclick a adresách URL](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Tyto hodnoty jsou zástupné symboly. Musíte použít skutečný identifikátor, adresu URL odpovědi a přihlašovací adresu URL. Postup získání těchto hodnot je popsán dále v tomto kurzu.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** vyberte odkaz pro **stažení** vedle **certifikátu (Base64)** podle vašich požadavků a uložte certifikát do počítače:

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavení Proxyclick** zkopírujte podle vašich požadavků příslušné adresy URL:

    ![Kopírovat konfigurační adresy URL](common/copy-configuration-urls.png)

    1. **Přihlašovací adresa URL**

    1. **Identifikátor Azure AD**.

    1. **Odhlašovací adresa URL**

### <a name="configure-proxyclick-single-sign-on"></a>Konfigurace jednotného přihlašování Proxyclick

1. V novém okně webového prohlížeče se přihlaste k webu Proxyclick společnosti jako správce.

2. Vyberte **& nastavení účtu**:

    ![Výběr nastavení & účtu](./media/proxyclick-tutorial/configure1.png)

3. Přejděte dolů do části **integrace** a vyberte **SAML**:

    ![Vybrat SAML](./media/proxyclick-tutorial/configure2.png)

4. V části **SAML** proveďte následující kroky.

    ![Oddíl SAML](./media/proxyclick-tutorial/configure3.png)

    1. Zkopírujte hodnotu **URL příjemce SAML** a vložte ji do pole **Adresa URL odpovědi** v DIALOGOVÉM okně **základní konfigurace SAML** v Azure Portal.

    1. Zkopírujte hodnotu **adresy URL přesměrování jednotného přihlašování SAML** a vložte ji do polí **Signing URL** a **Identifier** v dialogovém okně **základní konfigurace SAML** v Azure Portal.

    1. V seznamu **Metoda žádosti SAML** vyberte **Přesměrování protokolu HTTP**.

    1. Do pole **vystavitele** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    1. Do pole **Adresa URL koncového bodu SAML 2,0** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    1. V programu Poznámkový blok otevřete soubor certifikátu, který jste stáhli z Azure Portal. Do pole **certifikát** vložte obsah tohoto souboru.

    1. Vyberte **Uložit změny**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon v Azure Portal.

1. V Azure Portal v levém podokně vyberte **Azure Active Directory** , vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**:

    ![Vyberte Všichni uživatelé.](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** :

    ![Vybrat nového uživatele](common/new-user.png)

3. V dialogovém okně **uživatel** proveďte následující kroky.

    ![Uživatel – dialogové okno](common/user-properties.png)

    1. Do pole **název** zadejte **BrittaSimon**.
  
    1. Do pole **uživatelské jméno** zadejte **BrittaSimon@ \<yourcompanydomain> . \<extension> **. (Například BrittaSimon@contoso.com .)

    1. Vyberte možnost **Zobrazit heslo**a pak zapište hodnotu, která je uvedena v poli **heslo** .

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Proxyclick.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **Proxyclick**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte možnost **Proxyclick**.

    ![Seznam aplikací](common/all-applications.png)

3. V levém podokně vyberte **Uživatelé a skupiny**:

    ![Vyberte Uživatelé a skupiny.](common/users-groups-blade.png)

4. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu Uživatelé položku **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části okna.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Klikněte na tlačítko **Vybrat** v dolní části okna.

7. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-proxyclick-test-user"></a>Vytvořit testovacího uživatele v Proxyclick

Pokud chcete uživatelům Azure AD povolit, aby se přihlásili k Proxyclick, musíte je přidat do Proxyclick. Musíte je přidat ručně.

Chcete-li vytvořit uživatelský účet, proveďte následující kroky:

1. Přihlaste se k webu Proxyclick společnosti jako správce.

1. V horní části okna vyberte **kolegové** :

    ![Výběr kolegů](./media/proxyclick-tutorial/user1.png)

1. Vyberte **Přidat kolegu**:

    ![Vyberte Přidat kolegu.](./media/proxyclick-tutorial/user2.png)

1. V části **Přidat kolegy** proveďte následující kroky.

    ![Přidat část kolegu](./media/proxyclick-tutorial/user3.png)

    1. Do pole **e-mail** zadejte e-mailovou adresu uživatele. V tomto případě **brittasimon \@ contoso.com**.

    1. **Do pole jméno** zadejte jméno uživatele. V tomto případě **Britta**.

    1. Do pole **příjmení** zadejte příjmení uživatele. V tomto případě **Simon**.

    1. Vyberte **Přidat uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Teď je potřeba otestovat konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu vyberete dlaždici Proxyclick, měli byste se automaticky přihlásit k instanci Proxyclick, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu věnovaném [přístupu a používání aplikací na portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Kurzy integrace aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)