---
title: 'Kurz: Azure Active Directory integrace s xMatters OnDemand | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a xMatters OnDemand.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 9e84be9cbc6aa50f4beb3ed333b47459b15fbcc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546277"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Kurz: Azure Active Directory integrace s xMatters OnDemand

V tomto kurzu se dozvíte, jak integrovat xMatters OnDemand s Azure Active Directory (Azure AD).
Integrace xMatters OnDemand s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k xMatters OnDemand.
* Uživatelům můžete povolit, aby se automaticky přihlásili k xMatters OnDemand (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Pokud chcete nakonfigurovat integraci Azure AD s xMatters OnDemand, budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* předplatné xMatters OnDemand Single Sign-on Enabled

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* xMatters OnDemand podporuje **IDP** , iniciované jednotné přihlašování

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Přidání xMatters OnDemand z Galerie

Pokud chcete nakonfigurovat integraci xMatters OnDemand do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat xMatters OnDemand z galerie.

**Pokud chcete přidat xMatters OnDemand z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **XMatters OnDemand**, z panelu výsledků vyberte **xMatters OnDemand** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![xMatters OnDemand v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí xMatters OnDemand na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v xMatters OnDemand.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí xMatters OnDemand, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte XMatters OnDemand Single Sign](#configure-xmatters-ondemand-single-sign-on)** -pro konfiguraci nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření XMatters OnDemand Test User](#create-xmatters-ondemand-test-user)** – Pokud chcete mít protějšek Britta Simon v xMatters OnDemand, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí xMatters OnDemand, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **XMatters OnDemand** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML proveďte následující kroky:

    ![informace o jednotném přihlašování xMatters OnDemand Domain a adres URL](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    - `https://<companyname>.au1.xmatters.com.au/`
    - `https://<companyname>.cs1.xmatters.com/`
    - `https://<companyname>.xmatters.com/`
    - `https://www.xmatters.com`
    - `https://<companyname>.xmatters.com.au/`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:

    - `https://<companyname>.au1.xmatters.com.au`
    - `https://<companyname>.xmatters.com/sp/<instancename>`
    - `https://<companyname>.cs1.xmatters.com/sp/<instancename>`
    - `https://<companyname>.au1.xmatters.com.au/<instancename>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, kontaktujte [tým podpory XMatters OnDemand Client](https://www.xmatters.com/company/contact-us/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

    > [!IMPORTANT]
    > Je nutné předaný certifikát do [týmu XMatters OnDemand support](https://www.xmatters.com/company/contact-us/). Aby bylo možné dokončit konfiguraci jednotného přihlašování, je nutné, aby byl certifikát odeslán týmem podpory xMatters.

6. V části **nastavit XMatters OnDemand** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-xmatters-ondemand-single-sign-on"></a>Konfigurace xMatters OnDemand Single Sign-On

1. V jiném okně webového prohlížeče se přihlaste k webu XMatters OnDemand Company jako správce.

2. Na panelu nástrojů v horní části klikněte na **správce**a potom v navigačním panelu na levé straně klikněte na **Podrobnosti společnosti** .

    ![Správce](./media/xmatters-ondemand-tutorial/IC776795.png "Správce")

3. Na stránce **Konfigurace SAML** proveďte následující kroky:

    ![Konfigurace SAML](./media/xmatters-ondemand-tutorial/IC776796.png "Konfigurace SAML")

    a. Vyberte **Povolit SAML**.

    b. Do textového pole **ID zprostředkovatele identity** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **Adresa URL jednotného přihlašování** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    d. Do textového pole **Adresa URL jednotného** odhlašování vložte **adresu URL**pro odhlášení, kterou jste zkopírovali z Azure Portal.

    e. Na stránce s informacemi o společnosti v horní části klikněte na **Uložit změny**.

    ![Podrobné informace o společnosti](./media/xmatters-ondemand-tutorial/IC776797.png "Podrobné informace o společnosti")

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte brittasimon@yourcompanydomain.extension . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k xMatters OnDemand.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte **xMatters OnDemand**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **XMatters OnDemand**.

    ![Odkaz xMatters OnDemand v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-xmatters-ondemand-test-user"></a>Vytvořit uživatele xMatters OnDemand test

Cílem této části je vytvořit uživatele s názvem Britta Simon v xMatters OnDemand.

**Pokud potřebujete ručně vytvořit uživatele, proveďte následující kroky:**

1. Přihlaste se ke klientovi **XMatters OnDemand** .

2. Klikněte na kartu **Uživatelé** a pak klikněte na **Přidat uživatele**.

    ![Uživatelé](./media/xmatters-ondemand-tutorial/IC781048.png "Uživatelé")

3. V části **Přidat uživatele** proveďte následující kroky:

    ![Přidat uživatele](./media/xmatters-ondemand-tutorial/IC781049.png "Přidat uživatele")

    a. Vyberte **aktivní**.

    b. Do textového pole **ID uživatele** zadejte ID uživatele jako Brittasimon@contoso.com .

    c. Do textového pole **jméno** zadejte jméno uživatele, jako je Britta.

    d. Do textového pole **příjmení** zadejte příjmení uživatele, jako je Simon.

    e. Do textového pole **Web** zadejte platný web platného účtu Azure AD, který chcete zřídit.

    f. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici xMatters OnDemand na přístupovém panelu, měli byste se automaticky přihlásit k xMatters OnDemand, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

