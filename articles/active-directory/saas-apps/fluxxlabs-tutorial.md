---
title: 'Kurz: Integrace Azure Active Directory s praktickými cvičeními Fluxx | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Fluxx Labs.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: e624520a9d1f39bc8115ac72e9df0398065928f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67102395"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Kurz: Integrace Azure Active Directory s praktickými cvičeními Fluxx

V tomto kurzu se dozvíte, jak Fluxx Labs integrovat s Azure Active Directory (Azure AD).
Integrace s Azure AD Fluxx Labs poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k testovacím prostředím Fluxx.
* Uživatelům se automaticky přihlášeni k testovacím prostředím Fluxx (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s praktickými cvičeními Fluxx, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Fluxx Labs jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Fluxx Labs **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-fluxx-labs-from-the-gallery"></a>Přidání Fluxx Labs z Galerie

Ke konfiguraci integrace Fluxx Labs do služby Azure AD, budete muset přidat Fluxx Labs z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání Fluxx Labs z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Fluxx Labs**vyberte **Fluxx Labs** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Cvičení Fluxx v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testování Azure AD jednotného přihlašování Labs Fluxx podle testovacího uživatele, volá se, **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Fluxx Labs.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s praktickými cvičeními Fluxx, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Fluxx Labs Single Sign-On](#configure-fluxx-labs-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Fluxx Labs](#create-fluxx-labs-test-user)**  – Pokud chcete mít protějšek Britta Simon Fluxx Labs, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s praktickými cvičeními Fluxx, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Fluxx Labs** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránce, proveďte následující kroky:

    ![Fluxx Labs domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce:

    | Prostředí | Vzor adresy URL|
    |-------------|------------|
    | Výroba | `https://<subdomain>.fluxx.io` |
    | Předvýroba | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce:

    | Prostředí | Vzor adresy URL|
    |-------------|------------|
    | Výroba | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Předvýroba | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Kontakt [tým podpory Fluxx Labs klienta](mailto:travis@fluxxlabs.com) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení testovacích prostředí Fluxx** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-fluxx-labs-single-sign-on"></a>Konfigurace Fluxx Labs jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se na web společnosti Fluxx Labs jako správce.

2. Vyberte **správce** níže **nastavení** oddílu.

    ![Konfigurace testovacích prostředí Fluxx](./media/fluxxlabs-tutorial/config1.png)

3. V panelu Správce vyberte **moduly plug-in** > **integrace** a pak vyberte **SAML SSO-(Disabled)**

    ![Konfigurace testovacích prostředí Fluxx](./media/fluxxlabs-tutorial/config2.png)

4. V části atribut proveďte následující kroky:

    ![Konfigurace testovacích prostředí Fluxx](./media/fluxxlabs-tutorial/config3.png)

    a. Vyberte **SAML SSO** zaškrtávací políčko.

    b. V **cestu požadavku** textové pole, typ **saml/ověření/** .

    c. V **cestu zpětného volání** textové pole, typ **/auth/saml/callback**.

    d. V **Assertion Consumer Service Url(Single Sign-On URL)** textového pole zadejte **adresy URL odpovědi** hodnotu, která jste zadali na webu Azure Portal.

    e. V **cílová skupina (SP Entity ID)** textového pole zadejte **identifikátor** hodnotu, která jste zadali na webu Azure Portal.

    f. V **cílová adresa URL pro jednotné přihlašování zprostředkovatele Identity** vložit do textového pole **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    g. V poznámkovém bloku otevřete certifikát kódováním base-64, zkopírujte obsah ho do schránky a a vložte ho do **certifikát poskytovatele Identity** textového pole.

    h. V **identifikátor názvu formátu** textového pole zadejte hodnotu `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    i. Klikněte na **Uložit**.

    > [!NOTE]
    > Jakmile je obsah uložen, se zobrazí pole prázdné, zabezpečení, ale hodnota byla uložena v konfiguraci.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k testovacím prostředím Fluxx.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Fluxx Labs**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Fluxx Labs**.

    ![Odkaz Fluxx testovacích prostředí v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-fluxx-labs-test-user"></a>Vytvořit testovací prostředí Fluxx testovacího uživatele

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k testovacím prostředím Fluxx, musí být zřízená ve službě Lab Fluxx. V případě Fluxx Labs zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k webu společnosti Fluxx Labs jako správce.

2. Klikněte na níže zobrazí **ikonu**.

    ![Konfigurace testovacích prostředí Fluxx](./media/fluxxlabs-tutorial/config6.png)

3. Na řídicím panelu, klikněte na níže zobrazené ikony otevřete **nových LIDÍ** karty.

    ![Konfigurace testovacích prostředí Fluxx](./media/fluxxlabs-tutorial/config4.png)

4. Na **nových LIDÍ** části, proveďte následující kroky:

    ![Konfigurace testovacích prostředí Fluxx](./media/fluxxlabs-tutorial/config5.png)

    a. Cvičení Fluxx pomocí e-mailu jako jedinečný identifikátor pro jednotné přihlášení. Naplnění **jednotného přihlašování UID** pole e-mailovou adresu uživatele, který odpovídá e-mailovou adresu, která používají jako přihlašovací údaje pomocí jednotného přihlašování.

    b. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Fluxx Labs na přístupovém panelu, můžete by měl být automaticky přihlášeni k testovacím prostředím Fluxx, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

