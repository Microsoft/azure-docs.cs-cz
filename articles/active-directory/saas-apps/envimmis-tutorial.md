---
title: 'Kurz: Azure Active Directory integrace s ENVI MMIS | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ENVI MMIS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/06/2019
ms.author: jeedes
ms.openlocfilehash: 1eb408025a45f09a6bdb854f4379a560842ef2d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826274"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Kurz: Azure Active Directory integrace s ENVI MMIS

V tomto kurzu se dozvíte, jak integrovat ENVI MMIS s Azure Active Directory (Azure AD).
Integrace ENVI MMIS se službou Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, kteří mají přístup k ENVI MMIS.
* Uživatelům můžete povolit, aby se automaticky přihlásili k ENVI MMIS (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s ENVI MMIS potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné ENVI MMIS s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* ENVI MMIS podporuje **SP** a **IDP** iniciované jednotné přihlašování.

## <a name="adding-envi-mmis-from-the-gallery"></a>Přidání ENVI MMIS z Galerie

Pokud chcete nakonfigurovat integraci ENVI MMIS do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat ENVI MMIS z galerie.

**Pokud chcete přidat ENVI MMIS z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ENVI MMIS**, vyberte **ENVI MMIS** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![ENVI MMIS v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí ENVI MMIS na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v ENVI MMIS.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ENVI MMIS, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování ENVI MMIS](#configure-envi-mmis-single-sign-on)** , abyste na straně aplikace nakonfigurovali nastavení jednoho Sign-On.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte ENVI MMIS Test User](#create-envi-mmis-test-user)** – abyste měli protějšek Britta Simon v ENVI MMIS, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí ENVI MMIS, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **MMIS pro ENVI** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na **Upravit** ikona a otevřete DIALOGOVÉ okno **základní konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Snímek obrazovky zobrazující konfiguraci "základní S A M L" se zvýrazněným tlačítkem "identifikátor", "odpovědět U R L" a "Uložit".](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://www.<CUSTOMER DOMAIN>.com/Account`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![ENVI MMIS, informace o jednotném přihlašování v doméně a adresách URL](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://www.<CUSTOMER DOMAIN>.com/Account`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta podpory ENVI MMIS](mailto:support@ioscorp.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

7. V části **Nastavení MMIS ENVI** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-envi-mmis-single-sign-on"></a>Konfigurace ENVI MMIS Single Sign-On

1. V jiném okně webového prohlížeče se přihlaste k webu ENVI MMIS jako správce.

2. Klikněte na kartu **moje doména** .

    ![Snímek obrazovky s vybraným názvem moje doména v nabídce uživatele](./media/envimmis-tutorial/configure1.png)

3. Klikněte na **Upravit**.

    ![Snímek obrazovky, který zobrazuje vybrané tlačítko pro úpravy](./media/envimmis-tutorial/configure2.png)

4. Zaškrtněte políčko **použít vzdálené ověřování** a potom v rozevíracím seznamu **typ ověřování** vyberte **http přesměrování** .

    ![Snímek obrazovky, který zobrazuje kartu Podrobnosti s zaškrtnutým políčkem použít vzdálené ověřování a je zaškrtnuté políčko "H T T P redirect".](./media/envimmis-tutorial/configure3.png)

5. Vyberte kartu **prostředky** a pak klikněte na **Odeslat metadata**.

    ![Snímek obrazovky zobrazující kartu prostředky s vybranou akcí odeslat metadata](./media/envimmis-tutorial/configure4.png)

6. V překryvném okně **nahrát metadata** proveďte následující kroky:

    ![Snímek obrazovky, který zobrazuje automaticky otevírané okno pro nahrávání metadat se zvolenou možností "soubor" a ikona zvolit soubor a tlačítko OK se zvýrazní.](./media/envimmis-tutorial/configure5.png)

    a. V rozevíracím seznamu **Odeslat z** vyberte možnost **soubor** .

    b. Nahrajte stažený soubor metadat z Azure Portal tím, že vyberete **ikonu zvolit soubor**.

    c. Klikněte na **OK**.

7. Po nahrání staženého souboru metadat se pole vyplní automaticky. Kliknout na **aktualizovat**

    ![Tlačítko pro konfiguraci jednoho Sign-On Uložit](./media/envimmis-tutorial/configure6.png)

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k ENVI MMIS.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **ENVI MMIS**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **ENVI MMIS**.

    ![Odkaz ENVI MMIS v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu Uživatelé položku **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-envi-mmis-test-user"></a>Vytvořit testovacího uživatele ENVI MMIS

Aby se uživatelé Azure AD mohli přihlašovat k ENVI MMIS, musí se zřídit v ENVI MMIS. V případě ENVI MMIS je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu společnosti ENVI MMIS jako správce.

2. Klikněte na kartu **seznam uživatelů** .

    ![Snímek obrazovky, který zobrazuje nabídku uživatel se zvoleným seznamem uživatelů](./media/envimmis-tutorial/user1.png)

3. Klikněte na tlačítko **Přidat uživatele** .

    ![Snímek obrazovky, který zobrazuje oddíl "uživatelé" s vybraným tlačítkem Přidat uživatele.](./media/envimmis-tutorial/user2.png)

4. V části **Přidat uživatele** proveďte následující kroky:

    ![Přidat zaměstnance](./media/envimmis-tutorial/user3.png)

    a. Do textového pole **uživatelské jméno** zadejte uživatelské jméno účtu Britta Simon, jako je **brittasimon \@ contoso.com**.
    
    b. Do textového pole **název** zadejte jméno BrittaSimon jako **Britta**.

    c. Do textového pole **příjmení** zadejte příjmení BrittaSimon jako **Simon**.

    d. Do **nadpisu** textového pole zadejte název uživatele.
    
    e. Do textového pole **e-mailová adresa** zadejte e-mailovou adresu účtu Britta Simon, jako je **brittasimon \@ contoso.com**.

    f. Do textového pole **uživatelské jméno jednotného přihlašování** zadejte uživatelské jméno účtu Britta Simon, jako je **brittasimon \@ contoso.com**.

    například Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici ENVI MMIS, měli byste být automaticky přihlášeni k ENVI MMIS, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

