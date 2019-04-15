---
title: 'Kurz: Integrace Azure Active Directory s stránka se stavem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a stránka se stavem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 49f77da5843bc2438ea7f82475b4faf753b66f09
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565187"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Kurz: Integrace Azure Active Directory s stránka se stavem

V tomto kurzu se dozvíte, jak integrovat stránka se stavem služby Azure Active Directory (Azure AD).
Stránka se stavem integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k stránka se stavem.
* Můžete povolit uživatelům být automaticky přihlášeni k stránka se stavem (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s stránka se stavem, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Stránka se stavem jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Stránka se stavem podporuje **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-statuspage-from-the-gallery"></a>Přidání stránka se stavem z Galerie

Konfigurace integrace stránka se stavem do služby Azure AD, budete muset přidat stránka se stavem v galerii na váš seznam spravovaných aplikací SaaS.

**Stránka se stavem přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **stránka se stavem**vyberte **stránka se stavem** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Stránka se stavem v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování služby Azure AD jednotné přihlašování s stránka se stavem podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v stránka se stavem.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s stránka se stavem, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace stránka se stavem Single Sign-On](#configure-statuspage-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele stránka se stavem](#create-statuspage-test-user)**  – Pokud chcete mít protějšek Britta Simon stránka se stavem, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s stránka se stavem, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **stránka se stavem** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránce, proveďte následující kroky:

    ![Stránka se stavem domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce:
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/`|
    | `https://<subdomain>.statuspage.io/`|

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce:
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume`|
    | `https://<subdomain>.statuspage.io/sso/saml/consume`|

    > [!NOTE]
    > Obraťte se na tým podpory stránka se stavem v [ SupportTeam@statuspage.io ](mailto:SupportTeam@statuspage.io)žádat o metadata potřebná ke konfiguraci jednotného přihlašování. 
    >
    > a. Z metadat, zkopírujte hodnotu Issuer a vložte jej do **identifikátor** textového pole.
    >
    > b. Z metadat, zkopírujte adresu URL odpovědi a vložte jej do **adresy URL odpovědi** textového pole.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavit stránka se stavem** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-statuspage-single-sign-on"></a>Konfigurace přihlašování jedním stránka se stavem

1. V jiném okně prohlížeče Přihlaste se k webu společnosti stránka se stavem jako správce.

1. Na hlavním panelu nástrojů klikněte na tlačítko **spravovat účet**.

    ![Konfigurace jednotného přihlašování](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klikněte na tlačítko **Single Sign-on** kartu.

    ![Konfigurace jednotného přihlašování](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. Na stránce nastavení jednotného přihlašování proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Konfigurace jednotného přihlašování](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. V **jednotného přihlašování k cílové adrese URL** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    b. Otevřete stažený certifikát v poznámkovém bloku, zkopírujte jeho obsah a vložte jej do **certifikát** textového pole.

    c. Klikněte na tlačítko **uložit konfiguraci**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole `brittasimon@yourcompanydomain.extension`  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k stránka se stavem.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **stránka se stavem**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **stránka se stavem**.

    ![Stránka se stavem odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-statuspage-test-user"></a>Vytvořit testovacího uživatele stránka se stavem

Cílem této části je vytvořte uživatele Britta Simon v stránka se stavem.

Stránka se stavem podporuje zřizování just-in-time. Je už povolená v [konfiguraci Azure AD Single Sign-On](#configure-azure-ad-single-sign-on).

**Vytvořte uživatele v stránka se stavem jako Britta Simon, proveďte následující kroky:**

1. Přihlašování k webu společnosti stránka se stavem jako správce.

1. V nabídce v horní části klikněte na tlačítko **spravovat účet**.

    ![Konfigurace jednotného přihlašování](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klikněte na tlačítko **členové týmu** kartu.
  
    ![Vytváří se testovací uživatele služby Azure AD](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Klikněte na tlačítko **člen týmu přidat**.
  
    ![Vytváří se testovací uživatele služby Azure AD](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Typ **e-mailovou adresu**, **křestní jméno**, a **příjmení** platné uživatele, které chcete zřídit do související textových polí. 

    ![Vytváří se testovací uživatele služby Azure AD](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Jako **Role**, zvolte **správce klienta**.

1. Klikněte na tlačítko **vytvořit účet**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici stránka se stavem na přístupovém panelu, můžete by měl být automaticky přihlášeni k stránka se stavem, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
