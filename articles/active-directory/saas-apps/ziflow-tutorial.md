---
title: 'Kurz: Integrace služby Azure Active Directory se službou Ziflow | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Ziflow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: d9745bdb1cb6de86a96946564865958433d49732
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086198"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Kurz: Integrace služby Azure Active Directory se službou Ziflow

V tomto kurzu se dozvíte, jak integrovat Ziflow s Azure Active Directory (Azure AD).
Integrace Ziflow s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Ziflow.
* Můžete povolit uživatelům, aby se automaticky přihlásili k Ziflow (jednotné přihlášení) s jejich účty Azure AD.
* Své účty můžete spravovat v jednom centrálním umístění – na portálu Azure.

Pokud se chcete dozvědět více podrobností o integraci aplikací SaaS s Azure AD, přečtěte [si, co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat integraci Azure AD s Ziflow, budete potřebovat následující položky:

* Předplatné Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Předplatné s povoleným jedním přihlášením Ziflow

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Ziflow podporuje **sp** iniciované sso

## <a name="adding-ziflow-from-the-gallery"></a>Přidání Ziflow z galerie

Chcete-li nakonfigurovat integraci Ziflow do Azure AD, je potřeba přidat Ziflow z galerie do seznamu spravovaných aplikací SaaS.

**Chcete-li přidat Ziflow z galerie, proveďte následující kroky:**

1. Na **[portálu Azure](https://portal.azure.com)** klikněte na levém navigačním panelu na ikonu **Služby Azure Active Directory.**

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikových aplikací** a pak vyberte možnost **Všechny aplikace.**

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klepněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Ziflow**, z panelu výsledků vyberte **Ziflow** a pak klepněte na **tlačítko Přidat** a přidejte aplikaci.

     ![Ziflow v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Ziflow na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být vytvořen vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem v Ziflow.

Chcete-li nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Ziflow, musíte dokončit následující stavební bloky:

1. **[Nakonfigurujte azure ad jednotné přihlašování](#configure-azure-ad-single-sign-on)** – aby vaši uživatelé mohli používat tuto funkci.
2. **[Konfigurace jednotného přihlašování Ziflow](#configure-ziflow-single-sign-on)** – konfigurace nastavení jednotného přihlášení na straně aplikace.
3. **[Vytvořte uživatele testu Azure AD](#create-an-azure-ad-test-user)** – k testování jednotného přihlášení Azure AD s Brittou Simonovou.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** – chcete-li Britta Simon ové povolit použití jednotného přihlášení azure ad.
5. **[Vytvořte uživatele testu Ziflow](#create-ziflow-test-user)** – chcete-li mít protějšek Britta Simon v Ziflow, který je propojený s reprezentací Azure AD uživatele.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** - chcete-li ověřit, zda konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD na webu Azure Portal.

Chcete-li nakonfigurovat jednotné přihlašování Azure AD pomocí technologie Ziflow, proveďte následující kroky:

1. Na [webu Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Ziflow** vyberte **Jedno přihlášení**.

    ![Konfigurace odkazu pro jednotné přihlášení](common/select-sso.png)

2. V **dialogovém okně Vybrat metodu jednotného přihlašování** vyberte režim **SAML/WS-Fed,** abyste povolili jednotné přihlašování.

    ![Režim výběru jednotného přihlášení](common/select-saml-option.png)

3. Na stránce **Nastavit jednotné přihlašování pomocí saml** kliknutím na ikonu **Upravit** otevřete dialogové okno Základní **konfigurace SAML.**

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **Základní konfigurace SAML** proveďte následující kroky:

    ![Ziflow Domény a adresy URL jednotné přihlašovací informace](common/sp-identifier.png)

    a. Do textového pole **Adresa URL přihlášení** zadejte adresu URL pomocí následujícího vzoru:`https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. Do textového pole **Identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > Předchozí hodnoty nejsou skutečné. Budete aktualizovat jedinečnou hodnotu ID v identifikátora a přihlásit na URL se skutečnou hodnotou, která je vysvětlena dále v kurzu.

5. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte v části **Podpisový certifikát SAML** na **Stáhnout** a stáhněte si **certifikát (Base64)** z daných možností podle vašeho požadavku a uložte jej do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavit Ziflow** zkopírujte příslušnou adresu URL podle vašeho požadavku.

    ![Kopírování konfiguračních adres URL](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor azure reklamy

    c. Adresa URL odhlášení

### <a name="configure-ziflow-single-sign-on"></a>Konfigurace jednotného přihlašování Ziflow

1. V jiném okně webového prohlížeče se přihlaste ke službě Ziflow jako správce zabezpečení.

2. Klikněte na Avatar v pravém horním rohu a potom klikněte na **Spravovat účet**.

    ![Správa konfigurace Ziflow](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

3. V levém horním rohu klikněte na **Jedno přihlášení**.

    ![Konfigurační znak Ziflow](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

4. Na stránce **Jednotné přihlašování** proveďte následující kroky:

    ![Konfigurace Ziflow](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Vyberte **typ** jako **SAML2.0**.

    b. Do textového pole **Přihlásit se k adrese URL** vložte hodnotu přihlašovací adresy **URL**, kterou jste zkopírovali z portálu Azure.

    c. Nahrajte kódovaný certifikát base-64, který jste stáhli z webu Azure Portal, do **podpisového certifikátu X509**.

    d. Do textového pole **Odhlásit adresu URL** vložte hodnotu **adresy URL odhlášení**, kterou jste zkopírovali z webu Azure Portal.

    e. V části **Nastavení konfigurace pro zprostředkovatele identifikátorů zkopírujte** zvýrazněnou jedinečnou hodnotu ID a přidejte ji pomocí adresy URL identifikátoru a přihlášení na **portálu Basic SAML Configuration** na webu Azure Portal.

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

V této části povolíte Britta Simon používat Azure jednotné přihlašování udělením přístupu k Ziflow.

1. Na portálu Azure vyberte **Podnikové aplikace**, vyberte **Všechny aplikace**a pak vyberte **Ziflow**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Možnost Ziflow**.

    ![Odkaz Ziflow v seznamu Aplikace](common/all-applications.png)

3. V nabídce vlevo vyberte **Možnost Uživatelé a skupiny**.

    ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte **brittu Simonovou** v seznamu Uživatelé a klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a klepněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit.**

### <a name="create-ziflow-test-user"></a>Vytvořit testovacího uživatele Ziflow

Chcete-li povolit uživatelům Azure AD k přihlášení k Ziflow, musí být zřízena do Ziflow. V Ziflow zřizování je ruční úlohy.

Chcete-li zřídit uživatelský účet, proveďte následující kroky:

1. Přihlaste se k Ziflow jako správce zabezpečení.

2. Přejděte na **lidi** nahoře.

    ![Uživatelé konfigurace Ziflow](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Klikněte na **Přidat** a potom na **Přidat uživatele**.

    ![Ziflow Konfigurace přidání uživatele](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. V místní nabídce **Přidat uživatele** proveďte následující kroky:

    ![Ziflow Konfigurace přidání uživatele](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. Do textového pole **E-mail** brittasimon@contoso.comzadejte e-mail uživatele, který se líbí .

    b. Do textového pole **Jméno** zadejte křestní jméno uživatele, jako je Britta.

    c. Do pole **Příjmení** zadejte příjmení uživatele, jako je Simon.

    d. Vyberte svou roli Ziflow.

    e. Klepněte na tlačítko **Přidat 1 uživatele**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail a následuje odkaz pro potvrzení svého účtu před tím, než se stane aktivním.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlášení Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Ziflow na přístupovém panelu, můžete by měl být automaticky přihlášeni k Ziflow, u kterého nastavíte přihlašující. Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

