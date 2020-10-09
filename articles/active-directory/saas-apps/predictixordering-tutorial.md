---
title: 'Kurz: Azure Active Directory integrace s řazením Predictix | Microsoft Docs'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a řazením Predictix.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 0eec8d9d88b286e386bd310c98d03737e56e05b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88553718"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-ordering"></a>Kurz: Azure Active Directory integrace s řazením Predictix

V tomto kurzu se dozvíte, jak integrovat řazení Predictix s využitím Azure Active Directory (Azure AD).
Tato integrace poskytuje tyto výhody:

* Pomocí Azure AD můžete řídit, kdo má přístup k Predictix řazení.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Predictix řazení (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat integraci Azure AD s řazením Predictix, musíte mít:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
* Předplatné řazení Predictix s povoleným jednotným přihlašováním.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Řazení Predictix podporuje jednotné přihlašování iniciované v SP.

## <a name="add-predictix-ordering-from-the-gallery"></a>Přidání řazení Predictix z Galerie

Pokud chcete nastavit integraci Predictix řazení do Azure AD, musíte přidat řazení Predictix z Galerie do seznamu spravovaných aplikací SaaS.

1. V [Azure Portal](https://portal.azure.com)v levém podokně vyberte **Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejít k **podnikovým aplikacím**  >  **všechny aplikace**:

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte v horní části okna možnost **Nová aplikace** :

    ![Vybrat novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Predictix řazení**. Ve výsledcích hledání vyberte **řazení Predictix** a pak vyberte **Přidat**.

     ![Výsledky hledání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí řazení Predictix pomocí testovacího uživatele s názvem Britta Simon.
Pokud chcete povolit jednotné přihlašování, musíte vytvořit relaci mezi uživatelem služby Azure AD a odpovídajícím uživatelem v Predictix řazení.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí řazení Predictix, musíte provést tyto kroky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** , abyste funkci povolili uživatelům.
2. **[Konfigurace jednotného přihlašování Predictix](#configure-predictix-ordering-single-sign-on)** na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** povolení jednotného přihlašování Azure AD pro uživatele.
5. **[Vytvořte Predictix testovacího uživatele](#create-a-predictix-ordering-test-user)** , který je propojený s Předprezentací Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí řazení Predictix, proveďte tyto kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikace **řazení Predictix** vyberte **jednotné přihlašování**:

    ![Vyberte jednotné přihlašování.](common/select-sso.png)

2. V dialogovém okně **Vybrat metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** pro povolení jednotného přihlašování:

    ![Vyberte metodu jednotného přihlašování.](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** vyberte ikonu **Upravit** a otevřete DIALOGOVÉ okno **základní konfigurace SAML** :

    ![Ikona úprav](common/edit-urls.png)

4. V dialogovém okně **základní konfigurace SAML** proveďte následující kroky.

    ![Základní dialogové okno Konfigurace SAML](common/sp-identifier.png)

    1. Do pole **přihlašovací adresa URL** zadejte adresu URL v tomto vzoru:

       `https://<companyname-pricing>.ordering.predictix.com/sso/request`

    1. Do pole **identifikátor (ID entity)** zadejte adresu URL v tomto vzoru:

        ```https
        https://<companyname-pricing>.dev.ordering.predictix.com
        https://<companyname-pricing>.ordering.predictix.com
        ```

    > [!NOTE]
    > Tyto hodnoty jsou zástupné symboly. Musíte použít vlastní přihlašovací adresu URL a identifikátor. Pokud chcete získat hodnoty, obraťte se na [tým podpory řazení Predictix](https://www.predix.io/support/) . Můžete se také podívat na vzory zobrazené v dialogovém okně **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** vyberte odkaz pro **stažení** vedle **certifikátu (Base64)** podle vašich požadavků a uložte certifikát do počítače:

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavení řazení Predictix** zkopírujte příslušné adresy URL na základě vašich požadavků:

    ![Kopírovat konfigurační adresy URL](common/copy-configuration-urls.png)

    1. **Přihlašovací adresa URL**

    2. **Identifikátor Azure AD**.

    3. **Odhlašovací adresa URL**

### <a name="configure-predictix-ordering-single-sign-on"></a>Konfigurace jednotného přihlašování v Predictix pořadí

Chcete-li nakonfigurovat jednotné přihlašování na straně objednávání Predictix, je třeba odeslat certifikát, který jste stáhli, a adresy URL, které jste zkopírovali z Azure Portal do [týmu podpory pro objednávání Predictix](https://www.predix.io/support/). Tento tým zajišťuje správné nastavení připojení SAML SSO na obou stranách.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí služby Azure AD tím, že udělíte přístup k řazení Predictix.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte **řazení Predictix**:

    ![Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **řazení Predictix**.

    ![Seznam aplikací](common/all-applications.png)

3. V levém podokně vyberte **Uživatelé a skupiny**:

    ![Vyberte Uživatelé a skupiny.](common/users-groups-blade.png)

4. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu Uživatelé položku **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-a-predictix-ordering-test-user"></a>Vytvořit testovacího uživatele pro řazení Predictix

Dále musíte vytvořit uživatele s názvem Britta Simon v řazení Predictix. Pokud chcete přidat uživatele, pracujte s [týmem podpory řazení Predictix](https://www.predix.io/support/) . Před použitím jednotného přihlašování je potřeba vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Teď je potřeba otestovat konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když vyberete dlaždici objednávání Predictix na přístupovém panelu, měli byste se automaticky přihlásit k instanci řazení Predictix, pro kterou jste nastavili jednotné přihlašování. Další informace najdete v tématu věnovaném [přístupu a používání aplikací na portálu moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Kurzy integrace aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
