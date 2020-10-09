---
title: 'Kurz: Azure Active Directory integrace s Asana | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Asana.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: dd2c02f25584fd32acd32277a1dd2300c6a98e73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88549496"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Kurz: Azure Active Directory integrace s Asana

V tomto kurzu se dozvíte, jak integrovat Asana s Azure Active Directory (Azure AD).
Integrace Asana s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Asana.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Asana (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Asana potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným Asanam jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Asana podporuje jednotné přihlašování iniciované v **SP**

* Asana podporuje [ **automatizované** zřizování uživatelů](asana-provisioning-tutorial.md)

## <a name="adding-asana-from-the-gallery"></a>Přidání Asana z Galerie

Pokud chcete nakonfigurovat integraci Asana do služby Azure AD, musíte přidat Asana z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Asana z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Asana**, vyberte **Asana** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Asana v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Asana na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Asana.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Asana, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Asana](#configure-asana-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření Asana Test User](#create-asana-test-user)** – pro Britta Simon v Asana, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Asana, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Asana** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně Asana a adresách URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL: `https://app.asana.com/`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL: `https://app.asana.com/`

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení Asana** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-asana-single-sign-on"></a>Nakonfigurovat Asana jednu Sign-On

1. V jiném okně prohlížeče se přihlaste k aplikaci Asana. Pokud chcete nakonfigurovat jednotné přihlašování v Asana, přejděte do nastavení pracovního prostoru kliknutím na název pracovního prostoru v pravém horním rohu obrazovky. Pak klikněte na ** \<your workspace name\> Nastavení**.

    ![Nastavení jednotného přihlašování Asana](./media/asana-tutorial/tutorial_asana_09.png)

2. V okně **Nastavení organizace** klikněte na **Správa**. Pak klikněte na **Členové se musí přihlásit přes SAML** a povolit konfiguraci jednotného přihlašování. Proveďte následující kroky:

    ![Konfigurovat nastavení jedné Sign-On organizace](./media/asana-tutorial/tutorial_asana_10.png)  

    a. Do textového pole **Adresa URL přihlašovací stránky** vložte **přihlašovací adresu URL**.

    b. Klikněte pravým tlačítkem na certifikát stažený z Azure Portal a pak otevřete soubor certifikátu pomocí poznámkového bloku nebo preferovaného textového editoru. Zkopírujte obsah mezi nadpisem začátek a konec certifikátu a vložte ho do textového pole **certifikát X. 509** .

3. Klikněte na **Uložit**. Pokud potřebujete další pomoc, přečtěte si [Průvodce Asana pro nastavení jednotného přihlašování](https://asana.com/guide/help/premium/authentication#gl-saml) .

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Asana.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Asana**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Asana**.

    ![Odkaz Asana v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-asana-test-user"></a>Vytvořit testovacího uživatele Asana

Cílem této části je vytvořit uživatele s názvem Britta Simon v Asana. Asana podporuje automatické zřizování uživatelů, které je ve výchozím nastavení povolené. Další podrobnosti najdete v [tématu](asana-provisioning-tutorial.md) konfigurace automatického zřizování uživatelů.

**Pokud potřebujete ručně vytvořit uživatele, proveďte prosím následující kroky:**

V této části vytvoříte uživatele s názvem Britta Simon v Asana.

1. V **Asana**přejdete do části **týmy** na levém panelu. Klikněte na tlačítko se symbolem plus.

    ![Vytvoření testovacího uživatele Azure AD](./media/asana-tutorial/tutorial_asana_12.png)

2. Do textového pole zadejte e-mail uživatele, jako je **Britta. simon \@ contoso.com** , a pak vyberte **pozvat**.

3. Klikněte na **Odeslat pozvánku**. Nový uživatel dostane e-mail na svůj e-mailový účet. uživatel bude muset účet vytvořit a ověřit.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Asana, měli byste se automaticky přihlásit k Asana, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurace zřizování uživatelů](asana-provisioning-tutorial.md)
