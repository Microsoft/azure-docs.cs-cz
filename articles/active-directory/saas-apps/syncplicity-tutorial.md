---
title: 'Kurz: Integrace Azure Active Directory s Syncplicity | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 11ac9d859ec76c782f762dfc6f424d70ea487665
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58360224"
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Kurz: Integrace Azure Active Directory s Syncplicity

V tomto kurzu se dozvíte, jak integrovat Syncplicity s Azure Active Directory (Azure AD).
Syncplicity integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Syncplicity.
* Můžete povolit uživatelům být automaticky přihlášeni k Syncplicity (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Syncplicity, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Syncplicity jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Syncplicity **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-syncplicity-from-the-gallery"></a>Přidání Syncplicity z Galerie

Konfigurace integrace Syncplicity do služby Azure AD, budete muset přidat Syncplicity z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Syncplicity z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Syncplicity**vyberte **Syncplicity** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Syncplicity v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Syncplicity podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Syncplicity.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Syncplicity, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Syncplicity Single Sign-On](#configure-syncplicity-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Syncplicity](#create-syncplicity-test-user)**  – Pokud chcete mít protějšek Britta Simon Syncplicity, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Syncplicity, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Syncplicity** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Syncplicity domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.syncplicity.com`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory Syncplicity klienta](https://www.syncplicity.com/contact-us) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení Syncplicity** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-syncplicity-single-sign-on"></a>Konfigurace Syncplicity jednotné přihlašování

1. Přihlaste se k vaší **Syncplicity** tenanta.

1. V nabídce v horní části klikněte na tlačítko **správce**vyberte **nastavení**a potom klikněte na tlačítko **vlastní domény a jednotné přihlašování**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Na **jednotné přihlašování (SSO)** dialogového okna stránky, proveďte následující kroky:

    ![Jednotné přihlašování \(jednotného přihlašování\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. V **vlastní domény** textového pole zadejte název vaší domény.
  
    b. Vyberte **povolené** jako **jednotné přihlašování – stav**.

    c. V **Entity Id** textového pole vložte hodnotu **Azure Ad identifikátor** zkopírovanou z webu Azure portal.

    d. V **přihlašovací adresa URL stránky** vložit do textového pole **přihlašovací adresa URL** zkopírovanou z webu Azure portal.

    e. V **adresy URL odhlašovací stránky** vložit do textového pole **odhlašovací adresa URL** zkopírovanou z webu Azure portal.

    f. V **certifikát poskytovatele Identity**, klikněte na tlačítko **zvolte soubor**a pak nahrajte certifikát, který jste si stáhli z portálu Azure portal.

    g. Klikněte na tlačítko **ULOŽTE změny**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Syncplicity použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Syncplicity**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Syncplicity**.

    ![Odkaz Syncplicity v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.
    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-syncplicity-test-user"></a>Vytvoření Syncplicity testovacího uživatele

Pro uživatele AAD, bude moct přihlásit musí být zřízená Syncplicity aplikace. Tato část popisuje, jak v Syncplicity vytvořte uživatelské účty AAD.

**Ke zřízení uživatelského účtu pro Syncplicity, proveďte následující kroky:**

1. Přihlaste se k vaší **Syncplicity** tenanta (například: `https://company.Syncplicity.com`).

1. Klikněte na tlačítko **správce** a vyberte **uživatelské účty** a potom klikněte na tlačítko **uživatele přidat**.

    ![Správa uživatelů](./media/syncplicity-tutorial/ic769764.png "Správa uživatelů")

1. Typ **e-mailové adresy** účtu služby Azure AD, které chcete zřídit, vyberte **uživatele** jako **Role**a potom klikněte na tlačítko **Další**.

    ![Informace o účtu](./media/syncplicity-tutorial/ic769765.png "informace o účtu")

    > [!NOTE]
    > Držitel účtu AAD získá e-mailu včetně odkazu na potvrzení a aktivovat účet.

1. Vyberte skupinu ve vaší společnosti, nový uživatel by měl stane členem, a pak klikněte na **Další**.

    ![Členství ve skupině](./media/syncplicity-tutorial/ic769772.png "členství ve skupině")

    > [!NOTE]
    > Pokud nejsou k dispozici žádné skupiny uvedeny, klikněte na tlačítko **Další**.

1. Vyberte složky, které chcete umístit pod kontrolou společnosti Syncplicity na počítači uživatele a potom klikněte na **Další**.

    ![Složky Syncplicity](./media/syncplicity-tutorial/ic769773.png "Syncplicity složek")

> [!NOTE]
> Můžete použít jakékoli jiné Syncplicity uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Syncplicity uživatelským účtům, zřídit AAD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Syncplicity na přístupovém panelu, můžete by měl být automaticky přihlášeni k Syncplicity, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
