---
title: 'Kurz: Integrace Azure Active Directory s nekonečnou Campus | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a školních nekonečné.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 91f1d7151debb1f1f3a562337d6c37e4f63a7ee7
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565287"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Kurz: Integrace Azure Active Directory s nekonečnou Campus

V tomto kurzu se dozvíte, jak integrovat nekonečné Campus s Azure Active Directory (Azure AD).
Nekonečné Campus integrace s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k nekonečné Campus.
* Uživatelům se automaticky přihlášeni k nekonečné Campus (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s nekonečnou Campus, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Nekonečné Campus jednotného přihlašování povolená předplatného
* Minimálně musíte být správce Azure Active Directory a mít roli zabezpečení produktu Campus z "Student informačního systému (SIS)" k dokončení konfigurace.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje nekonečné Campus **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-infinite-campus-from-the-gallery"></a>Přidání nekonečné Campus z Galerie

Ke konfiguraci integrace nekonečné Campus do služby Azure AD, budete muset přidat nekonečné Campus z Galerie na váš seznam spravovaných aplikací SaaS.

**Nekonečné Campus přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **nekonečné Campus**vyberte **nekonečné Campus** na panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Nekonečné Campus v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s nekonečnou Campus podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v nekonečné Campus.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s nekonečnou areálu, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace nekonečné Campus Single Sign-On](#configure-infinite-campus-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele nekonečné Campus](#create-infinite-campus-test-user)**  – Pokud chcete mít protějšek Britta Simon v nekonečné areálu, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s nekonečnou areálu, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **nekonečné Campus** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. V části základní konfiguraci SAML, proveďte následující kroky (Všimněte si, že doménu se bude lišit podle hostování modelu, ale **plně-kvalifikovaný-DOMAIN** hodnota musí odpovídat vaší instalace nekonečné Campus):

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Nekonečné Campus domény a adresy URL jednotného přihlašování – informace](common/sp-identifier-reply.png)

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a uložte ji na vaše počítač.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>Konfigurace nekonečné Campus jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k nekonečné Campus jako správce zabezpečení.

2. V levé nabídce klikněte na tlačítko **Správa systému**.

    ![Správce](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. Přejděte do **zabezpečení uživatele** > **SAML správu** > **konfigurace poskytovatele služby jednotného přihlašování**.

    ![Saml](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. Na **konfigurace poskytovatele služby jednotného přihlašování** stránce, proveďte následující kroky:

    ![Jednotné přihlašování](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Vyberte **povolit SAML jednotného přihlašování**.

    b. Upravit **volitelný název atributu** tak, aby obsahovala **název**

    c. Na **vyberte možnost načíst data serveru zprostředkovatele Identity (IDP)** vyberte **adresa URL metadat**, vložte **adresa Url federačních metadat aplikace** hodnotu, která máte zkopírovat z portálu Azure portal v poli a pak klikněte na tlačítko **synchronizace**.

    d. Po kliknutí na tlačítko **synchronizace** hodnoty získat automaticky dosadí **konfigurace poskytovatele služby jednotného přihlašování** stránky. Tyto hodnoty můžete ověřit tak, aby odpovídaly hodnoty v kroku 4 výše.

    e. Klikněte na **Uložit**.

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

> [!NOTE]
> Pokud chcete všechny Azure uživatelé budou používat jednotné přihlašování přistupovat k nekonečné Campus a Spolehněte se na nekonečnou Campus systém interní oprávnění k řízení přístupu, můžete nastavit **je vyžadováno přiřazení uživatele** vlastností aplikace a ne a následující kroky přeskočte.

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k nekonečné Campus.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **nekonečné Campus**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **nekonečné Campus**.

    ![Nekonečné Campus odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-infinite-campus-test-user"></a>Vytvoření nekonečné Campus testovacího uživatele

Nekonečné Campus má demografické údaje na architekturu. Obraťte se prosím na [tým podpory nekonečné Campus](mailto:sales@infinitecampus.com) přidat uživatele na platformě nekonečné Campus.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici nekonečné Campus na přístupovém panelu, vám by měl být automaticky přihlášeni k nekonečné areálu, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
