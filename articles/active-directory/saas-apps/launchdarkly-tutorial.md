---
title: 'Kurz: Azure Active Directory integrace s LaunchDarkly | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LaunchDarkly.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: 78f9217ea7288bac56b008911f7b39c73bba7771
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856530"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Kurz: Azure Active Directory integrace s LaunchDarkly

V tomto kurzu se dozvíte, jak integrovat LaunchDarkly s Azure Active Directory (Azure AD).
Integrace LaunchDarkly s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k LaunchDarkly.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k LaunchDarkly (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s LaunchDarkly potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným LaunchDarklym jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* LaunchDarkly podporuje jednotné přihlašování (SSO) **a IDP** .
* LaunchDarkly podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-launchdarkly-from-the-gallery"></a>Přidání LaunchDarkly z Galerie

Pokud chcete nakonfigurovat integraci LaunchDarkly do služby Azure AD, musíte přidat LaunchDarkly z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat LaunchDarkly z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **LaunchDarkly**, vyberte **LaunchDarkly** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![LaunchDarkly v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí [název aplikace] na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být navázán vztah odkazu mezi uživatelem služby Azure AD a souvisejícím uživatelem v [název aplikace].

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí [název aplikace], musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování LaunchDarkly](#configure-launchdarkly-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření LaunchDarkly Test User](#create-launchdarkly-test-user)** – pro Britta Simon v LaunchDarkly, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí [název aplikace], proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **LaunchDarkly** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Snímek obrazovky ukazuje základní konfiguraci SAML, kde můžete zadat identifikátor, odpovědět U R L a vybrat Uložit.](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL: `app.launchdarkly.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`

    > [!NOTE]
    > Hodnota adresy URL odpovědi není reálné číslo. Hodnotu aktualizujete skutečnou adresou URL odpovědi, která je vysvětlena dále v tomto kurzu. Pokud hodláte používat aplikaci v režimu **IDP** , musíte ponechat pole pro **přihlášení URL** prázdné, jinak nebudete moct zahájit přihlášení z **IDP**. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://app.launchdarkly.com`

    ![Snímek obrazovky s nastavením další U R ls, kde můžete zadat přihlášení U R L.](common/metadata-upload-additional-signon.png)

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavení LaunchDarkly** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-launchdarkly-single-sign-on"></a>Nakonfigurovat LaunchDarkly jednu Sign-On

1. V jiném okně webového prohlížeče se přihlaste k webu LaunchDarkly společnosti jako správce.

2. V levém navigačním panelu vyberte **Nastavení účtu** .

    ![Snímek obrazovky zobrazuje položku Nastavení účtu vybranou v části výroba.](./media/launchdarkly-tutorial/configure1.png)

3. Klikněte na kartu **zabezpečení** .

    ![Snímek obrazovky s nastavením účtu se zobrazí na kartě zabezpečení.](./media/launchdarkly-tutorial/configure2.png)

4. Klikněte na **Povolit jednotné přihlašování** a pak **Upravte konfiguraci SAML**.

    ![Snímek obrazovky se zobrazí na stránce jednotného přihlašování, kde můžete povolit nastavení S S S/O a upravit konfiguraci SAML.](./media/launchdarkly-tutorial/configure3.png)

5. V části **Upravit konfiguraci SAML** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí v oddílu upravit konfiguraci SAML, kde můžete provádět změny popsané tady.](./media/launchdarkly-tutorial/configure4.png)

    a. Zkopírujte **adresu URL služby příjemce SAML** pro vaši instanci a vložte ji do textového pole Adresa URL odpovědi v části **LaunchDarkly domény a adresy URL** na Azure Portal.

    b. Do textového pole **Adresa URL pro přihlášení** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. Otevřete stažený certifikát z Azure Portal do programu Poznámkový blok, zkopírujte obsah a vložte ho do pole **certifikát X. 509** , nebo můžete certifikát nahrát přímo kliknutím na **nahrát**ho.

    d. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k LaunchDarkly.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **LaunchDarkly**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **LaunchDarkly**.

    ![Odkaz LaunchDarkly v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-launchdarkly-test-user"></a>Vytvořit testovacího uživatele LaunchDarkly

Cílem této části je vytvořit uživatele s názvem Britta Simon v LaunchDarkly. LaunchDarkly podporuje zřizování za běhu, které je ve výchozím nastavení povolené. V této části není žádná položka akce. Nový uživatel se vytvoří během pokusu o přístup k LaunchDarkly, pokud ještě neexistuje.

> [!Note]
> Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory LaunchDarkly Client](mailto:support@launchdarkly.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici LaunchDarkly, měli byste se automaticky přihlásit k LaunchDarkly, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
