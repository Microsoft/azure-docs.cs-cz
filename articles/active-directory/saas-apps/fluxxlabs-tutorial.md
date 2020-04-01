---
title: 'Kurz: Integrace služby Azure Active Directory s laboratořemi Fluxx | Dokumenty společnosti Microsoft'
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67102395"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Kurz: Integrace Azure Active Directory s Laboratořemi Fluxx

V tomto kurzu se dozvíte, jak integrovat Laboratoře Fluxx s Azure Active Directory (Azure AD).
Integrace laboratoří Fluxx s Azure AD vám přináší následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Fluxx Labs.
* Můžete povolit, aby se uživatelé automaticky přihlašovali ke službě Fluxx Labs (Jednotné přihlášení) pomocí svých účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Fluxx Labs, potřebujete následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Předplatné s povoleným jedním přihlášením fluxx Labs

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Fluxx Labs podporuje **IDP** inicioval SSO

## <a name="adding-fluxx-labs-from-the-gallery"></a>Přidání Fluxx Labs z galerie

Chcete-li nakonfigurovat integraci laboratoří Fluxx do azure ad, musíte přidat Laboratoře Fluxx z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Laboratoře Fluxx z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Fluxx Labs**, z panelu výsledků vyberte **Fluxx Labs** a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![Fluxx Labs v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí laboratoří Fluxx na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v laboratořích Fluxx.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí laboratoří Fluxx, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace single sign-on společnosti Fluxx Labs](#configure-fluxx-labs-single-sign-on)** - konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte testovacího uživatele Fluxx Labs](#create-fluxx-labs-test-user)** – chcete-li mít protějšek Britta Simon v Laboratořích Fluxx, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí laboratoří Fluxx, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Fluxx Labs** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **Nastavit jednotné přihlašování pomocí saml** proveďte následující kroky:

    ![Fluxx Labs Domény a adresy URL jednotné přihlašovací informace](common/idp-intiated.png)

    a. Do textového pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    | Prostředí | Vzor adresy URL|
    |-------------|------------|
    | Výroba | `https://<subdomain>.fluxx.io` |
    | Předprodukce | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:

    | Prostředí | Vzor adresy URL|
    |-------------|------------|
    | Výroba | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Předprodukce | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou adresou URL identifikátoru a odpovědi. Obraťte se na [tým podpory klienta Fluxx Labs,](mailto:travis@fluxxlabs.com) abyste získali tyto hodnoty. Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit laboratoře Fluxx** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-fluxx-labs-single-sign-on"></a>Konfigurace jednorázového přihlášení laboratoří Fluxx Labs

1. V jiném okně webového prohlížeče se přihlaste na web společnosti Fluxx Labs jako správce.

2. Pod oddílem **Nastavení** vyberte **Správce.**

    ![Konfigurace laboratoří Fluxx](./media/fluxxlabs-tutorial/config1.png)

3. V panelu správce vyberte > **integrace** **modulů plug-in**a pak vyberte **SAML SSO-(Disabled)**

    ![Konfigurace laboratoří Fluxx](./media/fluxxlabs-tutorial/config2.png)

4. V části atributů proveďte následující kroky:

    ![Konfigurace laboratoří Fluxx](./media/fluxxlabs-tutorial/config3.png)

    a. Zaškrtněte políčko **Saml SSO.**

    b. Do textového pole **Žádost o cestu** zadejte **/auth/saml**.

    c. Do textového pole **Cesta zpětného volání** zadejte **/auth/saml/callback**.

    d. Do textového pole **Url služby Assertion Consumer Service (adresa URL jednotného přihlášení)** zadejte hodnotu **adresy URL pro odpověď,** kterou jste zadali na webu Azure Portal.

    e. Do textového pole **Cílová skupina (ID sp)** zadejte hodnotu **Identifikátor,** kterou jste zadali na webu Azure Portal.

    f. V textovém poli **cílová adresa URL zprostředkovatele identity** vložte hodnotu **přihlašovací adresy URL,** kterou jste zkopírovali z portálu Azure.

    g. Otevřete svůj certifikát kódovaný base-64 v poznámkovém bloku, zkopírujte jeho obsah do schránky a vložte jej do textového pole **Certifikát u poskytovatele identity.**

    h. Do textového pole **Název identifikátoru Formát** zadejte hodnotu `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    i. Klikněte na **Uložit**.

    > [!NOTE]
    > Po uložení obsahu se pole zobrazí jako prázdné z hlediska zabezpečení, ale hodnota byla uložena v konfiguraci.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské** brittasimon@yourcompanydomain.extensionjméno typ pole . Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Fluxx Labs.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Fluxx Labs**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Možnost INaXx Labs**.

    ![Odkaz Fluxx Labs v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-fluxx-labs-test-user"></a>Vytvořit testovacího uživatele Fluxx Labs

Chcete-li povolit uživatelům Azure AD přihlásit se k Fluxx Labs, musí být zřízena do Fluxx Labs. V případě Fluxx Labs zřizování je ruční úloha.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se na své stránky společnosti Fluxx Labs jako správce.

2. Klikněte na níže zobrazenou **ikonu**.

    ![Konfigurace laboratoří Fluxx](./media/fluxxlabs-tutorial/config6.png)

3. Na řídicím panelu kliknutím na níže zobrazenou ikonu otevřete kartu **New PEOPLE.**

    ![Konfigurace laboratoří Fluxx](./media/fluxxlabs-tutorial/config4.png)

4. V části **NEW PEOPLE** proveďte následující kroky:

    ![Konfigurace laboratoří Fluxx](./media/fluxxlabs-tutorial/config5.png)

    a. Fluxx Labs používat e-mail jako jedinečný identifikátor pro přihlášení přihlašování s přihlašování. Naplňte pole **SSO UID** s e-mailovou adresou uživatele, která odpovídá e-mailové adrese, kterou používají jako přihlášení pomocí služby SSO.

    b. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Fluxx Labs na přístupovém panelu, měli byste být automaticky přihlášeni k Fluxx Labs, pro které nastavíte sso. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

