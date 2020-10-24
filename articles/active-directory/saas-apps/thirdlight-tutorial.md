---
title: 'Kurz: Azure Active Directory integrace s ThirdLight | Microsoft Docs'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ThirdLight.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: f15d00f1050177c6255fb5528f03314153c3ed9a
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514655"
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Kurz: Azure Active Directory integrace s ThirdLight

V tomto kurzu se dozvíte, jak integrovat ThirdLight s Azure Active Directory (Azure AD). Tato integrace poskytuje tyto výhody:

* Pomocí Azure AD můžete řídit, kdo má přístup k ThirdLight.
* Uživatelům můžete povolit, aby se automaticky přihlásili k ThirdLight (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění: Azure Portal.

Pokud se chcete dozvědět víc o integraci aplikací SaaS s Azure AD, přečtěte si téma [jednotné přihlašování k aplikacím v Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Pokud chcete nakonfigurovat integraci Azure AD s ThirdLight, musíte mít:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné ThirdLight s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* ThirdLight podporuje jednotné přihlašování iniciované v SP.

## <a name="add-thirdlight-from-the-gallery"></a>Přidání ThirdLight z Galerie

K nastavení integrace ThirdLight do služby Azure AD je nutné přidat ThirdLight z Galerie do seznamu spravovaných aplikací SaaS.

1. V [Azure Portal](https://portal.azure.com)v levém podokně vyberte **Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejít k **podnikovým aplikacím**  >  **všechny aplikace**:

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte v horní části okna možnost **Nová aplikace** :

    ![Vybrat novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ThirdLight**. Ve výsledcích hledání vyberte **ThirdLight** a pak vyberte **Přidat**.

     ![Výsledky hledání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí ThirdLight pomocí testovacího uživatele s názvem Britta Simon.
Pokud chcete povolit jednotné přihlašování, musíte vytvořit relaci mezi uživatelem služby Azure AD a odpovídajícím uživatelem v ThirdLight.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ThirdLight, musíte provést tyto kroky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** , abyste funkci povolili uživatelům.
2. **[Konfigurace jednotného přihlašování ThirdLight](#configure-thirdlight-single-sign-on)** na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** povolení jednotného přihlašování Azure AD pro uživatele.
5. **[Vytvořte testovacího uživatele ThirdLight](#create-a-thirdlight-test-user)** , který je propojený s Předprezentací Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí ThirdLight, proveďte tyto kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací ThirdLight vyberte **jednotné přihlašování**:

    ![Vyberte jednotné přihlašování.](common/select-sso.png)

2. V dialogovém okně **Vybrat metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** pro povolení jednotného přihlašování:

    ![Vyberte metodu jednotného přihlašování.](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** vyberte ikonu **Upravit** a otevřete DIALOGOVÉ okno **základní konfigurace SAML** :

    ![Ikona úprav](common/edit-urls.png)

4. V dialogovém okně **základní konfigurace SAML** proveďte následující kroky.

    ![Základní dialogové okno Konfigurace SAML](common/sp-identifier.png)

    1. Do pole **přihlašovací adresa URL** zadejte adresu URL v tomto vzoru:
    
          `https://<subdomain>.thirdlight.com/`

    1. Do pole **identifikátor (ID entity)** zadejte adresu URL v tomto vzoru:

       `https://<subdomain>.thirdlight.com/saml/sp`

       > [!NOTE]
       > Tyto hodnoty jsou zástupné symboly. Musíte použít vlastní přihlašovací adresu URL a identifikátor. Pokud chcete získat hodnoty, obraťte se na [tým podpory ThirdLight](https://www.thirdlight.com/support) . Můžete se také podívat na vzory zobrazené v dialogovém okně **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** vyberte odkaz **ke stažení** vedle **metadat federace XML**podle vašich požadavků a uložte soubor do počítače:

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení ThirdLight** zkopírujte podle vašich požadavků příslušné adresy URL:

    ![Kopírovat konfigurační adresy URL](common/copy-configuration-urls.png)

    1. **Přihlašovací adresa URL**

    1. **Identifikátor Azure AD**.

    1. **Odhlašovací adresa URL**

### <a name="configure-thirdlight-single-sign-on"></a>Konfigurace jednotného přihlašování ThirdLight

1. V novém okně webového prohlížeče se přihlaste k webu ThirdLight společnosti jako správce.

1. Přejít do **Configuration**  >  **správy konfiguračního systému**  >  **typu Saml2**:

    ![Správa systémů](./media/thirdlight-tutorial/ic805843.png "Správa systémů")

1. V části Konfigurace typu Saml2 proveďte následující kroky.
  
    ![Konfigurační oddíl typu Saml2](./media/thirdlight-tutorial/ic805844.png "Konfigurační oddíl typu Saml2")

    1. Vyberte **Povolit jednotné přihlašování typu Saml2**.

    1. V části **zdroj pro metadata IDP**vyberte **načíst IDP metadata z XML**.

    1. Otevřete soubor metadat, který jste stáhli z Azure Portal v předchozí části. Zkopírujte obsah souboru a vložte ho do pole **XML metadat IDP** .

    1. Vyberte **Uložit nastavení typu Saml2**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon v Azure Portal.

1. V Azure Portal v levém podokně vyberte **Azure Active Directory** , vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**:

    ![Vyberte Všichni uživatelé.](common/users.png)

2. V horní části okna vyberte **Nový uživatel** :

    ![Vybrat nového uživatele](common/new-user.png)

3. V dialogovém okně **uživatel** proveďte následující kroky.

    ![Uživatel – dialogové okno](common/user-properties.png)

    1. Do pole **název** zadejte **BrittaSimon**.
  
    1. Do pole **uživatelské jméno** zadejte **BrittaSimon@ \<yourcompanydomain> . \<extension> **. (Například BrittaSimon@contoso.com .)

    1. Vyberte možnost **Zobrazit heslo**a pak zapište hodnotu, která je uvedena v poli **heslo** .

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k ThirdLight.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **ThirdLight**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte možnost **ThirdLight**.

    ![Seznam aplikací](common/all-applications.png)

3. V levém podokně vyberte **Uživatelé a skupiny**:

    ![Vyberte Uživatelé a skupiny.](common/users-groups-blade.png)

4. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu Uživatelé položku **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části okna.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Klikněte na tlačítko **Vybrat** v dolní části okna.

7. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-thirdlight-test-user"></a>Vytvořit testovacího uživatele v ThirdLight

Pokud chcete uživatelům Azure AD povolit, aby se přihlásili k ThirdLight, musíte je přidat do ThirdLight. Musíte je přidat ručně.

Chcete-li vytvořit uživatelský účet, proveďte následující kroky:

1. Přihlaste se k webu ThirdLight společnosti jako správce.

1. Přejít na kartu **Uživatelé** .

1. Vyberte **Uživatelé a skupiny**.

1. Vyberte **Přidat nového uživatele**.

1. Zadejte uživatelské jméno, název nebo popis a e-mailovou adresu platného účtu Azure AD, který chcete zřídit. Vyberte předvolbu nebo skupinu nových členů.

1. Vyberte **Vytvořit**.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít libovolný nástroj pro vytváření uživatelských účtů nebo rozhraní API, které poskytuje ThirdLight.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Teď je potřeba otestovat konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu vyberete dlaždici ThirdLight, měli byste se automaticky přihlásit k instanci ThirdLight, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu věnovaném [přístupu a používání aplikací na portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Kurzy integrace aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)