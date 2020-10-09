---
title: 'Kurz: Integrace Azure Active Directory s malými vylepšeními | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a malými vylepšeními.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: c659af83034a2406449e586723341e1ae535934f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88525357"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Kurz: Integrace Azure Active Directory s malými vylepšeními

V tomto kurzu se naučíte integrovat malá vylepšení pomocí Azure Active Directory (Azure AD).
Integrace malých vylepšení s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k malým vylepšením.
* Uživatelům můžete povolit, aby se automaticky přihlásili k malým vylepšením (jednotné přihlašování) s účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s malými vylepšeními potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Nízká vylepšení jednotného přihlašování s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Malá vylepšení podporují jednotné přihlašování se spuštěným **SP**

## <a name="adding-small-improvements-from-the-gallery"></a>Přidání malých vylepšení z Galerie

Pokud chcete nakonfigurovat integraci malých vylepšení do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat malé vylepšení z galerie.

**Chcete-li přidat malé vylepšení z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **malá vylepšení**, v panelu výsledek vyberte **malá vylepšení** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Malá vylepšení v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s malými vylepšeními na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, je potřeba zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v malých vylepšeních.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s malými vylepšeními, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Konfigurace malých vylepšení jednotného přihlašování](#configure-small-improvements-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvářejte malými vylepšeními testovacího uživatele](#create-small-improvements-test-user)** , abyste měli protějšek Britta Simon v malých vylepšeních, která jsou propojená s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD s malými vylepšeními, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **malá vylepšení** integrace aplikace vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Malá vylepšení informace o jednotném přihlašování v doméně a adresách URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.small-improvements.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.small-improvements.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, obraťte se na [malou vylepšení týmu podpory pro klienty](mailto:support@small-improvements.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavení malých vylepšení** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-small-improvements-single-sign-on"></a>Konfigurace malých vylepšení jednoho Sign-On

1. V jiném okně prohlížeče se přihlaste k malému vylepšení webu společnosti jako správce.

1. Na hlavní stránce řídicího panelu klikněte na tlačítko **Správa** na levé straně.

    ![Konfigurace jednoho Sign-On](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Klikněte na tlačítko **jednotného přihlašování SAML** z části **integrace** .

    ![Konfigurace jednoho Sign-On](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. Na stránce nastavení jednotného přihlašování proveďte následující kroky:

    ![Konfigurace jednoho Sign-On](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. Do textového pole **koncového bodu http** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.

    b. Otevřete stažený certifikát v programu Poznámkový blok, zkopírujte obsah a vložte ho do textového pole **certifikátu x509** . 

    c. Pokud chcete, aby uživatelé měli možnost jednotného přihlašování a ověřování formuláře k dispozici pro uživatele, zaškrtněte možnost **Povolit přístup buď pomocí možnosti přihlašovací jméno nebo heslo** .  

    d. Do textového pole **výzvy SAML** zadejte odpovídající hodnotu pro pojmenování přihlašovacího tlačítka jednotného přihlašování.  

    e. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole typ **uživatelského jména****brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k malým vylepšením.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **malá vylepšení**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **malá vylepšení**.

    ![Odkazy na malými vylepšeními v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-small-improvements-test-user"></a>Vytvořit nízká vylepšení testovacího uživatele

Aby se uživatelé Azure AD mohli přihlašovat k malým vylepšením, musí být zřízené v malých vylepšeních. V případě malých vylepšení je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k malému vylepšení webu společnosti jako správce.

1. Z domovské stránky přejděte do nabídky na levé straně a klikněte na **Správa**.

1. Klikněte na tlačítko **adresář uživatele** v části Správa uživatelů.

    ![Vytvoření testovacího uživatele Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Klikněte na **Přidat uživatele**.

    ![Vytvoření testovacího uživatele Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. V dialogovém okně **Přidat uživatele** proveďte následující kroky: 

    ![Vytvoření testovacího uživatele Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)

    a. Zadejte jméno **uživatele, jako je** například **Britta**.

    b. Zadejte **příjmení** uživatele, jako je **Simon**.

    c. Zadejte **e-mail** uživatele jako **brittasimon@contoso.com** .

    d. Do pole **Odeslat e-mail s oznámením** si můžete také zadat osobní zprávu. Pokud oznámení nechcete odesílat, zrušte zaškrtnutí tohoto políčka.

    e. Klikněte na tlačítko **vytvořit uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici malého vylepšení na přístupovém panelu byste měli být automaticky přihlášeni k malým vylepšením, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)