---
title: 'Kurz: Azure Active Directory integrace s InsideView | Microsoft Docs'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a InsideView.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: b8f12b6d55cb11c0cfeda5331072127ea4a696bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460100"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Kurz: Azure Active Directory integrace s InsideView

V tomto kurzu se dozvíte, jak integrovat InsideView s Azure Active Directory (Azure AD).
Tato integrace poskytuje tyto výhody:

* Pomocí Azure AD můžete řídit, kdo má přístup k InsideView.
* Uživatelům můžete povolit, aby se automaticky přihlásili k InsideView (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat integraci Azure AD s InsideView, musíte mít:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné InsideView s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* InsideView podporuje jednotné přihlašování iniciované IdP.

## <a name="add-insideview-from-the-gallery"></a>Přidání InsideView z Galerie

K nastavení integrace InsideView do služby Azure AD je nutné přidat InsideView z Galerie do seznamu spravovaných aplikací SaaS.

1. V [Azure Portal](https://portal.azure.com)v levém podokně vyberte **Azure Active Directory**:

    ![Vyberte Azure Active Directory.](common/select-azuread.png)

2. Přejít k **podnikovým aplikacím**  >  **všechny aplikace**:

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat aplikaci, vyberte v horní části okna možnost **Nová aplikace** :

    ![Vybrat novou aplikaci](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **InsideView**. Ve výsledcích hledání vyberte **InsideView** a pak vyberte **Přidat**.

    ![Výsledky hledání](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí InsideView pomocí testovacího uživatele s názvem Britta Simon.
Pokud chcete povolit jednotné přihlašování, musíte vytvořit relaci mezi uživatelem služby Azure AD a odpovídajícím uživatelem v InsideView.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí InsideView, musíte provést tyto kroky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** , abyste funkci povolili uživatelům.
2. **[Konfigurace jednotného přihlašování InsideView](#configure-insideview-single-sign-on)** na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** povolení jednotného přihlašování Azure AD pro uživatele.
5. **[Vytvořte testovacího uživatele InsideView](#create-an-insideview-test-user)** , který je propojený s Předprezentací Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí InsideView, proveďte tyto kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací InsideView vyberte **jednotné přihlašování**:

    ![Vyberte jednotné přihlašování.](common/select-sso.png)

2. V dialogovém okně **Vybrat metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** pro povolení jednotného přihlašování:

    ![Vyberte metodu jednotného přihlašování.](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** vyberte ikonu **Upravit** a otevřete DIALOGOVÉ okno **základní konfigurace SAML** :

    ![Ikona úprav](common/edit-urls.png)

4. V dialogovém okně **základní konfigurace SAML** proveďte následující kroky.

    ![Základní dialogové okno Konfigurace SAML](common/idp-reply.png)

    Do pole **Adresa URL odpovědi** zadejte adresu URL v tomto vzoru:

    `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Tato hodnota je zástupný symbol. Je nutné použít skutečnou adresu URL odpovědi. Pokud chcete získat hodnotu, obraťte se na [tým podpory InsideView](mailto:support@insideview.com) . Můžete se také podívat na vzory zobrazené v dialogovém okně **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** vyberte odkaz pro **stažení** vedle **certifikátu (RAW)** podle vašich požadavků a uložte certifikát do počítače:

    ![Odkaz na stažení certifikátu](common/certificateraw.png)

6. V části **Nastavení InsideView** zkopírujte podle vašich požadavků příslušné adresy URL:

    ![Kopírovat konfigurační adresy URL](common/copy-configuration-urls.png)

    1. **Přihlašovací adresa URL**

    1. **Identifikátor Azure AD**.

    1. **Odhlašovací adresa URL**

### <a name="configure-insideview-single-sign-on"></a>Konfigurace jednotného přihlašování InsideView

1. V novém okně webového prohlížeče se přihlaste k webu InsideView společnosti jako správce.

1. V horní části okna vyberte **správce**, **Nastavení SingleSignon** a pak **přidejte SAML**.
   
   ![Nastavení jednotného přihlašování SAML](./media/insideview-tutorial/ic794135.png "Nastavení jednotného přihlašování SAML")

1. V části **Přidat nový SAML** proveďte následující kroky.

    ![Přidat nový oddíl SAML](./media/insideview-tutorial/ic794136.png "Přidat nový oddíl SAML")

    1. Do pole **název služby STS** zadejte název konfigurace.

    1. Do pole **SamlP/WS-nakrmený nevyžádaný koncový bod** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    1. Otevřete nezpracovaný certifikát, který jste stáhli z Azure Portal. Zkopírujte obsah certifikátu do schránky a pak obsah vložte do pole **certifikát STS** .

    1. Do pole **mapování ID uživatele CRM** zadejte **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** .

    1. V poli **mapování e-mailů CRM** zadejte **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** .

    1. Do pole **mapování křestní jméno CRM** zadejte **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`** .

    1. Do pole **mapování příjmení aplikace CRM** zadejte **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`** .  

    1. Vyberte **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon v Azure Portal.

1. V Azure Portal v levém podokně vyberte **Azure Active Directory** , vyberte **Uživatelé** a pak vyberte **Všichni uživatelé**:

    ![Vyberte Všichni uživatelé.](common/users.png)

2. V horní části okna vyberte **Nový uživatel** :

    ![Vybrat nového uživatele](common/new-user.png)

3. V dialogovém okně **uživatel** proveďte následující kroky.

    ![Uživatel – dialogové okno](common/user-properties.png)

    1. Do pole **název** zadejte **BrittaSimon**.
  
    1. Do pole **uživatelské jméno** zadejte **BrittaSimon@ \<yourcompanydomain> . \<extension>**. (Například BrittaSimon@contoso.com .)

    1. Vyberte možnost **Zobrazit heslo** a pak zapište hodnotu, která je uvedena v poli **heslo** .

    1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k InsideView.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace** a pak vyberte možnost **InsideView**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte možnost **InsideView**.

    ![Seznam aplikací](common/all-applications.png)

3. V levém podokně vyberte **Uživatelé a skupiny**:

    ![Vyberte Uživatelé a skupiny.](common/users-groups-blade.png)

4. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Výběr možnosti Přidat uživatele](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu Uživatelé položku **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části okna.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Klikněte na tlačítko **Vybrat** v dolní části okna.

7. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

### <a name="create-an-insideview-test-user"></a>Vytvořit testovacího uživatele v InsideView

Pokud chcete uživatelům Azure AD povolit, aby se přihlásili k InsideView, musíte je přidat do InsideView. Musíte je přidat ručně.

Pokud chcete vytvořit uživatele nebo kontakty v InsideView, obraťte se na [tým podpory InsideView](mailto:support@insideview.com).

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít libovolný nástroj pro vytváření uživatelských účtů nebo rozhraní API, které poskytuje InsideView.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Teď je potřeba otestovat konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu vyberete dlaždici InsideView, měli byste se automaticky přihlásit k instanci InsideView, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu věnovaném [přístupu a používání aplikací na portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Kurzy integrace aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)