---
title: 'Kurz: Azure Active Directory integrace s percolate | Microsoft Docs'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a percolate.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 913ffc0670e40e749bd28382d492a16891fdc5c9
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522253"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Kurz: Azure Active Directory integrace s percolate

V tomto kurzu se dozvíte, jak integrovat percolate s Azure Active Directory (Azure AD).

Tato integrace poskytuje tyto výhody:

* Pomocí Azure AD můžete řídit, kdo má přístup k percolate.
* Uživatelům můžete povolit, aby se automaticky přihlásili k percolate (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

Pokud chcete nakonfigurovat integraci Azure AD s percolate, musíte mít:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné percolate s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Percolate podporuje jednotné přihlašování iniciované v SP a IdP.

## <a name="add-percolate-from-the-gallery"></a>Přidání percolate z Galerie

Pokud chcete nakonfigurovat integraci percolate do služby Azure AD, musíte přidat percolate z Galerie do svého seznamu spravovaných aplikací SaaS.

1. V [Azure Portal](https://portal.azure.com)v levém podokně vyberte **Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejít k **podnikovým aplikacím**  >  **všechny aplikace**:

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte v horní části okna možnost **Nová aplikace** :

    ![Vybrat novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **percolate**. Ve výsledcích hledání vyberte **percolate** a pak vyberte **Přidat**.

     ![Výsledky hledání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí percolate pomocí testovacího uživatele s názvem Britta Simon.
Pokud chcete povolit jednotné přihlašování, musíte vytvořit relaci mezi uživatelem služby Azure AD a odpovídajícím uživatelem v percolate.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí percolate, musíte provést tyto kroky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** , abyste funkci povolili uživatelům.
2. **[Konfigurace jednotného přihlašování percolate](#configure-percolate-single-sign-on)** na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** povolení jednotného přihlašování Azure AD pro uživatele.
5. **[Vytvořte testovacího uživatele percolate](#create-a-percolate-test-user)** , který je propojený s Předprezentací Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí percolate, proveďte tyto kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **percolate** vyberte **jednotné přihlašování**:

    ![Vyberte jednotné přihlašování.](common/select-sso.png)

2. V dialogovém okně **Vybrat metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** pro povolení jednotného přihlašování:

    ![Vyberte metodu jednotného přihlašování.](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** vyberte ikonu **Upravit** a otevřete DIALOGOVÉ okno **základní konfigurace SAML** :

    ![Ikona úprav](common/edit-urls.png)

4. V dialogovém okně **základní konfigurace SAML** není nutné provádět žádnou akci pro konfiguraci aplikace v režimu iniciované IDP. Aplikace je už integrovaná s Azure.

    ![Informace o jednotném přihlašování v doméně percolate a adresách URL](common/preintegrated.png)

5. Chcete-li nakonfigurovat aplikaci v režimu iniciované SP, vyberte možnost **nastavit další adresy URL** a v poli **Adresa URL pro přihlášení** zadejte **https://percolate.com/app/login** :

   ![Snímek obrazovky se zvýrazněnou možností nastavit další U R LS pomocí zvýrazněného textového pole "přihlašovat U R L".](common/metadata-upload-additional-signon.png)
6. Na stránce **nastavit jednu Sign-On s SAML** v části **podpisový certifikát SAML** vyberte ikonu **kopírování** a zkopírujte **adresu URL federačních metadat aplikace**. Uložte tuto adresu URL.

    ![Kopírovat adresu URL federačních metadat aplikace](common/copy-metadataurl.png)

7. V části **Nastavení percolate** zkopírujte podle vašich požadavků příslušné adresy URL.

    ![Kopírovat konfigurační adresy URL](common/copy-configuration-urls.png)

    1. **Přihlašovací adresa URL**

    1. **Identifikátor Azure AD**.

    1. **Odhlašovací adresa URL**

### <a name="configure-percolate-single-sign-on"></a>Konfigurace jednotného přihlašování percolate

1. V novém okně webového prohlížeče se přihlaste k percolate jako správce.

2. Na levé straně domovské stránky vyberte **Nastavení**:
    
    ![Nastavení](./media/percolate-tutorial/configure01.png)

3. V levém podokně vyberte **SSO** v části **organizace**:

    ![Výběr jednotného přihlašování v organizaci](./media/percolate-tutorial/configure02.png)

    1. Do pole **Adresa URL pro přihlášení** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    1. Do pole **ID entity** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    1. V programu Poznámkový blok otevřete certifikát s kódováním Base-64, který jste stáhli z Azure Portal. Zkopírujte jeho obsah a vložte ho do pole **certifikáty x509** .

    1. Do pole **atribut e-mail** zadejte **EmailAddress**.

    1. Pole **Adresa URL metadat zprostředkovatele identity** je volitelné pole. Pokud jste zkopírovali **adresu URL federačních metadat aplikace** z Azure Portal, můžete ji vložit do tohoto pole.

    1. V seznamu **by měl být podepsaný AuthNRequests?** vyberte **ne**.

    1. V seznamu **Povolit automatické zřizování jednotného přihlašování** vyberte **ne**.

    1. Vyberte **Uložit**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí služby Azure AD tím, že udělíte přístup k percolate.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **percolate**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte možnost **percolate**.

    ![Seznam aplikací](common/all-applications.png)

3. V levém podokně vyberte **Uživatelé a skupiny**:

    ![Snímek obrazovky se zvolenými možnostmi uživatelé a skupiny v levém podokně.](common/users-groups-blade.png)

4. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Vyberte Uživatelé a skupiny.](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu Uživatelé položku **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-percolate-test-user"></a>Vytvořit testovacího uživatele v percolate

Pokud chcete uživatelům Azure AD povolit, aby se přihlásili k percolate, musíte je přidat do percolate. Musíte je přidat ručně.

Chcete-li vytvořit uživatelský účet, proveďte následující kroky:

1. Přihlaste se k percolate jako správce.

2. V levém podokně vyberte **uživatele** v části **organizace**. Vyberte **nové uživatele**:

    ![Vybrat nové uživatele](./media/percolate-tutorial/configure03.png)

3. Na stránce **vytvořit uživatele** proveďte následující kroky.

    ![Stránka pro vytvoření uživatelů](./media/percolate-tutorial/configure04.png)

    1. Do pole **e-mail** zadejte e-mailovou adresu uživatele. Například, brittasimon@contoso.com.

    1. Do pole **jméno a příjmení zadejte** jméno uživatele. Například **Brittasimon**.

    1. Vyberte **vytvořit uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Teď je potřeba otestovat konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu vyberete dlaždici percolate, měli byste se automaticky přihlásit k instanci percolate, pro kterou jste nastavili jednotné přihlašování. Další informace najdete v tématu věnovaném [přístupu a používání aplikací na portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Kurzy integrace aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)