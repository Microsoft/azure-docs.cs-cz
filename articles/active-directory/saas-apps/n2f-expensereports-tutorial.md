---
title: 'Kurz: Integrace Azure Active Directory s N2F - sestavy výdajů | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a N2F - sestavy výdajů.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 7380cf6727817d99edbedf5552c8bea42f177074
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59261679"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Kurz: Integrace Azure Active Directory s N2F - sestavy výdajů

V tomto kurzu se dozvíte, jak integrovat N2F - sestavy výdajů se službou Azure Active Directory (Azure AD).
Integrace N2F - vyúčtování s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k N2F - sestavy výdajů.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k N2F - vyúčtování (Single Sign-On) pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s N2F - vyúčtování, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* N2F - vyúčtování jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Sestavy výdajů N2F - podporuje **SP** a **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Přidání N2F - vyúčtování z Galerie

Konfigurace integrace N2F - vyúčtování do služby Azure AD, budete muset přidat N2F - výdajů v galerii na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat N2F - vyúčtování z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **N2F - vyúčtování**vyberte **N2F - vyúčtování** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![N2F - sestavy výdajů v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testovací služby Azure AD jednotné přihlašování s N2F - vyúčtování podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování k práci, vztah odkazu mezi uživatele služby Azure AD a související uživatelské v N2F - sestavy výdajů je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s N2F - sestavy výdajů, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace N2F – Single Sign-On sestavy výdajů](#configure-n2f---expense-reports-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření N2F – vyúčtování testovací uživatele](#create-n2f---expense-reports-test-user)**  – aby protějšek Britta Simon v N2F - sestavy výdajů připojený k Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s N2F - vyúčtování, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **N2F - vyúčtování** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, uživatel nebude muset provést všechny kroky, protože aplikace je už předem integrované se službou Azure.

    ![N2F - vyúčtování domény a adresy URL jednotného přihlašování – informace](common/preintegrated.png)

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![N2F - vyúčtování domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL:  `https://www.n2f.com/app/`

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a uložte ji na vaše počítač.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

7. Na **nastavení myPolicies** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-n2f---expense-reports-single-sign-on"></a>Konfigurace N2F - vyúčtování jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k vaší N2F - webu společnosti sestavy výdajů jako správce.

2. Klikněte na **nastavení** a pak vyberte **rozšířená nastavení** z rozevíracího seznamu.

    ![N2F - vyúčtování konfigurace](./media/n2f-expensereports-tutorial/configure1.png)

3. Vyberte **nastavení účtu** kartu.

    ![N2F - vyúčtování konfigurace](./media/n2f-expensereports-tutorial/configure2.png)

4. Vyberte **ověřování** a pak vyberte **+ přidat metodu ověřování** kartu.

    ![N2F - vyúčtování konfigurace](./media/n2f-expensereports-tutorial/configure3.png)

5. Vyberte **SAML Microsoft Office 365** jako metodu ověřování.

    ![N2F - vyúčtování konfigurace](./media/n2f-expensereports-tutorial/configure4.png)

6. Na **metodu ověřování** části, proveďte následující kroky:

    ![N2F - vyúčtování konfigurace](./media/n2f-expensereports-tutorial/configure5.png)

    a. V **Entity ID** vložit do textového pole **Azure AD identifikátor** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    b. V **adresa URL metadat** vložit do textového pole **adresa Url federačních metadat aplikace** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    c. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k N2F – sestavy výdajů.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **N2F - vyúčtování**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **N2F - vyúčtování**.

    ![Sestavy výdajů N2F - odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-n2f---expense-reports-test-user"></a>Vytvoření N2F – vyúčtování testovací uživatele

Přihlaste se k N2F - vyúčtování, Azure AD uživatelům umožnit, musí být zřízená do N2F – sestavy výdajů. V případě N2F - vyúčtování, zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší N2F - webu společnosti sestavy výdajů jako správce.

2. Klikněte na **nastavení** a pak vyberte **rozšířená nastavení** z rozevíracího seznamu.

    ![N2F - výdajů přidat uživatele](./media/n2f-expensereports-tutorial/configure1.png)

3. Vyberte **uživatelé** kartu v levém navigačním panelu.

    ![N2F - vyúčtování konfigurace](./media/n2f-expensereports-tutorial/user1.png)

4. Vyberte **+ nový uživatel** kartu.

    ![N2F - vyúčtování konfigurace](./media/n2f-expensereports-tutorial/user2.png)

5. Na **uživatele** části, proveďte následující kroky:

    ![N2F - vyúčtování konfigurace](./media/n2f-expensereports-tutorial/user3.png)

    a. V **e-mailová adresa** textového pole zadejte e-mailovou adresu uživatele, jako je **brittasimon\@contoso.com**.

    b. V **křestní jméno** textového pole zadejte jméno uživatele, jako je **Britta**.

    c. V **název** textového pole zadejte jméno uživatele, jako je **BrittaSimon**.

    d. Zvolte **Role, správce s přímým přístupem (N + 1)**, a **dělení** podle požadavků vaší organizace.

    e. Klikněte na tlačítko **ověřením a odeslat pozvánky**.

    > [!NOTE]
    > Pokud máte jakékoli problémy při přidávání uživatele, obraťte se prosím [N2F - vyúčtování tým podpory](mailto:support@n2f.com)

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete N2F – vyúčtování dlaždici na přístupovém panelu, vám by měl být automaticky přihlášeni ke N2F - vyúčtování, pro které můžete nastavit jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

