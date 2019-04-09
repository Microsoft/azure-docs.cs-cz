---
title: 'Kurz: Integrace Azure Active Directory s InsideView | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a InsideView.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: 91e1780462021430913a1354b4add7c5f047ec15
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263175"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Kurz: Integrace Azure Active Directory s InsideView

V tomto kurzu se dozvíte, jak integrovat InsideView s Azure Active Directory (Azure AD).
InsideView integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k InsideView.
* Můžete povolit uživatelům být automaticky přihlášeni k InsideView (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s InsideView, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* InsideView jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje InsideView **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-insideview-from-the-gallery"></a>Přidání InsideView z Galerie

Konfigurace integrace InsideView do služby Azure AD, budete muset přidat InsideView z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat InsideView z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **InsideView**vyberte **InsideView** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![InsideView v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí InsideView podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v InsideView.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s InsideView, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace InsideView Single Sign-On](#configure-insideview-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele InsideView](#create-insideview-test-user)**  – Pokud chcete mít protějšek Britta Simon InsideView, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s InsideView, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **InsideView** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![InsideView domény a adresy URL jednotného přihlašování – informace](common/idp-reply.png)

    V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Hodnota není skutečný. Skutečná adresa URL odpovědi zaktualizujte příslušnou hodnotu. Kontakt [tým podpory InsideView klienta](mailto:support@insideview.com) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Raw)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificateraw.png)

6. Na **nastavení InsideView** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-insideview-single-sign-on"></a>Konfigurace InsideView jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti InsideView jako správce.

1. Na panelu nástrojů v horní části klikněte na tlačítko **správce**, **SingleSignOn nastavení**a potom klikněte na tlačítko **přidat SAML**.
   
   ![Jednotné přihlašování SAML pro nastavení](./media/insideview-tutorial/ic794135.png "SAML jednotného přihlašování nastavení")

1. V **přidat nové SAML** části, proveďte následující kroky:

    ![Přidat nový SAML](./media/insideview-tutorial/ic794136.png "přidat nové SAML")

    a. V **název služby tokenů zabezpečení** textového pole zadejte název pro vaši konfiguraci.

    b. V **nevyžádané koncový bod SamlP/WS-Fed** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    c. Otevřete váš kódovaného certifikátu Base64, který jste si stáhli z webu Azure portal, zkopírujte obsah ho do schránky a vložte ho do **certifikátu STS** textového pole.

    d. V **mapování Id uživatele Crm** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. V **Crm e-mailu mapování** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. V **Crm křestní jméno mapování** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    g. V **Crm lastName mapování** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.  

    h. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole `brittasimon@yourcompanydomain.extension`. Například, BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k InsideView použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **InsideView**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **InsideView**.

    ![Odkaz InsideView v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-insideview-test-user"></a>Vytvoření InsideView testovacího uživatele

Povolení služby Azure AD uživatelům umožní přihlásit k InsideView, musí být zřízená v k InsideView. V případě InsideView zřizování se ruční úlohy.

Obraťte se na uživatele nebo kontaktů vytvořených v InsideView získáte [tým podpory InsideView](mailto:support@insideview.com).

> [!NOTE]
> Můžete použít jakékoli jiné InsideView uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných InsideView zřízení uživatelských účtů služby Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici InsideView na přístupovém panelu, můžete by měl být automaticky přihlášeni k InsideView, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
