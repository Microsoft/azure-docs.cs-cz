---
title: 'Kurz: Azure Active Directory integrace s SilkRoad Life Suite | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SilkRoad životní sadou.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 49dd333454f0eb92f5fb0dddc40390ec1baa91c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88525441"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Kurz: Azure Active Directory integrace se sadou SilkRoad Life Suite

V tomto kurzu se naučíte integrovat sadu SilkRoad Life Suite s Azure Active Directory (Azure AD).
Integrace SilkRoad Life Suite s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k SilkRoad životní sadě.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k SilkRoad životní sadě (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD se sadou SilkRoad Life potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povoleným jednotným přihlašováním pro SilkRoad životní sadu

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Sada SilkRoad Life podporuje jednotné přihlašování (SSO) iniciované **SP**

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Přidání SilkRoad Life Suite z Galerie

Pokud chcete nakonfigurovat integraci SilkRoad Life Suite do Azure AD, musíte z Galerie přidat SilkRoad Life Suite do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete do galerie přidat SilkRoad Life Suite, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SilkRoad Life Suite**, vyberte **SilkRoad Life Suite** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![SilkRoad Life Suite v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí SilkRoad Life Suite na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být navázán odkaz na odkaz mezi uživatelem služby Azure AD a souvisejícím uživatelem v sadě SilkRoad Life Suite.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí SilkRoad Life Suite, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování pro sadu SilkRoad Life Suite](#configure-silkroad-life-suite-single-sign-on)** a nakonfigurujte jedno Sign-On nastavení na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele SilkRoad Life Suite](#create-silkroad-life-suite-test-user)** – abyste měli protějšek Britta Simon v sadě Silkroad, která je propojená s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí sady SilkRoad Life Suite, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **SilkRoad Life Suite** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud máte **soubor metadat poskytovatele služeb**v **základní části Konfigurace SAML** , proveďte následující kroky:

    > [!NOTE]
    > **Soubor metadat poskytovatele služeb** se vysvětluje dále v tomto kurzu.

    a. Klikněte na **nahrát soubor metadat**.

    ![image](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.

    ![image](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat se hodnoty **adresy URL** **identifikátoru** a odpovědi získají automaticky v základní části Konfigurace SAML:

    ![image](common/sp-identifier-reply.png)

    > [!Note]
    > Pokud hodnoty **adresy URL** pro **identifikátor** a odpověď nezískávají automaticky polulated, zadejte je ručně podle vašich požadavků.

    d. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.silkroad-eng.com/Authentication/`

5. Pokud nemáte **soubor metadat poskytovatele služeb**v **základní části Konfigurace SAML** , proveďte následující kroky:

    ![Informace o jednotném přihlašování pro doménu SilkRoad Life Suite a adresy URL](common/sp-identifier-reply.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. Do pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    - `https://<subdomain>.silkroad-eng.com/Authentication/SP`
    - `https://<subdomain>.silkroad.com/Authentication/SP`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:

    - `https://<subdomain>.silkroad-eng.com/Authentication/`
    - `https://<subdomain>.silkroad.com/Authentication/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným Sign-On URL, identifikátor a adresu URL odpovědi. Pokud chcete získat tyto hodnoty, kontaktujte [tým podpory SilkRoad Life Suite](https://www.silkroad.com/locations/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

7. V části **Nastavení životního cyklu Silkroad** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-silkroad-life-suite-single-sign-on"></a>Konfigurace SilkRoad sady Life Suite Single Sign-On

1. Přihlaste se k webu SilkRoad společnosti jako správce.

    > [!NOTE]
    > Pokud chcete získat přístup k aplikaci SilkRoad Life Suite Authentication pro konfiguraci federace s Microsoft Azure AD, obraťte se prosím na podporu SilkRoad nebo svého zástupce SilkRoad služeb.

1. Přejděte na **poskytovatel služeb**a potom klikněte na **Podrobnosti federace**.

    ![Služba Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png)

1. Klikněte na **Stáhnout federační metadata**a uložte soubor metadat do svého počítače. Použijte stažená metadata federace jako **soubor metadat poskytovatele služeb** v **základní části konfigurace SAML** v Azure Portal.

    ![Služba Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png)

1. V aplikaci **Silkroad** klikněte na **zdroje ověřování**.

    ![Služba Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png) 

1. Klikněte na **Přidat zdroj ověřování**.

    ![Služba Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png)

1. V části **Přidat zdroj ověřování** proveďte následující kroky:

    ![Služba Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png)
  
    a. V části **možnost 2 – soubor metadat**klikněte na **Procházet** a nahrajte stažený soubor metadat z Azure Portal.
  
    b. Klikněte na **vytvořit zprostředkovatele identity pomocí souborů data**.

1. V části **zdroje ověřování** klikněte na **Upravit**.

    ![Služba Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png)

1. V dialogu **Upravit zdroj ověřování** proveďte následující kroky:

    ![Služba Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png)

    a. Jako **povolené**vyberte **Ano**.

    b. Do textového pole **EntityId** vložte hodnotu **identifikátoru služby Azure AD** , který jste zkopírovali z Azure Portal.

    c. Do textového pole **popis IDP** zadejte popis konfigurace (například: *Azure AD SSO*).

    d. V textovém poli **souboru metadat** nahrajte soubor **metadat** , který jste stáhli z Azure Portal.
  
    e. Do textového pole **název IDP** zadejte název, který je specifický pro vaši konfiguraci (například: *Azure SP*).
  
    f. Do textového pole **Adresa URL odhlašovací služby** vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    například Do textového pole **Adresa URL přihlašovací služby** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    h. Klikněte na **Uložit**.

1. Zakažte všechny ostatní zdroje ověřování.

    ![Služba Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole typ **uživatelského jména**`brittasimon@yourcompanydomain.extension`  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k životní sadě SilkRoad.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **SilkRoad Life Suite**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **SilkRoad Life Suite**.

    ![Odkaz na životní sadu SilkRoad v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-silkroad-life-suite-test-user"></a>Vytvořit testovacího uživatele SilkRoad Life Suite

V této části vytvoříte uživatele s názvem Britta Simon v SilkRoad životní sadě. Pokud chcete přidat uživatele do platformy SilkRoad Life Suite, pracujte s [týmem podpory klientů pro životní sadu Silkroad](https://www.silkroad.com/locations/) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici SilkRoad Life Suite na přístupovém panelu, měli byste se automaticky přihlásit k životní sadě SilkRoad, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
