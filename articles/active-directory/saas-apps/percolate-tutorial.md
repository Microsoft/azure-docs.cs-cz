---
title: 'Kurz: Integrace Azure Active Directory s provede se perkolace | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a provede se perkolace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 355f9659-b378-44c9-aa88-236e9b529a53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 166a9daed40c03ed603d9ecaf4db2d49da0bf93d
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887132"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Kurz: Integrace Azure Active Directory s provede se perkolace

V tomto kurzu se dozvíte, jak integrovat provede se perkolace s Azure Active Directory (Azure AD).
Integrace s Azure AD provede se perkolace poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k provede se perkolace.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k provede pomocí jejich účtů služby Azure AD se perkolace (Single Sign-On).
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s provede se perkolace, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Provede se perkolace jednotné přihlášení povolený předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Provede se perkolace podporuje **SP** a **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-percolate-from-the-gallery"></a>Přidání provede se perkolace z Galerie

Pokud chcete nakonfigurovat integraci provede se perkolace do služby Azure AD, musíte doplnit provede se perkolace z Galerie váš seznam spravovaných aplikací SaaS.

**Provede se perkolace přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **provede se perkolace**vyberte **provede se perkolace** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Provede se perkolace v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování provede se podle testu uživateli perkolace **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v provede se perkolace.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s provede se perkolace, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace provede se perkolace Single Sign-On](#configure-percolate-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele provede se perkolace](#create-percolate-test-user)**  – Pokud chcete mít v provede, který je propojený s Azure AD reprezentace uživatele se perkolace protějšek Britta Simon.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s provede se perkolace, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **provede se perkolace** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, uživatel nebude muset provést libovolný krok, protože aplikace je už předem integrovaných s Azure.

    ![Provede se perkolace domény a adresy URL jednotné přihlašování – informace](common/preintegrated.png)

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Provede se perkolace domény a adresy URL jednotné přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL:  `https://percolate.com/app/login`

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a uložte ji na vaše počítač.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

7. Na **nastavení provede se perkolace** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-percolate-single-sign-on"></a>Konfigurace provede se perkolace jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k provede se perkolace jako správce.

2. Na levé straně domovské stránky, klikněte na **nastavení**.
    
    ![Konfigurace jednotného přihlašování](./media/percolate-tutorial/configure01.png)

3. V levé části řádku nabídek klikněte na **jednotného přihlašování** pod **organizace**.

    ![Konfigurace jednotného přihlašování](./media/percolate-tutorial/configure02.png)

    a. V **přihlašovací adresa URL** textového pole vložte hodnotu **přihlašovací adresa URL** zkopírovanou z webu Azure portal.

    b. V **Entity ID** textového pole vložte hodnotu **Azure AD identifikátor** zkopírovanou z webu Azure portal.

    c. V poznámkovém bloku otevřete base-64 kódovaných certifikát, který jste si stáhli z webu Azure portal, zkopírujte jeho obsah a vložte jej do **x509 certifikáty** pole.

    d. V **atribut e-mailové** textové pole, typ **emailaddress**.

    e. **Adresa URL metadat zprostředkovatele identity** pole je volitelné a jestli máte **adresa url federačních metadat aplikace** zkopírovanou z webu Azure portal, vložte ji **adresa URL metadat zprostředkovatele Identity** textové pole.

    f. Vyberte **ne** jako **AuthNRequests by měl být podepsané?**.

    g. Vyberte **ne** jako **jednotného přihlašování k povolení automatického zřizování**.

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
  
    b. V **uživatelské jméno** typ pole brittasimon@yourcompanydomain.extension. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon pomocí Azure jednotné přihlašování provede se perkolace udělení přístupu.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **provede se perkolace**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **provede se perkolace**.

    ![Provede se perkolace odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-percolate-test-user"></a>Vytvoření provede se perkolace testovacího uživatele

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k provede se perkolace, musí být poskytnuty do provede se perkolace. V provede se perkolace zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k provede se perkolace jako správce.

2. V levé části řádku nabídek klikněte na **uživatelé** pod **organizace** a přejděte do **noví uživatelé**.

    ![Konfigurace jednotného přihlašování](./media/percolate-tutorial/configure03.png)

3. Na **vytvořit uživatele** stránce, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/percolate-tutorial/configure04.png)

    a. V **e-mailu** textové pole, zadejte e-mailu uživatele, jako je brittasimon@contoso.com.

    b. V **Fullname** textové pole, zadejte jméno uživatele, jako je **Brittasimon**.

    c. Klikněte na tlačítko **vytvořit uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici provede se perkolace na přístupovém panelu, vám by měl být automaticky přihlášeni ke provede se perkolace u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

