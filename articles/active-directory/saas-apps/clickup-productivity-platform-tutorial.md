---
title: 'Kurz: Integrace Azure Active Directory s ClickUp produktivní platforma | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ClickUp produktivní platforma.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06853edd-e8da-4ad2-a4e6-5555d592cee5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: 3244140999dc61560549db077d4c402b3986956b
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65900514"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Kurz: Integrace Azure Active Directory s ClickUp produktivní platforma

V tomto kurzu se dozvíte, jak integrovat ClickUp produktivní Platforma Azure Active Directory (Azure AD).
Integrace ClickUp produktivní platforma s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k ClickUp produktivní platforma.
* Uživatelům se automaticky přihlášeni k ClickUp produktivní platforma (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ClickUp produktivní platforma, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* ClickUp produktivní platforma jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* ClickUp produktivní platforma podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-clickup-productivity-platform-from-the-gallery"></a>Přidání ClickUp produktivní platforma z Galerie

Ke konfiguraci integrace ClickUp produktivní platforma do služby Azure AD, budete muset přidat ClickUp produktivní platforma z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat ClickUp produktivní platforma z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ClickUp produktivní platforma**vyberte **ClickUp produktivní platforma** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![ClickUp produktivní platforma v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování služby Azure AD jednotné přihlašování s ClickUp produktivitu platforma založená na uživateli testu **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v ClickUp produktivní platforma.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s využitím ClickUp produktivity platformy, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace ClickUp produktivity platformy Single Sign-On](#configure-clickup-productivity-platform-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele ClickUp produktivní platforma](#create-clickup-productivity-platform-test-user)**  – Pokud chcete mít protějšek Britta Simon ClickUp produktivní platforma, která souvisí s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s ClickUp produktivní platforma, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **ClickUp produktivní platforma** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![ClickUp produktivity platformy domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL: `https://app.clickup.com/login/sso`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://api.clickup.com/v1/team/<team_id>/microsoft`

    > [!NOTE]
    > Hodnota identifikátoru není skutečný. Aktualizujte tuto hodnotu skutečné identifikátor, který je vysvětlen později v tomto kurzu.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a uložte ji na vaše počítač.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-clickup-productivity-platform-single-sign-on"></a>Konfigurace ClickUp produktivity platformy jednotného přihlašování

1. V okně prohlížeče jiných webových přihlašování k vašemu tenantovi ClickUp produktivní platforma jako správce.

2. Klikněte na **profilu uživatele** a vyberte **nastavení**.

    ![Konfigurace ClickUp produktivitu](./media/clickup-productivity-platform-tutorial/configure1.png)

3. Vyberte **Microsoft**v části jednotného přihlašování (SSO) zprostředkovatele.

    ![Konfigurace ClickUp produktivitu](./media/clickup-productivity-platform-tutorial/configure2.png)

4. Na **konfigurace Microsoft Single Sign On** stránce, proveďte následující kroky:

    ![Konfigurace ClickUp produktivitu](./media/clickup-productivity-platform-tutorial/configure3.png)

    a. Klikněte na tlačítko **kopírování** zkopírujte hodnotu Entity ID a vložte ho do **identifikátor (Entity ID)** textového pole v **základní konfiguraci SAML** části webu Azure Portal.
    
    b. V **adresa URL federačních metadat Azure** textového pole vložte hodnotu adresa Url federačních metadat aplikace, které jste zkopírovali z portálu Azure portal a potom klikněte na tlačítko **Uložit**.

5. K dokončení instalace, klikněte na tlačítko **ověření s Microsoftu a dokončete nastavení** a proveďte ověření pomocí účtu microsoft.

    ![Konfigurace ClickUp produktivitu](./media/clickup-productivity-platform-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon\@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k platformě ClickUp produktivitu.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **ClickUp produktivní platforma**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **ClickUp produktivní platforma**.

    ![Odkaz ClickUp produktivní platforma v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-clickup-productivity-platform-test-user"></a>Vytvořit testovacího uživatele ClickUp produktivní platforma

1. V okně prohlížeče jiných webových přihlašování k vašemu tenantovi ClickUp produktivní platforma jako správce.

2. Klikněte na **profilu uživatele** a vyberte **uživatelé**.

    ![Konfigurace ClickUp produktivitu](./media/clickup-productivity-platform-tutorial/user1.png)

3. Zadejte e-mailovou adresu uživatele, do textového pole a klikněte na tlačítko **pozvat**.

    ![Konfigurace ClickUp produktivitu](./media/clickup-productivity-platform-tutorial/user2.png)

    > [!NOTE]
    > Uživatel dostane oznámení a má k přijetí pozvánky k aktivaci účtu.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici ClickUp produktivní platforma, na přístupovém panelu, vám by měl být automaticky přihlášeni ke ClickUp produktivní platforma, pro kterou můžete nastavit jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

