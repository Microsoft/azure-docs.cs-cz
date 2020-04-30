---
title: 'Kurz: Azure Active Directory integrace s Fluxx Labs | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Fluxx Labs.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "67102395"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Kurz: Azure Active Directory integrace s Fluxx Labs

V tomto kurzu se dozvíte, jak integrovat Fluxx Labs s Azure Active Directory (Azure AD).
Integrace Fluxx Labs s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Fluxx Labs.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Fluxx Labs (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Fluxx Labs potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné Fluxx Labs s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Fluxx Labs podporuje **IDPy** iniciované jednotné přihlašování.

## <a name="adding-fluxx-labs-from-the-gallery"></a>Přidání Fluxx Labs z Galerie

Pokud chcete nakonfigurovat integraci Fluxx Labs do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Fluxx Labs z galerie.

**Pokud chcete přidat Fluxx Labs z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Fluxx Labs**, vyberte **Fluxx Labs** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Fluxx Labs v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Fluxx Labs na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je potřeba zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Fluxx Labs.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Fluxx Labs, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Konfigurace jednotného přihlašování Fluxx Labs](#configure-fluxx-labs-single-sign-on)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele Fluxx Labs](#create-fluxx-labs-test-user)** – můžete mít protějšek Britta Simon v Fluxx Labs, který se odkazuje na reprezentaci uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Fluxx Labs, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **Fluxx Labs** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **nastavit jednotné přihlašování pomocí SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování domény a adresy URL Fluxx Labs](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    | Prostředí | Vzor adresy URL|
    |-------------|------------|
    | Výroba | `https://<subdomain>.fluxx.io` |
    | Předběžná produkce | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:

    | Prostředí | Vzor adresy URL|
    |-------------|------------|
    | Výroba | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Předběžná produkce | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory pro klienty Fluxx Labs](mailto:travis@fluxxlabs.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení Fluxx Labs** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-fluxx-labs-single-sign-on"></a>Konfigurace jednotného přihlašování v Fluxx Labs

1. V jiném okně webového prohlížeče se přihlaste k webu společnosti Fluxx Labs jako správce.

2. V části **Nastavení** vyberte **správce** .

    ![Konfigurace Fluxx Labs](./media/fluxxlabs-tutorial/config1.png)

3. Na panelu pro správu vyberte integrace **s modulem plug-in** > **Integrations** a pak vyberte **jednotné přihlašování SAML – (zakázáno)** .

    ![Konfigurace Fluxx Labs](./media/fluxxlabs-tutorial/config2.png)

4. V části atributu proveďte následující kroky:

    ![Konfigurace Fluxx Labs](./media/fluxxlabs-tutorial/config3.png)

    a. Zaškrtněte políčko **SAML SSO** .

    b. Do textového pole **Cesta požadavku** zadejte **/auth/SAML**.

    c. Do textového pole **cesta zpětného volání** zadejte **/auth/SAML/callback**.

    d. Do textového pole **Adresa URL služby assertion Consumer Service (adresa URL jednotného přihlašování)** zadejte hodnotu **adresy URL odpovědi** , kterou jste zadali v Azure Portal.

    e. Do textového pole **cílová skupina (ID entity SP)** zadejte hodnotu **identifikátoru** , kterou jste zadali v Azure Portal.

    f. Do textového pole **Adresa URL cíle jednotného přihlašování zprostředkovatele identity** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    g. Otevřete v programu Poznámkový blok certifikát s kódováním Base-64, zkopírujte jeho obsah do schránky a vložte ho do textového pole **certifikát poskytovatele identity** .

    h. Do textového pole **Formát identifikátoru názvu** zadejte hodnotu `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    i. Klikněte na **Uložit**.

    > [!NOTE]
    > Po uložení obsahu se pole pro zabezpečení zobrazí jako prázdné, ale hodnota se uloží do konfigurace.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte brittasimon@yourcompanydomain.extension. Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Fluxx Labs.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Fluxx Labs**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Fluxx Labs**.

    ![Odkaz na Fluxx Labs v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-fluxx-labs-test-user"></a>Vytvořit testovacího uživatele pro Fluxx Labs

Aby se uživatelé Azure AD mohli přihlašovat k Fluxx Labs, musí se zřídit v Fluxx Labs. V případě Fluxx Labs je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k firemnímu webu Fluxx Labs jako správce.

2. Klikněte na **ikonu**zobrazené níže.

    ![Konfigurace Fluxx Labs](./media/fluxxlabs-tutorial/config6.png)

3. Na řídicím panelu klikněte na ikonu níže zobrazené a otevřete kartu **nové osoby** .

    ![Konfigurace Fluxx Labs](./media/fluxxlabs-tutorial/config4.png)

4. V části **noví lidé** proveďte následující kroky:

    ![Konfigurace Fluxx Labs](./media/fluxxlabs-tutorial/config5.png)

    a. Fluxx Labs jako jedinečný identifikátor pro přihlašovací jména jednotného přihlašování používá e-mail. Vyplňte pole **UID jednotného přihlašování** pomocí e-mailové adresy uživatele, která se shoduje s e-mailovou adresou, kterou používají jako přihlašovací údaje s SSO.

    b. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Fluxx Labs na přístupovém panelu, měli byste se automaticky přihlásit k Fluxx Labs, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

