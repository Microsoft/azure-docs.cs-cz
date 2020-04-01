---
title: 'Kurz: Integrace služby Azure Active Directory se softwarem Igloo | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a softwarem Igloo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/06/2019
ms.author: jeedes
ms.openlocfilehash: df1d70f895e2e0a81344cf2a4e8e2d9963c951fa
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67100583"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Kurz: Integrace Azure Active Directory se softwarem Igloo

V tomto kurzu se dozvíte, jak integrovat software Igloo s Azure Active Directory (Azure AD).
Integrace softwaru Igloo s Azure AD vám přináší následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k softwaru Igloo.
* Uživatelům můžete povolit automatické přihlášení k softwaru Igloo Software (jednotné přihlášení) pomocí jejich účtů Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Igloo Software, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební [verzi zde](https://azure.microsoft.com/pricing/free-trial/)
* Předplatné s povoleným jedním přihlášením igloo Software

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Igloo Software podporuje **SP** inicioval SSO
* Igloo Software podporuje **just in time** zřizování uživatelů

## <a name="adding-igloo-software-from-the-gallery"></a>Přidání Softwaru Igloo z galerie

Chcete-li nakonfigurovat integraci softwaru Igloo do Azure AD, musíte přidat Software Igloo z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat software Igloo z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Igloo Software**, vyberte **Igloo Software** z panelu výsledků a pak klepněte na tlačítko **Přidat** a přidejte aplikaci.

     ![Igloo Software v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí softwaru Igloo na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Igloo Software.

Chcete-li konfigurovat a testovat jednotné přihlašování Azure AD pomocí softwaru Igloo, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlášení softwaru Igloo](#configure-igloo-software-single-sign-on)** - konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu softwaru Igloo](#create-igloo-software-test-user)** – chcete-li mít protějšek Britta Simon v Igloo Software, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí softwaru Igloo, proveďte následující kroky:

1. Na [portálu Azure](https://portal.azure.com/)na stránce integrace aplikací **Igloo Software** vyberte **Jednotné přihlašování**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlášení k doméně softwaru Igloo a adresURL](common/sp-identifier-reply.png)

    a. Do textového pole **Přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<company name>.igloocommmunities.com`

    b. Do pole **Identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://<company name>.igloocommmunities.com/saml.digest`

    c. Do textového pole **Odpovědět na adresu URL** zadejte adresu URL pomocí následujícího vzoru:`https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty skutečnou přihlašovací adresou URL, identifikátorem a adresou URL pro odpověď. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klienta softwaru Igloo.](https://www.igloosoftware.com/services/support) Můžete také odkazovat na vzory uvedené v části **Základní konfigurace SAML** na webu Azure Portal.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit software Igloo** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-igloo-software-single-sign-on"></a>Konfigurace jednotného přihlašování softwaru Igloo

1. V jiném okně webového prohlížeče se přihlaste na web společnosti Igloo Software jako správce.

2. Přejděte na **Ovládací panely**.

     ![Ovládací panel](./media/igloo-software-tutorial/ic799949.png "Ovládací panel")

3. Na kartě **Členství** klikněte na **Přihlásit se k Nastavení**.

    ![Nastavení přihlášení](./media/igloo-software-tutorial/ic783968.png "Nastavení přihlášení")

4. V části Konfigurace SAML klepněte na **položku Konfigurovat ověřování SAML**.

    ![Konfigurace SAML](./media/igloo-software-tutorial/ic783969.png "Konfigurace SAML")

5. V části **Obecná konfigurace** proveďte následující kroky:

    ![Obecná konfigurace](./media/igloo-software-tutorial/ic783970.png "Obecná konfigurace")

    a. Do textového pole **Název připojení** zadejte vlastní název konfigurace.

    b. V textovém poli **Adresa URL přihlášení k ověření přihlašovacího údaje** vložte hodnotu přihlašovací adresy **URL,** kterou jste zkopírovali z webu Azure Portal.

    c. V textovém poli **Url odhlášení od protokolu idp** vložte hodnotu **adresy URL odhlášení,** kterou jste zkopírovali z webu Azure Portal.

    d. Vyberte **možnost Odhlásit odpověď a požádat o typ HTTP** jako **POST**.

    e. Otevřete svůj certifikát kódovaný **base-64** v poznámkovém bloku staženém z webu Azure Portal, zkopírujte jeho obsah do schránky a vložte ho do textového pole **Veřejný certifikát.**

6. V **konfiguraci odpovědí a ověřování**proveďte následující kroky:

    ![Konfigurace odpovědi a ověřování](./media/igloo-software-tutorial/IC783971.png "Konfigurace odpovědi a ověřování")
  
    a. Jako **zprostředkovatel identity**vyberte **položku Microsoft ADFS**.

    b. Jako **typ identifikátoru**vyberte **možnost E-mailová adresa**. 

    c. Do textového pole **Atribut e-mailu** zadejte **e-mailovou adresu**.

    d. Do textového pole **Atribut křestního jména** zadejte **givenname**.

    e. Do textového pole **Atribut příjmení** zadejte **příjmení**.

7. K dokončení konfigurace proveďte následující kroky:

    ![Vytvoření uživatele při přihlášení](./media/igloo-software-tutorial/IC783972.png "Vytvoření uživatele při přihlášení") 

    a. Jako **vytvoření uživatele při přihlášení**vyberte při přihlášení možnost Vytvořit **nového uživatele na webu**.

    b. Jako **Nastavení přihlášení**vyberte použít tlačítko **SAML na obrazovce "Přihlásit se"**.

    c. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele na webu Azure portal s názvem Britta Simon.

1. Na webu Azure Portal v levém podokně vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.

    ![Odkazy "Uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel.**

    ![Tlačítko nového uživatele](common/new-user.png)

3. Ve vlastnostech User proveďte následující kroky.

    ![Dialogové okno Uživatel](common/user-properties.png)

    a. Do pole **Název** zadejte **BrittaSimon**.
  
    b. V poli **Uživatelské jméno** typ pole**brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte **políčko Zobrazit heslo** a poznamenejte si hodnotu, která se zobrazí v poli Heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlášení udělením přístupu k Softwaru Igloo.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Igloo Software**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Igloo Software**.

    ![Odkaz Software Igloo v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-igloo-software-test-user"></a>Vytvořit testovacího uživatele softwaru Igloo

Neexistuje žádná položka akce pro konfiguraci zřizování uživatelů na Igloo Software.  

Když se přiřazený uživatel pokusí přihlásit k Softwaru Igloo pomocí přístupového panelu, Igloo Software zkontroluje, zda uživatel existuje.  Pokud ještě není k dispozici žádný uživatelský účet, je automaticky vytvořen společností Igloo Software.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Igloo Software na přístupovém panelu, měli byste být automaticky přihlášeni k Softwaru Igloo, pro které nastavíte přispojené k správě. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)