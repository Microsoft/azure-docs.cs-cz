---
title: 'Kurz: Azure Active Directory integrace s ITRP | Microsoft Docs'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ITRP.
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
ms.openlocfilehash: a49edf738586dce190fa90f7349f2c9b01e2050d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91850366"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Kurz: Azure Active Directory integrace s ITRP

V tomto kurzu se dozvíte, jak integrovat ITRP s Azure Active Directory (Azure AD).
Tato integrace poskytuje tyto výhody:

* Pomocí Azure AD můžete řídit, kdo má přístup k ITRP.
* Uživatelům můžete povolit, aby se automaticky přihlásili k ITRP (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Pokud chcete nakonfigurovat integraci Azure AD s ITRP, musíte mít:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné ITRP s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* ITRP podporuje jednotné přihlašování iniciované v SP.

## <a name="add-itrp-from-the-gallery"></a>Přidání ITRP z Galerie

K nastavení integrace ITRP do služby Azure AD je nutné přidat ITRP z Galerie do seznamu spravovaných aplikací SaaS.

1. V [Azure Portal](https://portal.azure.com)v levém podokně vyberte **Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejít k **podnikovým aplikacím**  >  **všechny aplikace**:

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte v horní části okna možnost **Nová aplikace** :

    ![Vybrat novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ITRP**. Ve výsledcích hledání vyberte **ITRP** a pak vyberte **Přidat**.

     ![Výsledky hledání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí ITRP pomocí testovacího uživatele s názvem Britta Simon.
Pokud chcete povolit jednotné přihlašování, musíte vytvořit relaci mezi uživatelem služby Azure AD a odpovídajícím uživatelem v ITRP.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ITRP, musíte provést tyto kroky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** , abyste funkci povolili uživatelům.
2. **[Konfigurace jednotného přihlašování ITRP](#configure-itrp-single-sign-on)** na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** povolení jednotného přihlašování Azure AD pro uživatele.
5. **[Vytvořte testovacího uživatele ITRP](#create-an-itrp-test-user)** , který je propojený s Předprezentací Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí ITRP, proveďte tyto kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací ITRP vyberte **jednotné přihlašování**:

    ![Vyberte jednotné přihlašování.](common/select-sso.png)

2. V dialogovém okně **Vybrat metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** pro povolení jednotného přihlašování:

    ![Vyberte metodu jednotného přihlašování.](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** vyberte ikonu **Upravit** a otevřete DIALOGOVÉ okno **základní konfigurace SAML** :

    ![Snímek obrazovky s vybranou ikonou pro úpravu zobrazí stránku nastavit jednu Sign-On se stránkou SAML.](common/edit-urls.png)

4. V dialogovém okně **základní konfigurace SAML** proveďte následující kroky.

    ![Základní dialogové okno Konfigurace SAML](common/sp-identifier.png)

    1. Do pole **přihlašovací adresa URL** zadejte adresu URL v tomto vzoru:
    
       `https://<tenant-name>.itrp.com`

    1. Do pole **identifikátor (ID entity)** zadejte adresu URL v tomto vzoru:

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > Tyto hodnoty jsou zástupné symboly. Musíte použít vlastní přihlašovací adresu URL a identifikátor. Pokud chcete získat hodnoty, obraťte se na [tým podpory ITRP](https://www.4me.com/support/) . Můžete se také podívat na vzory zobrazené v dialogovém okně **základní konfigurace SAML** v Azure Portal.

5. V části **podpisový certifikát SAML** vyberte ikonu **Upravit** a otevřete dialogové okno **podpisový certifikát SAML** :

    ![Snímek obrazovky se stránkou podpisového certifikátu SAML se zvolenou ikonou pro úpravy.](common/edit-certificate.png)

6. V dialogovém okně **podpisový certifikát SAML** Zkopírujte hodnotu **kryptografického otisku** a uložte ji:

    ![Kopírovat hodnotu kryptografického otisku](common/copy-thumbprint.png)

7. V části **Nastavení ITRP** zkopírujte podle vašich požadavků příslušné adresy URL:

    ![Kopírovat konfigurační adresy URL](common/copy-configuration-urls.png)

    1. **Přihlašovací adresa URL**

    1. **Identifikátor Azure AD**.

    1. **Odhlašovací adresa URL**

### <a name="configure-itrp-single-sign-on"></a>Konfigurace jednotného přihlašování ITRP

1. V novém okně webového prohlížeče se přihlaste k webu ITRP společnosti jako správce.

1. V horní části okna vyberte ikonu **Nastavení** :

    ![Ikona nastavení](./media/itrp-tutorial/ic775570.png "Ikona nastavení")

1. V levém podokně vyberte **jednotné přihlašování**:

    ![Vyberte jednotné přihlašování.](./media/itrp-tutorial/ic775571.png "Vyberte jeden Sign-On")

1. V části konfigurace **jednotného přihlašování** proveďte následující kroky.

    ![Snímek obrazovky ukazuje jednu Sign-On oddíl s povoleným vybraným.](./media/itrp-tutorial/ic775572.png "Oddíl s jednou Sign-On")

    ![Snímek obrazovky zobrazuje jednu část Sign-On, do které můžete přidat informace popsané v tomto kroku.](./media/itrp-tutorial/ic775573.png "Oddíl s jednou Sign-On")

    1. Vyberte **Povoleno**.

    1. Do pole **Adresa URL pro vzdálené** odhlašování vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    1. Do pole **Adresa URL jednotného přihlašování SAML** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    1. Do pole **otisk certifikátu** vložte hodnotu **kryptografického otisku** certifikátu, který jste zkopírovali z Azure Portal.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k ITRP.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **ITRP**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte možnost **ITRP**.

    ![Seznam aplikací](common/all-applications.png)

3. V levém podokně vyberte **Uživatelé a skupiny**:

    ![Vyberte Uživatelé a skupiny.](common/users-groups-blade.png)

4. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu Uživatelé položku **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části okna.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Klikněte na tlačítko **Vybrat** v dolní části okna.

7. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-an-itrp-test-user"></a>Vytvořit testovacího uživatele v ITRP

Pokud chcete uživatelům Azure AD povolit, aby se přihlásili k ITRP, musíte je přidat do ITRP. Musíte je přidat ručně.

Chcete-li vytvořit uživatelský účet, proveďte následující kroky:

1. Přihlaste se ke svému tenantovi ITRP.

1. V horní části okna vyberte ikonu **záznamy** :

    ![Ikona záznamů](./media/itrp-tutorial/ic775575.png "Ikona záznamů")

1. V nabídce vyberte **osoby**:

    ![Vybrat lidi](./media/itrp-tutorial/ic775587.png "Vybrat lidi")

1. Vyberte znaménko plus ( **+** ) a přidejte novou osobu:

    ![Vyberte znaménko plus.](./media/itrp-tutorial/ic775576.png "Vyberte znaménko plus.")

1. V dialogovém okně **Přidat novou osobu** proveďte následující kroky.

    ![Dialogové okno Přidat novou osobu](./media/itrp-tutorial/ic775577.png "Dialogové okno Přidat novou osobu")

    1. Zadejte jméno a e-mailovou adresu platného účtu Azure AD, který chcete přidat.

    1. Vyberte **Uložit**.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít libovolný nástroj pro vytváření uživatelských účtů nebo rozhraní API, které poskytuje ITRP.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Teď je potřeba otestovat konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu vyberete dlaždici ITRP, měli byste se automaticky přihlásit k instanci ITRP, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu věnovaném [přístupu a používání aplikací na portálu moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Kurzy integrace aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
